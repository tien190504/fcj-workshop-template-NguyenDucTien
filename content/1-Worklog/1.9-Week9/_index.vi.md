---
title: "Tuần 9 - Vận hành máy chủ tập trung với AWS Systems Manager"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

* Tìm hiểu **AWS Systems Manager (SSM)** — bộ công cụ quản lý vận hành tập trung cho các máy chủ (managed nodes).
* Dựng hạ tầng lab: VPC `windows-lab-ssm-vpc` và khởi chạy 2 EC2 instance chạy Windows.
* Tạo IAM Role cấp quyền SSM cho EC2 và gán vào cả hai instance để chúng trở thành managed node.
* Dùng **Fleet Manager** kiểm tra trạng thái node và **Session Manager** kết nối vào Windows mà không cần RDP/SSH.
* Dùng **Patch Manager** quét và cài bản vá cho các instance.
* Dùng **Run Command** chạy lệnh PowerShell từ xa trên nhiều instance cùng lúc.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Tìm hiểu tổng quan Systems Manager <br> - Tạo VPC `windows-lab-ssm-vpc` (`10.0.0.0/16`) <br> - Khởi chạy 2 EC2 Windows (`Windows-Lab-SSM-1`, `Windows-Lab-SSM-2`) | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tạo IAM Role `Windows-Lab-SSM-Role` (trusted entity EC2) <br> - Gán role cho cả hai instance để cấp quyền SSM | 16/06/2026 | 16/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Kiểm tra managed nodes trên Fleet Manager (Ping status Online) <br> - Dùng Session Manager kết nối PowerShell vào cả hai Windows instance | 17/06/2026 | 17/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Dùng Patch Manager quét và cài bản vá cho 2 instance (operation Install) | 18/06/2026 | 18/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Dùng Run Command chạy PowerShell script trên cả hai instance <br> - Xem output trả về của lệnh | 19/06/2026 | 19/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Dựng VPC và khởi chạy hai EC2 Windows

Đầu tuần, tôi chuẩn bị hạ tầng cho bài lab Systems Manager. Trước tiên là VPC `windows-lab-ssm-vpc` (`vpc-0d3e2442f0ee81061`) với dải `10.0.0.0/16`, trạng thái **Available**, gồm 4 subnet và 4 route table — nền tảng mạng cho các máy Windows.

> **Ảnh minh chứng:**
> ![VPC windows-lab-ssm-vpc dải 10.0.0.0/16 ở trạng thái Available với 4 subnet](/images/1.9-Week9/create-windows-lab-vpc.png)

Tiếp theo, tôi khởi chạy instance Windows đầu tiên `Windows-Lab-SSM-1` (`i-0d5de246eb19c0e70`, `t3.micro`) — instance chuyển sang **Running**.

> **Ảnh minh chứng:**
> ![Khởi chạy EC2 Windows-Lab-SSM-1 (t3.micro) ở trạng thái Running](/images/1.9-Week9/launch-first-windows-instance.png)

Tôi khởi chạy thêm instance thứ hai `Windows-Lab-SSM-2` (`i-038c7ec288a048e41`) ở AZ khác (`ap-southeast-1b`). Cả hai instance đều **Running**; `Windows-Lab-SSM-1` có private IP `10.0.0.52`, public IP `13.250.16.49` — hai máy Windows này sẽ là đối tượng quản lý bằng SSM.

> **Ảnh minh chứng:**
> ![Hai instance Windows-Lab-SSM-1 và Windows-Lab-SSM-2 đều ở trạng thái Running](/images/1.9-Week9/launch-second-windows-instance.png)

#### Thứ 3 — Tạo IAM Role cho SSM và gán vào instance

Để EC2 có thể được Systems Manager quản lý, instance cần một **IAM Role** với quyền SSM. Tôi tạo role `Windows-Lab-SSM-Role` với trusted entity là **AWS Service: EC2** — role xuất hiện trong danh sách Roles kèm banner "Role Windows-Lab-SSM-Role created".

> **Ảnh minh chứng:**
> ![IAM Role Windows-Lab-SSM-Role được tạo với trusted entity EC2](/images/1.9-Week9/create-ssm-iam-role.png)

