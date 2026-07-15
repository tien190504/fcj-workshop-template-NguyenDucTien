---
title: "Worklog Tuần 7"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Tìm hiểu **VPC Peering** — cơ chế kết nối riêng tư (private) giữa hai VPC trong AWS.
* Dùng **CloudFormation** dựng hai VPC độc lập: `My-VPC-Stack` (dải `172.31.0.0/16`) và `HG-VPC-Stack` (dải `10.10.0.0/16`).
* Tạo Security Group cho từng VPC (`MY VPC SG`, `HG VPC SG`) mở SSH và ICMP để kiểm thử kết nối.
* Khởi chạy một EC2 instance trong mỗi VPC (`EC2 - My VPC`, `EC2 - HG VPC`).
* Kiểm tra kết nối giữa hai instance qua **public IP** trước khi peering.
* Tạo **VPC peering connection** `lab-vpc-peer` giữa hai VPC và kiểm chứng phân giải DNS/kết nối private sau khi peering.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Tìm hiểu tổng quan VPC Peering <br> - Dùng CloudFormation dựng VPC `My-VPC-Stack` (`172.31.0.0/16`) <br> - Dùng CloudFormation dựng VPC `HG-VPC-Stack` (`10.10.0.0/16`) | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tạo Security Group `MY VPC SG` cho VPC My VPC (mở SSH 22, ICMP) <br> - Tạo Security Group `HG VPC SG` cho VPC HG VPC | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Khởi chạy EC2 `EC2 - My VPC` (`t2.micro`) trong My VPC <br> - Khởi chạy EC2 `EC2 - HG VPC` (`t2.micro`) trong HG VPC | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - SSH vào `EC2 - My VPC` bằng EC2 Instance Connect <br> - Ping tới public IP của `EC2 - HG VPC` để kiểm tra kết nối qua Internet | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Tạo VPC peering connection `lab-vpc-peer` (My VPC ↔ HG VPC) <br> - Chấp nhận (accept) peering connection <br> - Kiểm tra phân giải DNS và kết nối private giữa hai VPC | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Dựng hai VPC bằng CloudFormation

Đầu tuần, tôi dùng **CloudFormation** để dựng hạ tầng mạng cho bài lab VPC Peering. Từ template VPC, tôi tạo stack đầu tiên `My-VPC-Stack` tại Region `ap-southeast-1`. Stack hoàn tất với trạng thái **CREATE_COMPLETE**; tab Events liệt kê 75 sự kiện tạo tài nguyên (VPC, Subnet, Route Table, Internet Gateway, NAT Gateway…) — toàn bộ một VPC dải `172.31.0.0/16` được dựng chỉ bằng một template.

> **Ảnh minh chứng:**
> ![Stack My-VPC-Stack tạo thành công với trạng thái CREATE_COMPLETE](/images/1.7-Week7/deploy-my-vpc-stack.png)

Tiếp theo, tôi dựng VPC thứ hai bằng stack `HG-VPC-Stack` (dùng cùng template nhưng đổi tham số sang dải `10.10.0.0/16`). Sau khi hoàn tất, danh sách Stacks hiển thị **cả hai** stack `HG-VPC-Stack` và `My-VPC-Stack` đều ở trạng thái **CREATE_COMPLETE** — nghĩa là tôi đã có hai VPC độc lập với dải địa chỉ khác nhau, sẵn sàng cho việc peering.

> **Ảnh minh chứng:**
> ![Hai stack HG-VPC-Stack và My-VPC-Stack đều CREATE_COMPLETE](/images/1.7-Week7/deploy-hg-vpc-stack.png)

#### Thứ 3 — Tạo Security Group cho từng VPC

Để hai instance có thể kiểm thử kết nối, tôi tạo Security Group riêng cho mỗi VPC. Trước tiên là `MY VPC SG` (sg-07e7bc4685630cb07) trong VPC My VPC (`vpc-0894fadf5d2a0559c`). Banner xanh xác nhận "Security group … was created successfully", tab Inbound rules có **3 luật**: SSH (TCP 22) để remote và hai luật ICMP (All) để cho phép ping.

