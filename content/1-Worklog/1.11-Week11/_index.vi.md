---
title: "Tuần 11 - Kiểm thử, sửa lỗi và tối ưu hệ thống"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:

* Kiểm thử toàn diện hệ thống đã tích hợp ở Tuần 10: kết nối, định tuyến, khôi phục và cảnh báo.
* Thực hiện **restore drill** — diễn tập khôi phục từ recovery point của AWS Backup để chắc chắn bản sao lưu dùng được.
* Rà soát **bảo mật** theo nguyên tắc least privilege: siết Security Group, kiểm tra IAM role/policy, đóng port không dùng.
* **Tối ưu chi phí và cấu hình**: right-size EC2, đặt retention hợp lý cho log/backup, dọn dẹp tài nguyên thừa.
* Sửa các lỗi phát hiện được và chạy lại kiểm thử hồi quy để xác nhận hệ thống ổn định.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Kiểm thử kết nối và định tuyến toàn hệ thống <br> - Dùng Reachability Analyzer, ping và kiểm tra route peering/Transit Gateway <br> - Ghi nhận các lỗi phát hiện | 29/06/2026 | 29/06/2026 | Tổng hợp nội bộ |
| 3   | - Diễn tập khôi phục (restore drill) từ recovery point AWS Backup <br> - Kiểm thử alarm/SNS bằng cách tạo tình huống vượt ngưỡng | 30/06/2026 | 30/06/2026 | Tổng hợp nội bộ |
| 4   | - Rà soát bảo mật: siết Security Group theo least privilege <br> - Kiểm tra IAM role/policy, gỡ quyền thừa, đóng port không dùng | 01/07/2026 | 01/07/2026 | Tổng hợp nội bộ |
| 5   | - Tối ưu chi phí: right-size EC2, đặt retention cho CloudWatch Logs/Backup <br> - Dọn Elastic IP, NAT Gateway và tài nguyên thừa của các bài lab cũ | 02/07/2026 | 02/07/2026 | Tổng hợp nội bộ |
| 6   | - Sửa các lỗi phát hiện trong tuần <br> - Chạy lại kiểm thử hồi quy, xác nhận hệ thống ổn định | 03/07/2026 | 03/07/2026 | Tổng hợp nội bộ |

### Nội dung thực hiện chi tiết:

> **Ghi chú:** Tuần này tập trung vào kiểm thử, rà soát bảo mật và tối ưu cấu hình nên không có hình minh họa cho tuần này.

#### Thứ 2 — Kiểm thử kết nối và định tuyến toàn hệ thống

Đầu tuần, tôi kiểm thử một cách có hệ thống thay vì thử ngẫu nhiên. Tôi dùng lại **Reachability Analyzer** (Tuần 2) để phân tích đường đi giữa tầng ứng dụng và tầng database, xác nhận traffic tới được đúng đích. Tôi cũng kiểm tra các tuyến định tuyến của VPC Peering (Tuần 7) và Transit Gateway (Tuần 8) — đối chiếu route table để chắc chắn không có tuyến thừa hoặc thiếu. Một số điểm chưa thông (ví dụ ping private xuyên VPC) được ghi lại vào danh sách lỗi để xử lý ở Thứ 6, thay vì sửa vội.

#### Thứ 3 — Diễn tập khôi phục và kiểm thử cảnh báo

Một bản sao lưu chỉ có giá trị khi khôi phục được, nên tôi thực hiện **restore drill**: chọn một recovery point trong AWS Backup (Tuần 6) và khôi phục thành tài nguyên mới, sau đó kiểm tra dữ liệu/khả năng khởi động của tài nguyên khôi phục. Tiếp đó, tôi kiểm thử **cảnh báo**: chủ động tạo tình huống vượt ngưỡng để alarm CloudWatch chuyển trạng thái và xác nhận thông báo được gửi qua SNS về email (đúng chuỗi đã dựng ở Tuần 5). Việc "thử cho hỏng có kiểm soát" giúp tôi tin rằng khi sự cố thật xảy ra, hệ thống sẽ báo và có đường khôi phục.

#### Thứ 4 — Rà soát bảo mật theo least privilege

Tôi rà soát lại toàn bộ **Security Group** theo nguyên tắc least privilege đã học từ Tuần 2–3: gỡ các rule mở quá rộng (ví dụ mở SSH/RDP ra `0.0.0.0/0`), chỉ giữ nguồn thật sự cần, và ưu tiên kết nối qua **Session Manager** thay vì mở port quản trị ra internet. Về IAM, tôi kiểm tra các role/policy đã tạo (như `AdminRole`, `Windows-Lab-SSM-Role`), xác nhận mỗi role chỉ giữ quyền cần thiết và gỡ những quyền thừa. Đây là bước biến "chạy được" thành "chạy an toàn".

#### Thứ 5 — Tối ưu chi phí và cấu hình

