---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

* Bước vào giai đoạn cuối của kỳ thực tập: **hoàn thiện và tích hợp** các mảnh rời rạc đã làm suốt 9 tuần thành một hệ thống thống nhất.
* Rà soát và lập danh mục (inventory) toàn bộ tài nguyên đã tạo qua các tuần; vẽ lại sơ đồ kiến trúc tổng thể.
* Chuẩn hóa hạ tầng bằng **Infrastructure as Code (CloudFormation)** và quy ước đặt tên/tag nhất quán.
* Tích hợp: triển khai lại ứng dụng **AWS FCJ Management** (EC2 + RDS) trong VPC chuẩn với Security Group đúng vai trò.
* Gắn giám sát **CloudWatch** (dashboard, alarm + SNS) và đưa instance vào quản lý bằng **Systems Manager**.
* Bật **AWS Backup** cho EC2/RDS theo backup plan và kiểm thử end-to-end toàn bộ hệ thống tích hợp.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Rà soát toàn bộ tài nguyên đã tạo từ Tuần 1–9 <br> - Lập danh mục (inventory) VPC, EC2, RDS, S3, IAM… <br> - Vẽ lại sơ đồ kiến trúc tổng thể của hệ thống | 22/06/2026 | 22/06/2026 | Tổng hợp nội bộ |
| 3   | - Chuẩn hóa hạ tầng bằng CloudFormation template <br> - Áp dụng quy ước đặt tên và gắn tag nhất quán cho tài nguyên | 23/06/2026 | 23/06/2026 | Tổng hợp nội bộ |
| 4   | - Triển khai lại ứng dụng AWS FCJ Management trên EC2 trong VPC chuẩn <br> - Kết nối ứng dụng với cơ sở dữ liệu RDS <br> - Gắn Security Group đúng vai trò app/db | 24/06/2026 | 24/06/2026 | Tổng hợp nội bộ |
| 5   | - Tạo CloudWatch dashboard và alarm cho EC2/RDS <br> - Gắn hành động thông báo qua SNS <br> - Đưa EC2 vào Systems Manager (managed node) | 25/06/2026 | 25/06/2026 | Tổng hợp nội bộ |
| 6   | - Cấu hình AWS Backup plan cho EC2/RDS <br> - Kiểm thử end-to-end toàn bộ luồng hệ thống tích hợp | 26/06/2026 | 26/06/2026 | Tổng hợp nội bộ |

### Nội dung thực hiện chi tiết:

> **Ghi chú:** Tuần này tập trung vào rà soát, tích hợp và chuẩn hóa hạ tầng đã có nên không có hình minh họa cho tuần này.

#### Thứ 2 — Rà soát tài nguyên và vẽ lại kiến trúc tổng thể

Đầu tuần, tôi dành thời gian nhìn lại toàn bộ hành trình 9 tuần thay vì tạo tài nguyên mới. Tôi rà soát và lập danh mục những gì đã dựng: các VPC (`ASG`, `Windows-vpc`, `Linux-vpc`, `windows-lab-ssm-vpc`…), các EC2 instance, RDS `first-cloud-db-instance`, các S3 bucket, IAM user/role, cùng những thành phần mạng nâng cao như VPC Peering và Transit Gateway. Việc liệt kê thành một bảng inventory giúp tôi thấy rõ tài nguyên nào còn dùng, tài nguyên nào là "rác" của các bài lab cũ. Từ danh mục đó, tôi vẽ lại **sơ đồ kiến trúc tổng thể**: một VPC chuẩn chứa ứng dụng AWS FCJ Management (EC2) và cơ sở dữ liệu (RDS), được giám sát bởi CloudWatch, sao lưu bởi AWS Backup và quản lý bởi Systems Manager.

#### Thứ 3 — Chuẩn hóa hạ tầng bằng Infrastructure as Code

Từ Tuần 5 trở đi tôi đã dùng CloudFormation để dựng hạ tầng, nhưng nhiều tài nguyên các tuần đầu vẫn tạo tay trên Console. Ngày này, tôi gom các bước tạo tay thường lặp lại (VPC, Subnet, Route Table, Security Group) vào **CloudFormation template** để có thể tái tạo hạ tầng nhất quán. Tôi cũng thống nhất **quy ước đặt tên** và **gắn tag** (ví dụ `Project`, `Environment`, `Owner`) cho tài nguyên — điều mà những tuần trước làm rời rạc khiến việc tra cứu khó khăn. Đây là bước biến kinh nghiệm rải rác thành một bộ khuôn có thể lặp lại.

#### Thứ 4 — Tích hợp ứng dụng AWS FCJ Management với RDS trong VPC chuẩn

Tôi triển khai lại ứng dụng **AWS FCJ Management** (đã làm quen từ Tuần 3 và Tuần 4) lên một EC2 instance nằm trong VPC chuẩn, và kết nối ứng dụng tới cơ sở dữ liệu **RDS** thay vì database cục bộ. Security Group được gắn đúng theo mô hình phân tầng đã học ở Tuần 2–4: tầng ứng dụng mở cổng web và cổng 5000, tầng database chỉ nhận kết nối MySQL (3306) từ Security Group của tầng ứng dụng. Kiểm thử nhanh luồng CRUD user (List/Add/Edit) cho thấy ứng dụng đọc/ghi đúng dữ liệu từ RDS.

