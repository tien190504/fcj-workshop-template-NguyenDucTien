---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Tìm hiểu **AWS Transit Gateway (TGW)** — trung tâm định tuyến (network transit hub) kết nối nhiều VPC lại với nhau, thay cho mô hình peering từng cặp ở tuần trước.
* Dùng **CloudFormation** dựng môi trường lab `Lab20-Stack` gồm nhiều VPC và các EC2 host để kiểm thử.
* Chuẩn bị kết nối tới các host bằng key pair `tgw-key.pem`.
* Tạo Transit Gateway `lab20-tgw` và gắn (attach) nhiều VPC vào TGW.
* Tạo Transit Gateway route table `lab20-TGW-RT` và associate các attachment để định tuyến tập trung.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Tìm hiểu tổng quan Transit Gateway <br> - Dùng CloudFormation dựng môi trường lab `Lab20-Stack` (nhiều VPC + EC2 host) | 08/06/2026 | 08/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Chuẩn bị key pair `tgw-key.pem` <br> - Dùng `scp` copy key tới các EC2 host để chuẩn bị kết nối chuyển tiếp | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tạo Transit Gateway `lab20-tgw` <br> - Kiểm tra trạng thái khởi tạo của TGW | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tạo Transit Gateway VPC attachment đầu tiên <br> - Gắn thêm các VPC còn lại vào TGW (tổng cộng 4 attachment) | 11/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Tạo Transit Gateway route table `lab20-TGW-RT` <br> - Associate 4 attachment vào route table để định tuyến tập trung | 12/06/2026 | 12/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Dựng môi trường lab bằng CloudFormation

Đầu tuần, tôi dùng **CloudFormation** để dựng môi trường thực hành cho bài lab Transit Gateway. Stack `Lab20-Stack` chạy tại Region `ap-southeast-1` hoàn tất với trạng thái **CREATE_COMPLETE**; tab Events có hơn 100 sự kiện tạo tài nguyên (trong đó có host `FirstHost`). Cùng lúc, stack `My-VPC-Stack` của bài lab tuần trước đang được dọn dẹp (**DELETE_IN_PROGRESS**) để giải phóng tài nguyên trước khi bắt đầu bài mới.

> **Ảnh minh chứng:**
> ![Stack Lab20-Stack tạo thành công CREATE_COMPLETE, My-VPC-Stack đang được xóa](/images/1.8-Week8/deploy-lab-stack.png)

#### Thứ 3 — Chuẩn bị kết nối tới các host bằng key pair

Để làm việc với các EC2 host trong nhiều VPC, tôi cần đưa key pair `tgw-key.pem` lên host trung gian. Từ cmd.exe, tôi chạy `scp -i tgw-key.pem tgw-key.pem ec2-user@54.179.195.54:` và tương tự tới host `13.212.12.165`. Tuy nhiên cả hai lần đều báo **Host key verification failed / Connection closed** — do host mới chưa có trong `known_hosts` và lời nhắc xác thực fingerprint chưa được trả lời đúng. Danh sách EC2 phía sau cho thấy các instance vẫn đang **Initializing** (private IP ví dụ `172.16.3.12`).

> **Ảnh minh chứng:**
> ![Dùng scp copy tgw-key.pem tới các host nhưng gặp lỗi Host key verification failed](/images/1.8-Week8/scp-key-host-verification-failed.png)

#### Thứ 4 — Tạo Transit Gateway

Sau khi hạ tầng sẵn sàng, tôi tạo **Transit Gateway** làm trung tâm định tuyến. TGW `lab20-tgw` (`tgw-0053b8d202c91b6ba`) được tạo thành công — banner xanh xác nhận "You successfully created … lab20-tgw", trạng thái ban đầu là **Pending** trong khi AWS khởi tạo. Đây sẽ là "hub" để mọi VPC kết nối vào thay vì phải peering từng cặp.

> **Ảnh minh chứng:**
> ![Transit Gateway lab20-tgw được tạo, trạng thái Pending](/images/1.8-Week8/create-transit-gateway.png)

#### Thứ 5 — Gắn các VPC vào Transit Gateway

Tiếp theo, tôi tạo **Transit Gateway attachment** để đưa các VPC vào TGW. Attachment đầu tiên `tgw-attach-09375dbe5a4d001ca` (Resource type **VPC**, `vpc-00b2bcf8f8fa68b5a`) được tạo thành công, gắn vào `lab20-tgw`, trạng thái **Pending**.

> **Ảnh minh chứng:**
> ![Tạo Transit Gateway VPC attachment đầu tiên, trạng thái Pending](/images/1.8-Week8/create-tgw-vpc-attachment.png)

Tôi lặp lại thao tác cho các VPC còn lại. Màn hình Transit gateway attachments hiển thị **4 attachment** (Attachment 1 → 4), tất cả đều trỏ về cùng `tgw-0053b8d202c91b6ba` với Resource type VPC — một attachment đã **Available**, số còn lại đang **Pending**. Như vậy nhiều VPC đã cùng kết nối vào một Transit Gateway duy nhất.