> **Ảnh minh chứng:**
> ![Security group MY VPC SG được tạo với luật SSH và ICMP](/images/1.7-Week7/create-my-vpc-security-group.png)

Tương tự, tôi tạo `HG VPC SG` (sg-08d46c738a1e50d94) trong VPC HG VPC (`vpc-080a1fc0b167628ef`). Màn hình Security Groups liệt kê đủ các nhóm: `MY VPC SG` thuộc VPC My VPC và `HG VPC SG` thuộc VPC HG VPC — mỗi VPC giờ đã có một Security Group cho phép SSH và ICMP.

> **Ảnh minh chứng:**
> ![Security group HG VPC SG được tạo, danh sách hiển thị SG của cả hai VPC](/images/1.7-Week7/create-hg-vpc-security-group.png)

#### Thứ 4 — Khởi chạy EC2 instance trong mỗi VPC

Tôi khởi chạy một EC2 instance (`t2.micro`) trong mỗi VPC để làm điểm đầu/cuối kiểm thử kết nối. Danh sách Instances hiển thị hai máy: `EC2 - My VPC` (i-0c92b0698afd409af) ở trạng thái **Running** (2/2 checks passed) và `EC2 - HG VPC` (i-0446a743af0985436) đang **Pending** với private IP `10.10.2.55`, public IP `18.141.137.14` — đúng như thiết kế, mỗi instance nằm trong một VPC với dải địa chỉ riêng.

> **Ảnh minh chứng:**
> ![Hai instance EC2 - My VPC (Running) và EC2 - HG VPC (Pending) trong hai VPC](/images/1.7-Week7/launch-ec2-instances.png)

#### Thứ 5 — Kiểm tra kết nối qua public IP

Trước khi peering, tôi kiểm tra kết nối giữa hai máy qua **Internet (public IP)**. Dùng **EC2 Instance Connect** để SSH vào `EC2 - My VPC` (private IP `172.31.4.77`), rồi chạy `ping 18.141.137.14 -c5` — chính là public IP của `EC2 - HG VPC`. Kết quả: **5 packets transmitted, 5 received, 0% packet loss** — hai máy liên lạc được với nhau qua đường Internet, xác nhận Security Group đã mở ICMP đúng.

> **Ảnh minh chứng:**
> ![Ping public IP của EC2 - HG VPC từ EC2 - My VPC thành công, 0% packet loss](/images/1.7-Week7/ping-public-ip-success.png)

#### Thứ 6 — Tạo VPC Peering và kiểm tra kết nối private

Ngày cuối tuần, tôi thiết lập **VPC peering connection** để hai VPC nói chuyện với nhau qua đường riêng thay vì Internet. Yêu cầu peering `pcx-0404a6e4d3d4368d5` / `lab-vpc-peer` được tạo với **Requester VPC** là My VPC (`172.31.0.0/16`) và **Accepter VPC** là HG VPC; ban đầu trạng thái là **Pending Acceptance**.

> **Ảnh minh chứng:**
> ![VPC peering connection lab-vpc-peer được tạo, trạng thái Pending Acceptance](/images/1.7-Week7/request-vpc-peering.png)

Sau khi bấm chấp nhận (accept) yêu cầu, peering connection chuyển sang trạng thái **Active**. Console hiển thị banner "Your VPC peering connection … has been established" kèm lưu ý quan trọng: để traffic đi được qua peering, **phải thêm route** trỏ sang VPC đối tác trong route table của mỗi VPC.

> **Ảnh minh chứng:**
> ![Peering connection lab-vpc-peer chuyển sang trạng thái Active](/images/1.7-Week7/vpc-peering-active.png)

Cuối cùng, tôi kiểm tra kết nối **private** qua peering. Từ `EC2 - My VPC`, tôi ping bằng **public DNS** của instance HG VPC: `ping ec2-18-141-137-14.ap-southeast-1.compute.amazonaws.com -c5`. Điểm thú vị: sau khi peering, tên miền này phân giải về **private IP `10.10.2.55`** (thay vì public IP) — chứng tỏ DNS resolution qua peering đã hoạt động. Tuy nhiên ping trả về **100% packet loss** vì lúc này route table và Security Group chưa được cấu hình cho traffic private xuyên VPC — đúng như cảnh báo "phải thêm route" ở bước trước.