#### Thứ 5 — Gắn giám sát CloudWatch và đưa vào Systems Manager

Để hệ thống "quan sát được", tôi dựng một **CloudWatch dashboard** tổng hợp các metric quan trọng của EC2 (CPU) và RDS, đồng thời tạo **alarm** cho các ngưỡng rủi ro và gắn hành động **SNS** để nhận cảnh báo qua email — tái sử dụng đúng chuỗi log/metric/alarm/SNS đã dựng ở Tuần 5. Sau đó tôi gán IAM Role phù hợp để đưa EC2 vào **Systems Manager** như một managed node (kỹ thuật từ Tuần 9), giúp về sau có thể vá lỗi và chạy lệnh từ xa mà không cần mở RDP/SSH.

#### Thứ 6 — Cấu hình AWS Backup và kiểm thử end-to-end

Cuối tuần, tôi bật **AWS Backup** cho EC2 và RDS bằng một backup plan (tái sử dụng kiến thức Tuần 6): gắn resource assignment và backup rule để hệ thống tự tạo recovery point định kỳ. Sau đó tôi chạy một lượt **kiểm thử end-to-end**: truy cập ứng dụng qua trình duyệt, thao tác dữ liệu để xác nhận app–RDS thông suốt, kiểm tra alarm/dashboard phản ánh đúng trạng thái, và xác nhận instance hiện diện trong Systems Manager. Đến đây, các mảnh rời rạc của 9 tuần đã ghép thành một hệ thống có kiến trúc rõ ràng: **mạng chuẩn → ứng dụng + database → giám sát → sao lưu → quản lý tập trung**.

### Khó khăn gặp phải:

* **Tài nguyên rải rác và trùng lặp qua các tuần**: sau 9 tuần, tài khoản tồn tại nhiều VPC/instance/security group của các bài lab khác nhau, khó phân biệt cái nào còn dùng.
  * **Cách giải quyết:** lập bảng inventory và gắn tag nhất quán để phân loại; đánh dấu rõ tài nguyên "đang dùng" và tài nguyên "sẽ dọn dẹp" cho Tuần 11.
* **Ứng dụng kết nối RDS chưa thông ngay**: ban đầu ứng dụng không kết nối được database do Security Group tầng db chưa mở đúng nguồn.
  * **Cách giải quyết:** áp dụng lại mô hình phân tầng — chỉ cho phép cổng 3306 từ Security Group của tầng ứng dụng, thay vì mở rộng ra cả dải mạng.
* **Đưa instance vào Systems Manager cần đúng quyền**: instance mới triển khai chưa xuất hiện là managed node.
  * **Cách giải quyết:** gắn IAM Role có quyền SSM và chờ node chuyển sang trạng thái Online, đúng như bài học Tuần 9.

### Kết quả đạt được tuần 10:

* Rà soát và lập được danh mục (inventory) toàn bộ tài nguyên đã tạo từ Tuần 1–9; vẽ lại sơ đồ kiến trúc tổng thể.
* Chuẩn hóa hạ tầng bằng CloudFormation template kèm quy ước đặt tên và gắn tag nhất quán.
* Triển khai lại ứng dụng AWS FCJ Management trên EC2 trong VPC chuẩn, kết nối thành công tới RDS với Security Group đúng vai trò.
* Dựng CloudWatch dashboard/alarm cho EC2/RDS và gắn thông báo SNS; đưa EC2 vào Systems Manager như managed node.
* Bật AWS Backup plan cho EC2/RDS và kiểm thử end-to-end toàn bộ hệ thống tích hợp thành công.

### Kiến thức học được:

Tuần 10 dạy tôi rằng làm chủ từng dịch vụ riêng lẻ mới chỉ là một nửa; nửa còn lại là **ghép chúng thành một hệ thống mạch lạc**. Khi đặt cạnh nhau, các mảnh học rời rạc bỗng lộ ra mối liên hệ: mạng (Tuần 2) là nền cho compute (Tuần 3) và database (Tuần 4); giám sát (Tuần 5) và sao lưu (Tuần 6) là hai vòng bảo vệ; còn Systems Manager (Tuần 9) là lớp vận hành phía trên. Tôi cũng thấm thía giá trị của **Infrastructure as Code** và **đặt tên/tag nhất quán** — những thứ tưởng nhỏ nhưng quyết định việc một hệ thống có bảo trì được hay không. Nhìn tổng thể, kiến trúc tốt không phải là nhiều dịch vụ, mà là các dịch vụ được sắp đặt đúng vai trò và kết nối đúng cách.

### Tổng kết tuần:

Tuần 10 mở màn giai đoạn cuối bằng việc biến 9 tuần kiến thức rời rạc thành một hệ thống hoàn chỉnh: hạ tầng mạng chuẩn hóa bằng CloudFormation, ứng dụng AWS FCJ Management chạy trên EC2 kết nối RDS, được giám sát bằng CloudWatch + SNS, sao lưu bằng AWS Backup và quản lý tập trung bằng Systems Manager. Hệ thống tích hợp đã chạy end-to-end, tạo nền tảng vững chắc để bước sang Tuần 11 tập trung kiểm thử, sửa lỗi và tối ưu. Không đưa thông tin nhạy cảm như AWS Account ID, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
