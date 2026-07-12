---
title: "Worklog Tuần 3"
date: 2026-05-04
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Thực hành trọn vẹn vòng đời Amazon EC2 theo bài lab [Giới thiệu về Amazon EC2](https://000004.awsstudygroup.com/vi/): dựng hạ tầng mạng, khởi chạy, kết nối, triển khai ứng dụng và dọn dẹp tài nguyên.
* Xây dựng hai môi trường mạng tách biệt (`Windows-vpc` và `Linux-vpc`), mỗi VPC có subnet riêng và một Security Group thiết kế đúng vai trò.
* Khởi chạy và kết nối Windows instance qua RDP, Linux instance qua SSH (MobaXterm).
* Cài XAMPP trên Windows instance, khởi tạo database `awsuser` bằng phpMyAdmin và chạy ứng dụng Node.js tại localhost.
* Cài đặt, cấu hình Apache web server (kèm MariaDB) trên Amazon Linux 2023, kiểm chứng qua cả public IPv4 và public DNS.
* Triển khai ứng dụng web **AWS FCJ Management** trên Linux instance và kiểm thử đầy đủ luồng CRUD user: List, Add, Details, Edit.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Ôn lý thuyết EC2, AMI, instance type <br> - Tạo VPC `Windows-vpc` (`10.0.0.0/16`) với 4 subnet trên 2 AZ <br> - Bật auto-assign public IPv4 cho public subnet <br> - Tạo Security Group `Windows-SG` (RDP 3389, HTTP 80, Custom TCP 5000, ICMP) | 04/05/2026 | 04/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 3   | - Khởi chạy `Windows-instance` (`t3.micro`) trong public subnet <br> - Giải mã mật khẩu Administrator bằng key pair và kết nối RDP <br> - Cài XAMPP, tạo database `awsuser` và bảng `user` trong phpMyAdmin <br> - Cài Node.js, chạy ứng dụng tại `localhost:5000` <br> - Stop instance sau khi hoàn tất để tiết kiệm credit | 05/05/2026 | 05/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 4   | - Tạo VPC `Linux-vpc` (`10.0.0.0/16`) với 4 subnet trên 2 AZ <br> - Tạo Security Group `Linux-SG` (SSH 22, HTTP 80, HTTPS 443, MySQL 3306, Custom TCP 5000, ICMP) <br> - Khởi chạy `Linux-instance` (`t3.nano`, Amazon Linux 2023) | 06/05/2026 | 06/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 5   | - SSH vào `Linux-instance` bằng MobaXterm với key pair <br> - Cài Apache (`httpd`) và MariaDB bằng `dnf`, kích hoạt dịch vụ với `systemctl` <br> - Kiểm chứng web server qua public IPv4 và public DNS | 07/05/2026 | 07/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 6   | - Triển khai ứng dụng **AWS FCJ Management** trên cổng 5000 <br> - Kiểm thử trọn luồng CRUD user: List, Add, View Details, Edit <br> - Tổng kết tuần và dọn dẹp tài nguyên không dùng | 08/05/2026 | 08/05/2026 | <https://000004.awsstudygroup.com/vi/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Dựng môi trường mạng cho Windows instance

Đầu tuần, thay vì dùng default VPC, tôi chuẩn bị một môi trường mạng riêng cho Windows instance. Tôi tạo VPC `Windows-vpc` với dải CIDR `10.0.0.0/16`, bật **DNS hostnames** và **DNS resolution** để instance sau này nhận được public DNS thân thiện. Phần Resource map xác nhận VPC được sinh ra với 4 subnet và 4 route table đúng như thiết kế.

> **Ảnh minh chứng:**
> ![Chi tiết VPC Windows-vpc sau khi tạo](/images/1.3-Week3/create-window-vpc.png)

VPC được chia thành 4 subnet trên 2 Availability Zone: `Windows-subnet-public1` (`10.0.0.0/20`, ap-southeast-1a), `Windows-subnet-public2` (`10.0.16.0/20`, ap-southeast-1b) và 2 private subnet `10.0.128.0/20`, `10.0.144.0/20`. Với public subnet, tôi bật **auto-assign public IPv4 address** — Console hiển thị banner "You have successfully changed subnet settings", đồng thời chi tiết subnet cho thấy nó đã gắn với route table `Windows-rtb-public`.

> **Ảnh minh chứng:**
> ![Danh sách subnet của Windows-vpc với auto-assign public IPv4 đã bật](/images/1.3-Week3/list-subnet-window.png)

Cuối ngày, tôi tạo Security Group `Windows-SG` gắn vào `Windows-vpc` với 8 inbound rule phục vụ đủ các kịch bản trong tuần: **RDP (TCP 3389)** cho Remote Desktop, **HTTP (TCP 80)**, **Custom TCP 5000** cho ứng dụng Node.js và ICMP để kiểm tra kết nối. Nhìn các rule dưới góc độ "chỉ mở đúng thứ mình dùng", tôi thấy mình cẩn trọng hơn hẳn so với hồi còn chọn default group.

> **Ảnh minh chứng:**
> ![Windows-SG tạo thành công với 8 inbound rule](/images/1.3-Week3/create-window-sg.png)

#### Thứ 3 — Windows instance: RDP, XAMPP và triển khai ứng dụng tại chỗ

Tôi khởi chạy `Windows-instance` (Microsoft Windows Server, `t3.micro`) vào public subnet của `Windows-vpc`, gắn `Windows-SG`. Instance chuyển sang trạng thái **Running** và nhận public IPv4 DNS chỉ sau khoảng một phút — nhanh hơn tôi nghĩ đối với một máy Windows.

> **Ảnh minh chứng:**
> ![Windows-instance đang chạy trên EC2 Console](/images/1.3-Week3/create-window-instances.png)

Để kết nối, tôi chọn **Connect using RDP client**, tải file remote desktop và dùng key pair giải mã mật khẩu Administrator. Dán mật khẩu vào Remote Desktop Connection, tôi vào thẳng màn hình desktop của Windows Server chạy trên EC2. Đây là lần đầu tôi thao tác trên một máy Windows đặt trên cloud, và cảm giác thật sự không khác gì máy local.

> **Ảnh minh chứng:**
> ![Kết nối desktop Windows instance qua RDP](/images/1.3-Week3/connect-window-instances.png)

Trong phiên RDP, tôi cài **XAMPP** để có trọn bộ Apache + MariaDB + PHP. Trên phpMyAdmin (`localhost/phpmyadmin`), tôi tạo database `awsuser` và chạy câu lệnh SQL tạo bảng `user` với các cột `id`, `first_name`, `last_name`, `comments`, `status` — MySQL trả về "empty result set", xác nhận bảng đã được tạo thành công.

> **Ảnh minh chứng:**
> ![Tạo database awsuser và bảng user trong phpMyAdmin trên Windows instance](/images/1.3-Week3/intall-xampp-on-windows-instance.png)

Tiếp theo, tôi cài Node.js, chạy `npm init` và khởi động ứng dụng **AWS FCJ Management** ngay trên instance. Mở `localhost:5000` trong trình duyệt của phiên RDP, trang Records hiển thị dữ liệu đọc trực tiếp từ database `awsuser` — ứng dụng chạy trọn vẹn trên Windows instance.

> **Ảnh minh chứng:**
> ![Ứng dụng AWS FCJ Management chạy tại localhost:5000 trên Windows instance](/images/1.3-Week3/deploy-app-on-windows-instance.png)

Cuối ngày, tôi stop instance để tiết kiệm credit Free Tier. Phiên RDP lập tức báo "Your Remote Desktop Services session has ended" — một minh họa nhỏ nhưng đáng nhớ rằng phiên RDP sống chết theo trạng thái của instance.

> **Ảnh minh chứng:**
> ![Phiên RDP kết thúc sau khi Windows instance bị stop](/images/1.3-Week3/windowinstances_stop.png)

#### Thứ 4 — Dựng mạng và khởi chạy Linux instance

Lặp lại quy trình của Thứ 2, tôi dựng môi trường thứ hai cho Linux: VPC `Linux-vpc` (`10.0.0.0/16`) với DNS hostnames/resolution được bật. Làm lần thứ hai chỉ tốn chưa đến nửa thời gian lần đầu — chuỗi thao tác VPC → Subnet → SG đã bắt đầu thành phản xạ.

> **Ảnh minh chứng:**
> ![Chi tiết VPC Linux-vpc sau khi tạo](/images/1.3-Week3/create-vpc-linux.png)

VPC này cũng có 4 subnet trên 2 AZ (`Linux-subnet-public1/2`, `Linux-subnet-private1/2`) với sơ đồ CIDR giống VPC Windows, và tôi bật auto-assign public IPv4 cho `Linux-subnet-public1`.

> **Ảnh minh chứng:**
> ![Danh sách subnet của Linux-vpc với auto-assign public IPv4 đã bật](/images/1.3-Week3/list-subnet-linux.png)

Security Group `Linux-SG` khác `Windows-SG` về mục đích: thay vì RDP, nó mở **SSH (TCP 22)**, kèm **HTTP 80 / HTTPS 443** cho web server, **MySQL/Aurora (TCP 3306)** cho database và **Custom TCP 5000** cho ứng dụng. Đặt hai group cạnh nhau để so sánh, tôi mới thật sự "ngấm" tư duy mỗi vai trò máy chủ một Security Group riêng.

> **Ảnh minh chứng:**
> ![Linux-SG tạo thành công với 7 inbound rule](/images/1.3-Week3/create-linux-sg.png)

Sau đó tôi khởi chạy `Linux-instance` (Amazon Linux 2023, `t3.nano`) vào public subnet. Danh sách instance kể lại câu chuyện của cả tuần chỉ trong một khung hình: `Windows-instance` ở trạng thái **Stopped** từ hôm qua, `Linux-instance` đang **Running** và initializing.

> **Ảnh minh chứng:**
> ![Linux-instance đang chạy trong khi Windows-instance vẫn stopped](/images/1.3-Week3/create-linux-instances.png)

#### Thứ 5 — SSH, cài Apache và MariaDB trên Amazon Linux 2023

Tôi kết nối instance bằng **MobaXterm** qua SSH: `ec2-user@54.169.253.206` xác thực bằng key pair. Banner Amazon Linux 2023 hiện ra cùng prompt `ip-10-0-8-114` — chính là private IP của instance trong `Linux-vpc`, một lời nhắc trực quan rằng địa chỉ public và private cùng tồn tại trên một máy.

> **Ảnh minh chứng:**
> ![SSH vào Linux instance bằng MobaXterm](/images/1.3-Week3/connect-linux-instance.png)

Tôi cài bộ web và database bằng `dnf`: gói `mariadb105` (phiên bản 10.5.29) cài đặt hoàn tất, sau đó `sudo systemctl start httpd` và `sudo systemctl enable httpd` khởi động Apache và đăng ký chạy cùng hệ thống — lệnh `systemctl is-enabled httpd` trả về `enabled`.

> **Ảnh minh chứng:**
> ![Cài MariaDB và kích hoạt dịch vụ Apache](/images/1.3-Week3/setup-apache-server.png)

Để kiểm chứng, tôi mở trình duyệt từ máy local truy cập public IPv4 `54.169.253.206` — trang **"It works!"** kinh điển của Apache hiện ra, chứng tỏ cả web server lẫn rule port 80 của Security Group đều chính xác.

> **Ảnh minh chứng:**
> ![Kiểm tra Apache qua địa chỉ public IPv4](/images/1.3-Week3/use-ipv4-test-apache.png)

Tôi kiểm tra thêm bằng public DNS `ec2-54-169-253-206.ap-southeast-1.compute.amazonaws.com` và nhận cùng kết quả — xác nhận tùy chọn **DNS hostnames** bật từ lúc tạo VPC đã phát huy tác dụng: một địa chỉ cho con người, một cho máy móc, cùng trỏ về một instance.

> **Ảnh minh chứng:**
> ![Kiểm tra Apache qua public DNS](/images/1.3-Week3/use-dns-test-apache.png)

#### Thứ 6 — Triển khai AWS FCJ Management và kiểm thử luồng CRUD user

Ngày cuối tuần, tôi triển khai ứng dụng **AWS FCJ Management** lên Linux instance và mở ra ngoài qua cổng 5000 — cổng đã được mở sẵn trong `Linux-SG` từ Thứ 4. Truy cập `54.169.253.206:5000` từ máy local, trang Records hiển thị với bảng rỗng và nút **Add New User**: ứng dụng đã chạy thật trên internet, không còn là `localhost` như trên Windows instance nữa.

> **Ảnh minh chứng:**
> ![AWS FCJ Management triển khai trên Linux instance tại cổng 5000](/images/1.3-Week3/deploy-aws-web.png)

Tôi dùng **Add New User** để thêm lần lượt các bản ghi user. Danh sách cập nhật sau mỗi lần thêm và hiển thị đủ 18 bản ghi kèm các nút **View / Edit / Delete** trên từng dòng — phần Create và List của CRUD hoạt động trơn tru.

> **Ảnh minh chứng:**
> ![Danh sách Records sau khi thêm user](/images/1.3-Week3/add-user.png)

Bấm **View** trên một bản ghi, ứng dụng điều hướng tới `/viewuser/18` hiển thị chi tiết user *Joanna Jedrzejczyk* — họ tên, email, số điện thoại và comments được render từ database.

> **Ảnh minh chứng:**
> ![Trang chi tiết user](/images/1.3-Week3/details-user.png)

Cuối cùng, tôi kiểm thử **Edit** tại `/edituser/18`: form tự điền sẵn toàn bộ dữ liệu hiện có, cho phép chỉnh sửa và Submit để cập nhật ngược về MariaDB. Đến đây, trọn luồng — mạng, instance, web server, database và ứng dụng CRUD — đã được kiểm chứng đầu-cuối trên AWS.

> **Ảnh minh chứng:**
> ![Form Edit User với dữ liệu điền sẵn](/images/1.3-Week3/edit-user.png)

### Kết quả đạt được tuần 3:

* Tự dựng hai môi trường mạng hoàn chỉnh, tách biệt theo mục đích (`Windows-vpc` và `Linux-vpc`), mỗi VPC 4 subnet trên 2 AZ, bật auto-assign public IPv4 cho public subnet.
* Thiết kế Security Group theo vai trò máy chủ: `Windows-SG` (RDP 3389, HTTP 80, TCP 5000, ICMP) và `Linux-SG` (SSH 22, HTTP 80, HTTPS 443, MySQL 3306, TCP 5000, ICMP).
* Khởi chạy và quản trị cả Windows instance (`t3.micro`) lẫn Linux instance (`t3.nano`); thành thạo hai đường kết nối — RDP với giải mã mật khẩu bằng key pair và SSH qua MobaXterm.
* Cài XAMPP trên Windows instance, khởi tạo database `awsuser` và bảng `user` bằng phpMyAdmin, chạy ứng dụng Node.js tại `localhost:5000`.
* Cài đặt và kích hoạt Apache (`httpd`), MariaDB 10.5 trên Amazon Linux 2023 bằng `dnf` và `systemctl`, kiểm chứng qua cả public IPv4 lẫn public DNS.
* Triển khai ứng dụng web AWS FCJ Management ra internet trên cổng 5000 và kiểm thử đầy đủ luồng CRUD user: List, Add, Details, Edit.
* Hình thành thói quen stop instance khi không dùng để giữ credit Free Tier.

### Khó khăn gặp phải:

* **Chưa lấy được mật khẩu RDP ngay**: sau khi khởi chạy Windows instance, nút "Get password" chưa khả dụng trong vài phút đầu do instance còn đang khởi tạo — tôi học được cách chờ status check thay vì nghĩ rằng có lỗi.
* **Phiên RDP đột ngột kết thúc** khi instance bị stop; ban đầu tôi tưởng lỗi mạng, nhưng hóa ra đó là hành vi bình thường của vòng đời instance.
* **Mở cổng cho ứng dụng**: ứng dụng ở cổng 5000 không truy cập được từ ngoài cho đến khi tôi kiểm tra lại rule Custom TCP 5000 trong `Linux-SG` và đảm bảo ứng dụng lắng nghe trên mọi interface thay vì chỉ `localhost`.
* **Phân biệt kiểm thử IPv4 và DNS**: để hiểu vì sao hai địa chỉ cùng trỏ về một instance, tôi phải quay lại tùy chọn DNS hostnames đã bật từ ngày đầu tạo VPC — một cấu hình sớm mà nhiều ngày sau mới thấy tác dụng.

### Bài học rút ra:

Thay đổi lớn nhất tuần này là chuyển từ *xây hạ tầng* (tuần 1–2) sang *chạy workload thật trên hạ tầng đó*. Việc lặp lại chuỗi VPC → Subnet → Security Group → Instance hai lần — một cho Windows, một cho Linux — biến những gì từng là checklist thành phản xạ, và việc so sánh hai Security Group cạnh nhau dạy tôi về nguyên tắc least privilege nhiều hơn bất kỳ trang lý thuyết nào. Tôi cũng cảm nhận rõ sự khác biệt giữa hai mô hình vận hành: quản trị Windows trực quan qua RDP, còn Linux thao tác qua terminal và nhẹ hơn hẳn (`t3.nano` là đủ). Nếu làm lại, tôi sẽ tự động hóa phần dựng mạng lặp lại thay vì bấm tay trên Console hai lần.

### Kết luận:

Tuần 3 khép kín vòng tròn từ một tài khoản AWS trống đến một ứng dụng web phục vụ CRUD trên internet: hai VPC tự dựng, Security Group đúng vai trò, Windows instance chạy XAMPP + Node.js, và Linux instance chạy Apache + MariaDB phục vụ ứng dụng AWS FCJ Management trên cổng 5000. Mỗi bước đều có minh chứng — từ trang "It works!" qua cả IPv4 lẫn DNS đến các luồng Add/Details/Edit user. Không đưa thông tin nhạy cảm như AWS Account ID, key pair vào nội dung báo cáo.