Sau đó tôi gán role này cho từng instance. Với `Windows-Lab-SSM-1` (`i-0d5de246eb19c0e70`), console báo **"Successfully attached Windows-Lab-SSM-Role to instance"**; phần chi tiết cho thấy IAM role đã gắn và instance nằm trong subnet `windows-lab-ssm-subnet-public1-ap-southeast-1a`.

> **Ảnh minh chứng:**
> ![Gán Windows-Lab-SSM-Role thành công cho instance Windows-Lab-SSM-1](/images/1.9-Week9/attach-role-to-instance-1.png)

Tương tự, tôi gán role cho `Windows-Lab-SSM-2` (`i-038c7ec288a048e41`, subnet `windows-lab-ssm-subnet-public2-ap-southeast-1b`) — cũng nhận được thông báo attach thành công. Đến đây cả hai instance đều đã có quyền để kết nối tới Systems Manager.

> **Ảnh minh chứng:**
> ![Gán Windows-Lab-SSM-Role thành công cho instance Windows-Lab-SSM-2](/images/1.9-Week9/attach-role-to-instance-2.png)

#### Thứ 4 — Fleet Manager và Session Manager

Sau khi gán role, tôi mở **Fleet Manager** trong Systems Manager. Mục Managed Nodes hiển thị **2 node** — cả hai instance Windows đều ở trạng thái **Running**, Ping status **Online**, SSM Agent phiên bản `3.3.4515.0`. Điều này xác nhận SSM Agent trên máy đã kết nối thành công về dịch vụ.

> **Ảnh minh chứng:**
> ![Fleet Manager hiển thị 2 managed node Windows đều Online](/images/1.9-Week9/fleet-manager-managed-nodes.png)

Tôi dùng **Session Manager** để kết nối vào `Windows-Lab-SSM-1` — mở được PowerShell ngay trên trình duyệt mà không cần RDP hay mở port. Chạy thử `ipconfig`, kết quả trả về đúng IPv4 `10.0.0.52` và default gateway `10.0.0.1`.

> **Ảnh minh chứng:**
> ![Session Manager kết nối vào Windows-Lab-SSM-1, chạy ipconfig trả về IP 10.0.0.52](/images/1.9-Week9/session-manager-connect-instance-1.png)

Tương tự, tôi mở một session Session Manager tới `Windows-Lab-SSM-2` (`i-038c7ec288a048e41`) và nhận được cửa sổ PowerShell của máy — chứng tỏ cả hai máy đều truy cập được qua Session Manager.

> **Ảnh minh chứng:**
> ![Session Manager kết nối PowerShell vào Windows-Lab-SSM-2](/images/1.9-Week9/session-manager-connect-instance-2.png)

#### Thứ 5 — Patch Manager

Tôi chuyển sang **Patch Manager** để quản lý bản vá. Thực hiện thao tác **Patch now** với operation **Install**, reboot option **NoReboot**, targets là cả hai instance (`i-0d5de246eb19c0e70`, `i-038c7ec288a048e41`). Trang Association execution summary cho kết quả **Status: Success**, phần Summary hiển thị **Success=2** và biểu đồ Scan/Install operation summary hoàn toàn **Succeeded**.

> **Ảnh minh chứng:**
> ![Patch Manager cài bản vá cho 2 instance với kết quả Success=2](/images/1.9-Week9/patch-manager-install-summary.png)

#### Thứ 6 — Run Command chạy PowerShell từ xa

Cuối tuần, tôi dùng **Run Command** để chạy lệnh trên nhiều instance cùng lúc. Command ID `3db39f20-dfd3-45de-9bef-58fd98ed25bd` (document `aws:runPowerShellScript`) được gửi thành công — Overall status **Success**, **2 targets / 2 completed / 0 error / 0 delivery timed out**, cả hai instance đều báo Success.

> **Ảnh minh chứng:**
> ![Run Command chạy trên 2 instance với overall status Success, 2/2 completed](/images/1.9-Week9/run-command-success.png)

Mở output chi tiết trên instance `i-038c7ec288a048e41`, bước `aws:runPowerShellScript` trả về **Response code 0**, phần Output liệt kê các tài khoản người dùng Windows (`Administrator`, `DefaultAccount`, `Guest`, `ssm-user`, `WDAGUtilityAccount`) — dòng cuối có ghi chú "The command completed with one or more errors". Như vậy lệnh vẫn thực thi và trả về dữ liệu, dù có cảnh báo nhỏ ở output.

