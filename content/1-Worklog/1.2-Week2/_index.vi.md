---
title: "Worklog Tuần 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Nắm vững kiến trúc Amazon VPC: Subnet, Route Table, Internet Gateway, NAT Gateway và các lớp tường lửa trong VPC (Security Group, Network ACL).
* Tự tay xây dựng một VPC hoàn chỉnh theo bài lab [Bắt đầu với Amazon VPC và AWS Site-to-Site VPN](https://000003.awsstudygroup.com/vi/) của AWS Study Group.
* Triển khai EC2 instance trong public/private subnet và kiểm chứng các phương thức kết nối: SSH, EC2 Instance Connect Endpoint, Reachability Analyzer.
* Cấu hình AWS Site-to-Site VPN kết nối VPC chính với môi trường on-premise giả lập.
* Bật VPC Flow Logs và giám sát lưu lượng mạng qua CloudWatch.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Đọc lý thuyết VPC: Subnets, Route Table, Internet Gateway, NAT Gateway <br> - Tìm hiểu tường lửa trong VPC: Security Group, Network ACLs, VPC Resource Map <br> - Tạo VPC `ASG` (`10.10.0.0/16`), 4 subnet và Internet Gateway | 27/04/2026 | 27/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 3   | - Tạo Route Table public/private, thêm route `0.0.0.0/0` → IGW <br> - Gắn subnet association cho từng route table <br> - Tạo 3 Security Group phân tầng <br> - Bật VPC Flow Logs và kiểm tra log streams trên CloudWatch | 28/04/2026 | 28/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 4   | - Tạo EC2 Public và EC2 Private <br> - SSH vào EC2 Public, kiểm tra kết nối internet và ping EC2 Private <br> - SSH nối tiếp từ EC2 Public sang EC2 Private bằng key pair <br> - Cấp phát Elastic IP, tạo NAT Gateway cho private subnet ra internet | 29/04/2026 | 29/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 5   | - Phân tích đường đi mạng bằng Reachability Analyzer <br> - Tạo EC2 Instance Connect Endpoint trong private subnet <br> - Kết nối EC2 Private trực tiếp từ Console qua EIC Endpoint (không cần bastion) | 30/04/2026 | 30/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 6   | - Dựng môi trường on-premise giả lập: VPC `ASG VPN`, subnet, IGW, SG và EC2 đóng vai trò Customer Gateway <br> - Tạo Virtual Private Gateway, Customer Gateway và VPN Connection <br> - Bật Route Propagation cho route table public/private | 01/05/2026 | 01/05/2026 | <https://000003.awsstudygroup.com/vi/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Xây dựng nền tảng mạng: VPC, Subnet và Internet Gateway

Ngày đầu tuần, tôi đọc phần lý thuyết của bài lab để hiểu vai trò từng thành phần: Subnet phân đoạn dải địa chỉ của VPC theo Availability Zone, Route Table quyết định đường đi của lưu lượng, Internet Gateway là cửa ngõ ra internet cho public subnet, còn NAT Gateway cho phép private subnet chủ động ra internet mà không bị truy cập ngược lại.

Sau đó tôi bắt tay tạo VPC tên `ASG` với dải CIDR `10.10.0.0/16`, chọn **VPC only** và giữ Tenancy mặc định, đồng thời bật **DNS hostnames** và **DNS resolution** để các instance trong VPC phân giải được tên miền.

> **Ảnh minh chứng:**
> ![Tạo VPC ASG](/images/1.2-Week2/create-vpc.png)

Tiếp theo, tôi chia VPC thành 4 subnet trải trên 2 Availability Zone để đảm bảo tính sẵn sàng cao:

* `Public Subnet 1` — `10.10.1.0/24` (ap-southeast-1a)
* `Public Subnet 2` — `10.10.2.0/24` (ap-southeast-1b)
* `Private Subnet 1` — `10.10.3.0/24` (ap-southeast-1a)
* `Private Subnet 2` — `10.10.4.0/24` (ap-southeast-1b)

> **Ảnh minh chứng:**
> ![Tạo Subnet](/images/1.2-Week2/create-subnet.png)

Với 2 public subnet, tôi bật **Enable auto-assign public IPv4 address** để EC2 instance khởi chạy trong đó tự động nhận public IP — điều kiện cần để truy cập trực tiếp từ internet.

> **Ảnh minh chứng:**
> ![Bật auto-assign public IP cho Public Subnet 1](/images/1.2-Week2/enable-auto-assign-publicsubnet1.png)
> ![Bật auto-assign public IP cho Public Subnet 2](/images/1.2-Week2/enable-auto-assign-publicsubnet2.png)

Cuối ngày, tôi tạo Internet Gateway và attach vào VPC `ASG`. Đây là thành phần bắt buộc để lưu lượng từ public subnet đi ra internet.

> **Ảnh minh chứng:**
> ![Tạo Internet Gateway](/images/1.2-Week2/create-internet-gateway.png)

#### Thứ 3 — Định tuyến, Security Group và VPC Flow Logs

Tôi tạo `Route table-Public` gắn với VPC `ASG`, sau đó vào **Edit routes** thêm route `0.0.0.0/0` trỏ tới Internet Gateway. Kết quả route table có 2 route ở trạng thái Active: route local `10.10.0.0/16` và route internet qua IGW.

> **Ảnh minh chứng:**
> ![Tạo Route table Public](/images/1.2-Week2/create-public-route-table.png)
> ![Thêm route 0.0.0.0/0 tới Internet Gateway](/images/1.2-Week2/add-new-route.png)

Tôi liên kết 2 public subnet vào route table này qua tab **Subnet associations**, rồi tạo thêm `Route table-Private` cho 2 private subnet — route table này ban đầu chỉ có route local, đảm bảo private subnet hoàn toàn cách ly với internet.

> **Ảnh minh chứng:**
> ![Gắn subnet associations](/images/1.2-Week2/edit-subnet-associations.png)
> ![Tạo Route table Private](/images/1.2-Week2/create-private-route-table.png)

Về tường lửa, tôi tạo 3 Security Group theo mô hình phân tầng:

* `Public subnet - SG`: cho phép SSH (port 22) từ **My IP** và ICMP từ mọi nơi — chỉ máy của tôi SSH được vào bastion.
* `Private subnet - SG`: chỉ cho phép SSH từ `Public subnet - SG` — EC2 Private không thể bị SSH trực tiếp từ internet.
* `VPC-Endpoints-SG`: cho phép HTTPS (port 443) từ dải `10.10.0.0/16` — phục vụ các VPC endpoint nhận kết nối từ bên trong VPC.

> **Ảnh minh chứng:**
> ![Tạo Public subnet SG](/images/1.2-Week2/create-public-subnet-sg.png)
> ![Tạo Private subnet SG](/images/1.2-Week2/create-private-subnet-sg.png)
> ![Tạo VPC Endpoints SG](/images/1.2-Week2/create-vpc-endpoint-sg.png)
> ![Danh sách Security Group](/images/1.2-Week2/sg-list.png)

Cuối ngày, tôi bật **VPC Flow Logs** cho VPC `ASG`, đẩy log về CloudWatch log group `/aws/vpc/flowlogs`. Kiểm tra trên CloudWatch, mỗi ENI trong VPC sinh ra một log stream riêng (`eni-xxx-all`), giúp truy vết toàn bộ lưu lượng ra/vào từng network interface.

> **Ảnh minh chứng:**
> ![Bật VPC Flow Logs](/images/1.2-Week2/create-vpc-flowlogs.png)
> ![Log streams trên CloudWatch](/images/1.2-Week2/logstreams.png)

#### Thứ 4 — Triển khai EC2, kiểm tra kết nối và NAT Gateway

Tôi khởi chạy 2 EC2 instance (Amazon Linux 2023, `t2.micro`):

* `EC2 Public` đặt trong Public Subnet 1, gắn `Public subnet - SG`, có public IP — đóng vai trò bastion host.
* `EC2 Private` đặt trong Private Subnet 2 (`10.10.4.21`), gắn `Private subnet - SG`, không có public IP.

> **Ảnh minh chứng:**
> ![Tạo EC2 Public](/images/1.2-Week2/create-ec2-public.png)
> ![Tạo EC2 Private](/images/1.2-Week2/create-ec2-private.png)

SSH vào `EC2 Public`, tôi kiểm tra kết nối: ping `8.8.8.8` thành công (0% packet loss) chứng tỏ public subnet ra internet bình thường qua IGW; ping `amazon.com` bị 100% packet loss do phía đích chặn ICMP — một bài học thực tế rằng không thể chỉ dựa vào ping tên miền để kết luận mất mạng. Ping sang `EC2 Private` (`10.10.4.21`) thành công nhờ route local của VPC.

> **Ảnh minh chứng:**
> ![Kiểm tra kết nối từ EC2 Public](/images/1.2-Week2/connecting-test.png)
> ![Ping EC2 Private từ EC2 Public](/images/1.2-Week2/ping-ec2-private.png)

Để SSH nối tiếp sang `EC2 Private`, tôi copy key pair `aws-keypair.pem` lên `EC2 Public`, đặt quyền `chmod 400` rồi SSH bằng private IP. Từ `EC2 Private`, tôi thử ping ra ngoài — ban đầu không được vì private subnet chưa có đường ra internet.

> **Ảnh minh chứng:**
> ![Copy key pair lên EC2 Public](/images/1.2-Week2/copy-key-on-ec2-public.png)
> ![SSH từ EC2 Public sang EC2 Private](/images/1.2-Week2/ssh-to-private.png)

Tôi cấp phát một **Elastic IP** rồi tạo **NAT Gateway** trong public subnet, sau đó thêm route `0.0.0.0/0` → NAT Gateway vào `Route table-Private`. Kiểm chứng lại từ `EC2 Private`: ping `amazon.com` đã nhận được phản hồi — private instance chủ động ra internet qua NAT nhưng internet vẫn không thể kết nối ngược vào.

> **Ảnh minh chứng:**
> ![Cấp phát Elastic IP](/images/1.2-Week2/allocate-elastic-ip.png)
> ![Tạo NAT Gateway](/images/1.2-Week2/create-nat-gateway.png)
> ![EC2 Private ping ra internet qua NAT Gateway](/images/1.2-Week2/ping-test-amazon-com.png)

#### Thứ 5 — Reachability Analyzer và EC2 Instance Connect Endpoint

Tôi dùng **VPC Reachability Analyzer** tạo một path phân tích từ `EC2 Public` đến `EC2 Private` để kiểm chứng cấu hình mạng bằng công cụ thay vì thủ công. Kết quả **Reachable**, phần Path details hiển thị từng hop lưu lượng đi qua (ENI nguồn → security group → route table → ENI đích), rất hữu ích khi cần chẩn đoán lỗi kết nối mà không phải SSH vào máy.

> **Ảnh minh chứng:**
> ![Tạo Reachability Analyzer](/images/1.2-Week2/create-reachability-analyzer.png)
> ![Chi tiết đường đi của lưu lượng](/images/1.2-Week2/path-details.png)

Tiếp theo, tôi tạo **EC2 Instance Connect Endpoint** (`EC2 private endpoint`) trong private subnet, gắn `VPC-Endpoints-SG`. Endpoint này cho phép kết nối SSH tới instance trong private subnet **trực tiếp từ AWS Console** mà không cần bastion host, không cần public IP và không mở port ra internet.

> **Ảnh minh chứng:**
> ![Tạo EIC Endpoint](/images/1.2-Week2/create-eic-endpoint.png)
> ![EIC Endpoint ở trạng thái Available](/images/1.2-Week2/create-ec2-endpoint.png)

Sau khi endpoint chuyển trạng thái **Available**, tôi chọn Connect trên `EC2 Private` với connection type **Connect using EC2 Instance Connect Endpoint** — vào thẳng shell của instance `10.10.4.21` ngay trên trình duyệt. So với cách SSH nối tiếp hôm trước, phương thức này an toàn và tiện hơn đáng kể.

> **Ảnh minh chứng:**
> ![Kết nối EC2 Private qua EIC Endpoint](/images/1.2-Week2/connecting-ec2-private-instance.png)

#### Thứ 6 — Site-to-Site VPN: kết nối AWS với môi trường on-premise giả lập

Ngày cuối tuần, tôi dựng môi trường **on-premise giả lập** bằng một VPC riêng tên `ASG VPN` (dải CIDR khác biệt với `10.10.0.0/16` của VPC chính), gồm subnet, Internet Gateway và Security Group riêng.

> **Ảnh minh chứng:**
> ![Tạo VPC ASG VPN](/images/1.2-Week2/create-asg-vpn.png)
> ![Tạo subnet cho VPC VPN](/images/1.2-Week2/create-vpn-subnet.png)
> ![Tạo Internet Gateway cho VPC VPN](/images/1.2-Week2/create-internet-gateway-vpn.png)
> ![Tạo Security Group cho VPN public subnet](/images/1.2-Week2/create-vpn-public-sg.png)

Trong VPC này, tôi khởi chạy EC2 instance `Customer Gateway instance` đóng vai trò thiết bị VPN đầu on-premise (chạy phần mềm VPN để thiết lập IPSec tunnel).

> **Ảnh minh chứng:**
> ![EC2 Customer Gateway instance](/images/1.2-Week2/create-ec2-gateway.png)

Phía AWS, tôi lần lượt tạo các thành phần của Site-to-Site VPN:

1. **Virtual Private Gateway (VGW)** — điểm cuối VPN phía AWS, attach vào VPC `ASG`.
2. **Customer Gateway (CGW)** — khai báo public IP của `Customer Gateway instance`, đại diện thiết bị VPN phía on-premise.
3. **VPN Connection** — liên kết VGW với CGW, AWS tự động sinh 2 IPSec tunnel để đảm bảo tính sẵn sàng cao.

> **Ảnh minh chứng:**
> ![Tạo Virtual Private Gateway](/images/1.2-Week2/create-vpn-gateway.png)
> ![Tạo Customer Gateway](/images/1.2-Week2/create-customer-gateway.png)
> ![Tạo VPN Connection](/images/1.2-Week2/create-vpn-connection.png)

Cuối cùng, tôi bật **Route Propagation** trên cả `Route table-Public` và `Route table-Private` để các route table tự động học tuyến đường tới mạng on-premise từ Virtual Private Gateway, thay vì phải thêm route tĩnh thủ công.

> **Ảnh minh chứng:**
> ![Bật Route Propagation cho Route table Public](/images/1.2-Week2/enable-route-propagation-public.png)
> ![Bật Route Propagation cho Route table Private](/images/1.2-Week2/enable-route-propagation-private.png)

### Kết quả đạt được tuần 2:

* Hiểu và giải thích được vai trò của Subnet, Route Table, Internet Gateway, NAT Gateway trong kiến trúc VPC.
* Tự xây dựng hoàn chỉnh VPC `ASG` (`10.10.0.0/16`) với 4 subnet trải trên 2 Availability Zone, bật DNS hostnames/resolution và auto-assign public IP cho public subnet.
* Cấu hình đúng mô hình định tuyến: public subnet ra internet qua Internet Gateway, private subnet ra internet một chiều qua NAT Gateway (với Elastic IP).
* Thiết kế Security Group phân tầng: bastion chỉ nhận SSH từ My IP, private instance chỉ nhận SSH từ SG của public subnet, VPC endpoint chỉ nhận HTTPS từ trong VPC.
* Triển khai và kiểm chứng kết nối EC2 Public/Private bằng nhiều phương thức: SSH trực tiếp, SSH nối tiếp qua bastion, EC2 Instance Connect Endpoint từ Console.
* Sử dụng được Reachability Analyzer để phân tích đường đi lưu lượng và chẩn đoán cấu hình mạng.
* Bật VPC Flow Logs và đọc được log streams theo từng ENI trên CloudWatch.
* Thiết lập được AWS Site-to-Site VPN: Virtual Private Gateway, Customer Gateway, VPN Connection với 2 IPSec tunnel và Route Propagation.
* Rút kinh nghiệm thực tế: ping tên miền bị chặn ICMP không đồng nghĩa mất kết nối internet — cần kiểm chứng bằng nhiều đích (IP public như `8.8.8.8`) trước khi kết luận.
* Không đưa thông tin nhạy cảm như AWS Account ID, key pair vào nội dung báo cáo.