> **Ảnh minh chứng:**
> ![Danh sách 4 Transit Gateway VPC attachment cùng gắn vào lab20-tgw](/images/1.8-Week8/tgw-vpc-attachments-list.png)

#### Thứ 6 — Tạo route table cho Transit Gateway và associate attachment

Ngày cuối tuần, tôi tạo **Transit Gateway route table** để điều khiển việc định tuyến giữa các VPC. Route table `lab20-TGW-RT` (`tgw-rtb-0960e827579c78459`) gắn với `lab20-tgw` được tạo thành công, trạng thái ban đầu **Pending**.

> **Ảnh minh chứng:**
> ![Tạo Transit Gateway route table lab20-TGW-RT, trạng thái Pending](/images/1.8-Week8/create-tgw-route-table.png)

Sau khi route table chuyển sang **Available**, tôi associate các attachment vào nó. Tab Associations của `lab20-TGW-RT` hiển thị **4 association** ứng với 4 VPC attachment — một số ở trạng thái **Associated**, một số đang **Associating**. Đến đây, một Transit Gateway với route table tập trung đã kết nối và điều phối định tuyến cho nhiều VPC cùng lúc.

> **Ảnh minh chứng:**
> ![Route table lab20-TGW-RT Available với 4 association từ các VPC attachment](/images/1.8-Week8/tgw-route-table-associations.png)

### Khó khăn gặp phải:

* **`scp` báo Host key verification failed**: khi copy `tgw-key.pem` tới host mới (`54.179.195.54`, `13.212.12.165`), SSH chưa có fingerprint của host trong `known_hosts` nên yêu cầu xác nhận; do lời nhắc không được trả lời đúng nên kết nối bị đóng (Connection closed).
  * **Cách giải quyết:** trả lời `yes` khi được hỏi xác thực fingerprint (hoặc thêm host vào `known_hosts` trước), đồng thời **chờ instance chuyển hẳn sang Running** (các instance lúc đó vẫn Initializing) rồi mới thực hiện `scp`.
* **Nhiều tài nguyên ở trạng thái Pending/Associating**: TGW, attachment và association đều cần thời gian khởi tạo, không sẵn sàng ngay sau khi tạo.
  * **Cách giải quyết:** kiên nhẫn refresh danh sách và chờ trạng thái chuyển sang Available/Associated trước khi sang bước tiếp theo (ví dụ chỉ associate được khi route table đã Available).
* **Dễ nhầm giữa nhiều VPC/attachment**: có tới 4 VPC attachment cùng gắn vào một TGW với ID gần giống nhau.
  * **Cách giải quyết:** đặt tên rõ ràng cho attachment (Attachment 1 → 4) và đối chiếu theo Transit gateway attachment ID / Resource ID khi thao tác.

### Kết quả đạt được tuần 8:

* Hiểu vai trò của Transit Gateway như một **hub định tuyến trung tâm**, giải quyết hạn chế của mô hình VPC peering từng cặp.
* Dựng được môi trường lab `Lab20-Stack` bằng CloudFormation (nhiều VPC + EC2 host) và dọn dẹp stack cũ trước khi bắt đầu.
* Thực hành copy key pair `tgw-key.pem` tới host bằng `scp` và nhận diện được lỗi Host key verification.
* Tạo được Transit Gateway `lab20-tgw` và gắn **4 VPC attachment** vào cùng một TGW.
* Tạo Transit Gateway route table `lab20-TGW-RT` và associate 4 attachment để định tuyến tập trung.
* Nắm được vòng đời trạng thái của tài nguyên TGW: Pending → Available, Associating → Associated.

### Kiến thức học được:

Tuần 8 mở rộng tư duy mạng của tôi từ "kết nối từng cặp" (VPC Peering ở tuần 7) sang "kết nối tập trung" qua Transit Gateway. Điểm mấu chốt là mô hình **hub-and-spoke**: thay vì tạo N×(N-1)/2 kết nối peering giữa các VPC, chỉ cần gắn mỗi VPC vào TGW một lần rồi để route table của TGW điều phối traffic — quản lý đơn giản và mở rộng dễ hơn nhiều. Tôi cũng hiểu rõ chuỗi thành phần của TGW: **Transit Gateway → VPC Attachment → TGW Route Table → Association**, mỗi bước đều có trạng thái riêng và phải chờ sẵn sàng trước khi làm bước sau. Bên cạnh đó, lỗi Host key verification khi dùng `scp` nhắc tôi rằng thao tác với host mới luôn cần xử lý fingerprint và đảm bảo instance đã thực sự Running.

### Tổng kết tuần:

Tuần 8 khép lại với một mô hình mạng nhiều VPC được kết nối tập trung qua AWS Transit Gateway: hạ tầng dựng tự động bằng CloudFormation `Lab20-Stack`, Transit Gateway `lab20-tgw` làm hub, 4 VPC được attach vào TGW, và route table `lab20-TGW-RT` associate đầy đủ các attachment để điều phối định tuyến. So với VPC Peering tuần trước, Transit Gateway cho thấy cách kết nối nhiều VPC gọn gàng và dễ mở rộng hơn hẳn. Không đưa thông tin nhạy cảm như AWS Account ID, nội dung key `.pem`, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