> **Ảnh minh chứng:**
> ![Output Run Command trên instance liệt kê các tài khoản Windows, response code 0](/images/1.9-Week9/run-command-output.png)

### Khó khăn gặp phải:

* **Instance cần IAM Role thì mới xuất hiện trong Fleet Manager**: khi mới khởi chạy, hai EC2 chưa có quyền nên chưa phải là managed node; SSM Agent không kết nối được về dịch vụ.
  * **Cách giải quyết:** tạo IAM Role `Windows-Lab-SSM-Role` (trusted entity EC2, quyền SSM) và gán cho cả hai instance; sau đó chờ node chuyển sang Ping status **Online** trong Fleet Manager.
* **Run Command báo "completed with one or more errors" dù status Success**: output của lệnh PowerShell có ghi chú lỗi ở cuối dù response code là 0 và overall status là Success.
  * **Cách giải quyết:** đọc kỹ phần Output/Error để phân biệt lỗi thật với cảnh báo phụ; ở đây lệnh vẫn trả về đúng danh sách tài khoản nên kết quả chính vẫn dùng được.
* **Kết nối Windows mà không muốn mở RDP/port**: mở RDP ra Internet tiềm ẩn rủi ro bảo mật.
  * **Cách giải quyết:** dùng **Session Manager** để truy cập PowerShell qua trình duyệt, không cần mở port 3389 hay dùng key — vừa tiện vừa an toàn hơn.

### Kết quả đạt được tuần 9:

* Hiểu vai trò của AWS Systems Manager như một bộ công cụ **quản lý vận hành tập trung** cho nhiều máy chủ.
* Dựng được VPC `windows-lab-ssm-vpc` và khởi chạy 2 EC2 Windows (`Windows-Lab-SSM-1`, `Windows-Lab-SSM-2`).
* Tạo IAM Role `Windows-Lab-SSM-Role` và gán cho cả hai instance để biến chúng thành managed node.
* Xác nhận 2 node **Online** trên Fleet Manager và kết nối được vào Windows bằng Session Manager (không cần RDP/SSH).
* Cài bản vá cho cả hai instance bằng Patch Manager với kết quả **Success=2**.
* Chạy PowerShell script từ xa trên cả hai instance bằng Run Command (2/2 Success) và đọc được output trả về.

### Kiến thức học được:

Tuần 9 cho tôi cái nhìn tổng quan về cách **vận hành đội máy chủ ở quy mô lớn** mà không cần đăng nhập trực tiếp từng máy. Điểm mấu chốt là chuỗi: **IAM Role (quyền SSM) → SSM Agent kết nối → managed node → dùng các công cụ SSM**. Khi instance đã là managed node, tôi có thể: xem tình trạng đội máy bằng Fleet Manager, kết nối an toàn bằng Session Manager (thay cho RDP/SSH, không phải mở port), tự động vá lỗi bằng Patch Manager, và chạy lệnh hàng loạt bằng Run Command. Ấn tượng nhất là Session Manager và Run Command: một cái giúp truy cập máy Windows qua trình duyệt an toàn, một cái giúp thực thi cùng một lệnh trên nhiều máy chỉ trong vài giây — cách tiếp cận này gọn và bảo mật hơn hẳn việc RDP vào từng máy thủ công.

### Tổng kết tuần:

Tuần 9 khép lại với một quy trình quản lý máy chủ Windows hoàn chỉnh bằng AWS Systems Manager: hạ tầng gồm VPC và 2 EC2 Windows, IAM Role cấp quyền để hai máy trở thành managed node, Fleet Manager theo dõi trạng thái, Session Manager kết nối an toàn, Patch Manager vá lỗi và Run Command chạy lệnh hàng loạt — tất cả đều thành công (Success=2 cho cả patch lẫn command). So với việc quản trị từng máy thủ công, Systems Manager cho thấy cách vận hành tập trung, an toàn và dễ mở rộng hơn nhiều. Không đưa thông tin nhạy cảm như AWS Account ID, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