> **Ảnh minh chứng:**
> ![Ping qua DNS phân giải về private IP 10.10.2.55 nhưng 100% packet loss do chưa cấu hình route](/images/1.7-Week7/ping-private-ip-failed.png)

### Kết quả đạt được tuần 7:

* Hiểu khái niệm VPC Peering: kết nối riêng tư giữa hai VPC, khác với đi qua Internet bằng public IP.
* Dựng được hai VPC độc lập bằng CloudFormation: `My-VPC-Stack` (`172.31.0.0/16`) và `HG-VPC-Stack` (`10.10.0.0/16`).
* Tạo Security Group `MY VPC SG` và `HG VPC SG` cho từng VPC với luật SSH (22) và ICMP để kiểm thử.
* Khởi chạy EC2 instance trong mỗi VPC (`EC2 - My VPC`, `EC2 - HG VPC`).
* Kiểm chứng kết nối qua public IP giữa hai instance thành công (ping 0% packet loss).
* Tạo và chấp nhận VPC peering connection `lab-vpc-peer` (My VPC ↔ HG VPC), đưa trạng thái về Active.
* Quan sát được hiện tượng DNS resolution qua peering: public DNS phân giải về private IP của máy đích, đồng thời hiểu rằng peering active vẫn cần thêm route và mở Security Group thì traffic private mới thông.

### Khó khăn gặp phải:

* **Peering active nhưng ping private vẫn 100% packet loss**: sau khi peering ở trạng thái Active, ping qua DNS (phân giải về private IP `10.10.2.55`) vẫn thất bại vì chưa thêm route trỏ sang VPC đối tác trong route table và chưa mở Security Group cho dải private của VPC kia — console có cảnh báo "must add a route to the peered VPC" nhưng dễ bị bỏ qua.
* **Phân biệt public IP và private IP khi ping**: cùng một máy đích, ping public IP (`18.141.137.14`) thì thành công (đi qua Internet), còn ping qua DNS (phân giải về private IP sau peering) lại thất bại — cần hiểu rõ đường đi của traffic để không nhầm lẫn khi chẩn đoán.
* **Quản lý hai VPC cùng lúc**: mỗi thao tác (Security Group, EC2, route) đều phải chọn đúng VPC/dải địa chỉ tương ứng, dễ nhầm giữa `172.31.0.0/16` (My VPC) và `10.10.0.0/16` (HG VPC).

### Bài học rút ra:

Tuần 7 giúp tôi hiểu rõ sự khác nhau giữa kết nối **public** (qua Internet Gateway, dùng public IP) và kết nối **private** (qua VPC Peering, dùng private IP). Điều ấn tượng nhất là chuỗi: dựng hạ tầng hai VPC bằng CloudFormation → tạo Security Group → khởi chạy EC2 → kiểm thử kết nối. Việc peering chuyển sang Active nhưng ping private vẫn thất bại là một bài học đắt giá: **peering chỉ mở "đường", còn muốn xe chạy được thì phải có "biển chỉ đường" (route table) và "cổng cho qua" (Security Group)**. Chi tiết public DNS tự phân giải về private IP sau khi peering cũng cho thấy AWS xử lý DNS resolution rất thông minh trong môi trường peered VPC. Nếu làm tiếp, tôi sẽ thêm route hai chiều và mở Security Group cho dải đối tác để hoàn tất kết nối private.

### Kết luận:

Tuần 7 khép lại với một mô hình hai VPC được kết nối qua VPC Peering: hạ tầng dựng tự động bằng CloudFormation, mỗi VPC có Security Group và EC2 riêng, kết nối public được kiểm chứng thành công, và peering connection `lab-vpc-peer` đã ở trạng thái Active. Bước còn thiếu để thông kết nối private (thêm route + mở Security Group) đã được nhận diện rõ qua thử nghiệm ping. Không đưa thông tin nhạy cảm như AWS Account ID, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
