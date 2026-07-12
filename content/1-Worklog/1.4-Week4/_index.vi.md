---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Làm quen với Amazon RDS — dịch vụ cơ sở dữ liệu được quản lý (managed database) của AWS.
* Dựng hạ tầng mạng `first-cloud-vpc` với Security Group tách riêng cho tầng ứng dụng (`first-cloud-app-sg`) và tầng database (`first-cloud-db-sg`).
* Tạo DB subnet group, khởi chạy app server EC2 và RDS MySQL instance `first-cloud-db-instance`.
* Kết nối RDS từ EC2 bằng mysql client: tạo database `first_cloud_users`, bảng `user` và nạp dữ liệu.
* Kết nối ứng dụng web **AWS FCJ Management** với RDS và kiểm chứng dữ liệu hiển thị trên trình duyệt.
* Giám sát RDS qua các metric CloudWatch, tìm hiểu automated backup và thực hành restore snapshot.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Tìm hiểu Amazon RDS và mô hình 2 tầng app/database <br> - Tạo VPC `first-cloud-vpc` (`10.0.0.0/16`) với 4 subnet <br> - Tạo Security Group `first-cloud-app-sg` (SSH 22, HTTP 80, HTTPS 443, Custom TCP 5000) <br> - Tạo Security Group `first-cloud-db-sg` (chỉ mở MySQL/Aurora 3306) | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tạo DB subnet group `first-cloud-db-subnet-group` cho RDS <br> - Khởi chạy EC2 `first-cloud-app-server` (`t2.micro`) trong public subnet <br> - Kết nối SSH vào app server bằng MobaXterm | 12/05/2026 | 12/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tạo RDS MySQL instance `first-cloud-db-instance` (`db.t4g.micro`) <br> - Từ EC2 kết nối RDS bằng mysql client <br> - Tạo database `first_cloud_users`, bảng `user` và INSERT 18 bản ghi <br> - Kiểm chứng dữ liệu bằng `SHOW TABLES`, `SELECT` | 13/05/2026 | 13/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Kết nối ứng dụng AWS FCJ Management với RDS, chạy trên cổng 5000 <br> - Kiểm chứng dữ liệu từ RDS hiển thị trên web <br> - Xem các metric giám sát RDS trên tab Monitoring (CloudWatch) | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Kiểm tra cấu hình automated backup và snapshot tự động của RDS <br> - Thực hành restore snapshot thành instance mới `first-cloud-db-restore` <br> - Tổng kết tuần và dọn dẹp tài nguyên | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Dựng nền tảng mạng và Security Group phân tầng app/db

Ngày đầu tuần, tôi dựng hạ tầng mạng cho bài lab RDS. Tôi tạo VPC `first-cloud-vpc` với dải CIDR `10.0.0.0/16`, bật DNS hostnames/DNS resolution; phần Resource map xác nhận VPC ở trạng thái **Available** với 4 subnet và 4 route table.

> **Ảnh minh chứng:**
> ![Chi tiết VPC first-cloud-vpc sau khi tạo](/images/1.4-Week4/create-first-cloud-vpc.png)

Khác với các tuần trước chỉ dùng một Security Group cho mỗi máy, tuần này tôi thiết kế theo mô hình **2 tầng**: tầng ứng dụng và tầng database có SG riêng. `first-cloud-app-sg` dành cho app server với 4 inbound rule: **SSH (22)** để quản trị, **HTTP (80)** / **HTTPS (443)** cho web và **Custom TCP 5000** cho ứng dụng Node.js.

> **Ảnh minh chứng:**
> ![Tạo Security Group first-cloud-app-sg với 4 inbound rule](/images/1.4-Week4/create-first-cloud-app-sg.png)

`first-cloud-db-sg` dành cho RDS thì tối giản hơn hẳn: chỉ duy nhất **một** inbound rule **MySQL/Aurora (TCP 3306)** — database không cần mở bất kỳ cổng nào khác ra ngoài. Đặt hai SG cạnh nhau, nguyên tắc least privilege hiện ra rất rõ: mỗi tầng chỉ mở đúng những cổng nó thật sự phục vụ.

> **Ảnh minh chứng:**
> ![Tạo Security Group first-cloud-db-sg chỉ mở port 3306](/images/1.4-Week4/create-first-cloud-db-sg.png)

#### Thứ 3 — DB subnet group và app server EC2

RDS không đặt trực tiếp vào một subnet như EC2 mà yêu cầu một **DB subnet group** — nhóm các subnet trải trên nhiều Availability Zone để AWS chọn vị trí đặt database. Tôi tạo `first-cloud-db-subnet-group` từ các subnet của `first-cloud-vpc`; trạng thái **Complete** xác nhận nhóm subnet hợp lệ.

> **Ảnh minh chứng:**
> ![DB subnet group first-cloud-db-subnet-group tạo thành công](/images/1.4-Week4/create-first-cloud-db-subnet-group.png)