Sang phần tối ưu, tôi soát chi phí dựa trên thói quen giữ credit đã hình thành từ các tuần đầu. Tôi **right-size** EC2 về đúng loại vừa đủ (`t3.micro`/`t3.nano` cho workload nhẹ), đặt **retention** hợp lý cho CloudWatch Logs và cho recovery point của AWS Backup để không lưu trữ vô hạn gây tốn kém (bài học retention từ Tuần 5). Tôi cũng dọn dẹp tài nguyên thừa của các bài lab cũ: giải phóng **Elastic IP** không gắn, xóa **NAT Gateway** không còn dùng, và stop/terminate các instance không phục vụ hệ thống chính — những thứ âm thầm phát sinh chi phí.

#### Thứ 6 — Sửa lỗi và kiểm thử hồi quy

Ngày cuối tuần, tôi xử lý lần lượt các lỗi đã ghi nhận từ đầu tuần: bổ sung route còn thiếu cho kết nối private xuyên VPC, chỉnh lại rule Security Group cho đúng nguồn, và sửa các cấu hình chưa nhất quán. Sau mỗi lần sửa, tôi chạy lại **kiểm thử hồi quy** trên các luồng đã kiểm ở Thứ 2–3 để chắc chắn việc sửa không làm hỏng thứ đang chạy. Kết thúc tuần, danh sách lỗi được đóng lại và hệ thống ở trạng thái ổn định, sẵn sàng cho việc tài liệu hóa và bàn giao ở Tuần 12.

### Khó khăn gặp phải:

* **Kết nối private xuyên VPC chưa thông từ các tuần trước**: đúng như đã nhận diện ở Tuần 7, peering/Transit Gateway active nhưng traffic private vẫn chưa đi được.
  * **Cách giải quyết:** bổ sung route trỏ sang dải của VPC đối tác trong route table và mở Security Group cho đúng dải nguồn, rồi kiểm chứng lại bằng ping/Reachability Analyzer.
* **Khó phân biệt lỗi thật với cảnh báo phụ khi kiểm thử**: một số kết quả kiểm thử trả về trạng thái mập mờ (ví dụ ICMP bị chặn ở đích).
  * **Cách giải quyết:** áp dụng bài học Tuần 2 — kiểm chứng bằng nhiều đích và nhiều công cụ (ping, Reachability Analyzer) trước khi kết luận, tránh sửa nhầm chỗ.
* **Tài nguyên thừa tiềm ẩn chi phí**: Elastic IP không gắn và NAT Gateway để quên vẫn tính phí.
  * **Cách giải quyết:** dựa trên bảng inventory của Tuần 10 để rà từng tài nguyên, giải phóng thứ không dùng và ghi lại lý do giữ/xóa.

### Kết quả đạt được tuần 11:

* Kiểm thử toàn diện kết nối và định tuyến của hệ thống (Reachability Analyzer, ping, route peering/Transit Gateway) và lập được danh sách lỗi.
* Diễn tập khôi phục thành công từ recovery point AWS Backup và kiểm thử được cảnh báo alarm → SNS qua tình huống vượt ngưỡng.
* Rà soát bảo mật theo least privilege: siết Security Group, đóng port quản trị không cần thiết, gỡ quyền thừa trong IAM.
* Tối ưu chi phí và cấu hình: right-size EC2, đặt retention cho log/backup, dọn Elastic IP/NAT Gateway và tài nguyên thừa.
* Sửa xong các lỗi phát hiện và chạy kiểm thử hồi quy, đưa hệ thống về trạng thái ổn định.

### Kiến thức học được:

Tuần 11 cho tôi trải nghiệm về giai đoạn ít hào nhoáng nhưng quan trọng nhất của một dự án: **kiểm thử, làm cứng và tối ưu**. Tôi hiểu rằng "chạy được" và "chạy tốt" là hai mức khác nhau — giữa chúng là kiểm thử khôi phục, siết bảo mật và cắt giảm lãng phí. Restore drill dạy tôi rằng backup chưa kiểm thử thì không thể tin; rà soát least privilege cho thấy bảo mật là việc chủ động gỡ bớt chứ không phải thêm vào; còn tối ưu chi phí nhắc rằng mỗi tài nguyên để quên đều có giá của nó. Quan trọng không kém là kỷ luật **ghi nhận lỗi rồi mới sửa** và **kiểm thử hồi quy sau mỗi thay đổi** — cách làm giúp cải thiện hệ thống mà không vô tình phá vỡ thứ đang hoạt động.

### Tổng kết tuần:

Tuần 11 nâng hệ thống tích hợp của Tuần 10 lên mức đáng tin cậy: kết nối và định tuyến được kiểm thử kỹ, khả năng khôi phục và cảnh báo được diễn tập thực tế, bảo mật được siết theo least privilege và chi phí được tối ưu bằng right-size cùng dọn dẹp tài nguyên thừa. Mọi lỗi phát hiện đều được sửa và kiểm thử hồi quy, đưa hệ thống về trạng thái ổn định — sẵn sàng cho Tuần 12 rà soát tài liệu, đánh giá kết quả và bàn giao. Không đưa thông tin nhạy cảm như AWS Account ID, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