Tiếp theo, tôi khởi chạy EC2 `first-cloud-app-server` (`t2.micro`, ap-southeast-1a) trong public subnet, gắn `first-cloud-app-sg` — máy này sẽ đóng vai trò app server kết nối vào RDS ở các ngày sau.

> **Ảnh minh chứng:**
> ![Khởi chạy EC2 first-cloud-app-server](/images/1.4-Week4/create-first-cloud-app-server.png)

Khi instance chạy ổn định, tôi SSH vào bằng **MobaXterm** với key pair: `ec2-user@54.169.53.137`, banner Amazon Linux 2023 hiện ra cùng prompt `ip-10-0-7-96` — private IP của app server trong VPC.

> **Ảnh minh chứng:**
> ![SSH vào app server bằng MobaXterm](/images/1.4-Week4/connect-ec2-instance.png)

#### Thứ 4 — Tạo RDS MySQL và khởi tạo dữ liệu từ EC2

Ngày quan trọng nhất tuần: tôi tạo RDS instance `first-cloud-db-instance` với engine **MySQL Community**, class `db.t4g.micro`, đặt trong subnet group và gắn `first-cloud-db-sg` đã chuẩn bị. Console hiển thị trạng thái **Creating** cùng thông báo database cần vài phút để khởi chạy; tab Connectivity & security cho thấy các phương thức kết nối (code snippets, CloudShell, Endpoints).

> **Ảnh minh chứng:**
> ![RDS instance first-cloud-db-instance đang được tạo](/images/1.4-Week4/create-rds.png)

Khi RDS sẵn sàng, từ phiên SSH trên app server tôi dùng mysql client kết nối tới endpoint của database (server trả về version 8.4.9). Tôi tạo database mới và chuyển vào làm việc:

```sql
CREATE DATABASE IF NOT EXISTS first_cloud_users;
USE first_cloud_users;
```

> **Ảnh minh chứng:**
> ![Kết nối RDS từ EC2 và tạo database first_cloud_users](/images/1.4-Week4/connect-db.png)

Tiếp đó tôi tạo bảng `user` (các cột `id`, `first_name`, `last_name`, `email`, `phone`, `comments`, `status`) và chạy câu lệnh `INSERT` nạp dữ liệu mẫu — kết quả `Query OK, 18 rows affected`, 18 bản ghi được ghi vào RDS không lỗi, không trùng lặp.

> **Ảnh minh chứng:**
> ![Tạo bảng user và INSERT 18 bản ghi vào RDS](/images/1.4-Week4/create-table-add-data.png)

Để kiểm chứng, tôi lần lượt chạy `SHOW DATABASES`, `SHOW TABLES` và `SELECT * FROM user` — bảng kết quả trả về đủ 18 dòng dữ liệu với đầy đủ các cột, xác nhận database trên RDS đã sẵn sàng phục vụ ứng dụng.

> **Ảnh minh chứng:**
> ![Kiểm chứng dữ liệu bằng SHOW TABLES và SELECT * FROM user](/images/1.4-Week4/show-data.png)

#### Thứ 5 — Kết nối ứng dụng với RDS và giám sát qua CloudWatch

Tôi cấu hình ứng dụng **AWS FCJ Management** trên app server trỏ tới endpoint của RDS rồi chạy trên cổng 5000. Truy cập `54.169.53.137:5000` từ máy local, trang Records hiển thị đủ 18 user kèm các nút View / Edit / Delete — điểm khác biệt then chốt so với tuần 3 là dữ liệu giờ nằm trên một **database được quản lý riêng**, không còn chung máy với ứng dụng.

> **Ảnh minh chứng:**
> ![Ứng dụng AWS FCJ Management đọc dữ liệu từ RDS qua cổng 5000](/images/1.4-Week4/web.png)

Sau đó tôi khám phá tab **Monitoring** của RDS instance — nơi tổng hợp các metric CloudWatch mà một managed database cung cấp sẵn: BinLogDiskUsage, BurstBalance, CPUCreditBalance, CPUCreditUsage…

> **Ảnh minh chứng:**
> ![Các metric CloudWatch của RDS: BinLogDiskUsage, BurstBalance, CPUCreditBalance](/images/1.4-Week4/monitor-rds-metrics.png)

Đáng chú ý nhất là nhóm metric vận hành: **CPUUtilization** tăng lên rồi hạ khi tôi chạy các câu lệnh SQL, **DatabaseConnections** nhảy lên đúng lúc ứng dụng kết nối vào, cùng DiskQueueDepth, EBSByteBalance%, FreeableMemory. Nhìn biểu đồ phản ánh chính thao tác mình vừa làm giúp tôi hiểu giá trị của việc AWS giám sát hộ toàn bộ tầng database.

> **Ảnh minh chứng:**
> ![Metric CPUUtilization và DatabaseConnections phản ánh hoạt động của ứng dụng](/images/1.4-Week4/monitor-rds-cpu-connections.png)

#### Thứ 6 — Automated backup và restore snapshot

Ngày cuối tuần, tôi tìm hiểu khả năng sao lưu của RDS trong tab **Maintenance & backups**: automated backup được bật (lưu 1 ngày), có backup window cố định, và AWS đã tự tạo một snapshot loại **Automated** ở trạng thái **Available** — hoàn toàn không cần tôi thao tác gì.

> **Ảnh minh chứng:**
> ![Automated backup và snapshot tự động của RDS ở trạng thái Available](/images/1.4-Week4/rds-automated-backup.png)

Tôi thực hành **restore** từ snapshot này thành một instance mới tên `first-cloud-db-restore`. Console báo "Snapshot is being restored", danh sách Databases hiển thị 2 instance: `first-cloud-db-instance` (Available) và `first-cloud-db-restore` (Creating). Một điểm quan trọng tôi rút ra: restore không ghi đè lên database cũ mà luôn tạo instance mới — an toàn cho dữ liệu gốc nhưng cũng có nghĩa là ứng dụng phải đổi endpoint nếu muốn dùng bản khôi phục.

> **Ảnh minh chứng:**
> ![Restore snapshot thành instance mới first-cloud-db-restore](/images/1.4-Week4/restore-rds-snapshot.png)

### Kết quả đạt được tuần 4:

* Hiểu vai trò của Amazon RDS — database được AWS quản lý (tự động backup, giám sát, vá lỗi) so với tự cài MySQL/MariaDB trên EC2 như tuần 3.
* Dựng hoàn chỉnh hạ tầng cho mô hình 2 tầng: VPC `first-cloud-vpc`, SG tầng ứng dụng `first-cloud-app-sg` (SSH, HTTP, HTTPS, TCP 5000) và SG tầng database `first-cloud-db-sg` (chỉ MySQL 3306).
* Nắm được khái niệm DB subnet group và lý do RDS yêu cầu subnet trải trên nhiều Availability Zone.
* Tạo và kết nối thành công RDS MySQL `first-cloud-db-instance` (`db.t4g.micro`) từ EC2 app server qua mysql client.
* Khởi tạo database `first_cloud_users`, bảng `user` và nạp 18 bản ghi; kiểm chứng bằng `SHOW DATABASES`, `SHOW TABLES`, `SELECT`.
* Kết nối ứng dụng AWS FCJ Management với RDS và xác nhận dữ liệu hiển thị đầy đủ trên web qua cổng 5000.
* Đọc được các metric CloudWatch của RDS (CPUUtilization, DatabaseConnections, FreeableMemory…) và liên hệ chúng với thao tác thực tế của ứng dụng.
* Hiểu cơ chế automated backup của RDS và thực hành restore snapshot thành instance mới `first-cloud-db-restore`.

### Khó khăn gặp phải:

* **RDS tạo khá lâu**: instance ở trạng thái Creating nhiều phút, ban đầu tôi tưởng cấu hình sai — thực tế đây là thời gian bình thường để AWS chuẩn bị một database được quản lý.
* **Kết nối database bị từ chối lúc đầu**: phải rà lại đúng chuỗi phụ thuộc — RDS nằm trong subnet group, gắn đúng `first-cloud-db-sg`, và app server phải nằm trong VPC có đường tới database — trước khi mysql client kết nối được.
* **Restore không thay thế instance cũ**: tôi từng nghĩ restore sẽ "khôi phục tại chỗ"; thực tế RDS luôn tạo instance mới từ snapshot, nên cần chú ý cả chi phí lẫn endpoint của ứng dụng.

### Bài học rút ra:

Tuần 4 cho tôi thấy sự khác biệt giữa **tự vận hành** và **được quản lý**: cùng là MySQL, nhưng trên RDS tôi không phải cài đặt, không phải cấu hình backup, còn nhận sẵn cả bộ metric giám sát — đổi lại phải hiểu thêm các khái niệm riêng như DB subnet group hay snapshot. Việc tách ứng dụng và database ra hai tầng với hai Security Group riêng cũng thay đổi cách tôi nhìn kiến trúc: bảo mật không nằm ở một tường lửa duy nhất mà ở việc mỗi tầng chỉ mở đúng cổng nó cần. Nếu làm lại, tôi sẽ ghi chú endpoint và thứ tự phụ thuộc (VPC → SG → subnet group → RDS) ngay từ đầu để đỡ mất thời gian dò lỗi kết nối.

### Kết luận:

Tuần 4 hoàn thiện bức tranh ứng dụng web chuẩn trên AWS: app server EC2 và database RDS tách tầng rõ ràng, dữ liệu nạp qua mysql client hiển thị trọn vẹn trên web AWS FCJ Management, kèm giám sát CloudWatch và quy trình backup/restore đã được kiểm chứng bằng thao tác thật. Không đưa thông tin nhạy cảm như AWS Account ID, mật khẩu database hay key pair vào nội dung báo cáo.
