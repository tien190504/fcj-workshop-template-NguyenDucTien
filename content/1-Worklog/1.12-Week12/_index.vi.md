---
title: "Tuần 12 - Rà soát tài liệu, đánh giá và bàn giao sản phẩm"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần 12:

* Khép lại kỳ thực tập 12 tuần: **rà soát tài liệu, đánh giá kết quả và chuẩn bị bàn giao** sản phẩm.
* Soát lại toàn bộ worklog Tuần 1–11 để bảo đảm tính nhất quán, chính xác và mạch lạc.
* Viết tài liệu kiến trúc và **runbook vận hành**: cách triển khai, giám sát, sao lưu/khôi phục và quản lý qua Systems Manager.
* Tổng kết kiến thức toàn khóa và tự đánh giá kết quả so với mục tiêu ban đầu.
* Chuẩn bị bài trình bày/demo sản phẩm và **dọn dẹp tài nguyên** cuối kỳ để tránh phát sinh chi phí.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Rà soát toàn bộ worklog Tuần 1–11 <br> - Kiểm tra tính nhất quán về thuật ngữ, format và đường dẫn <br> - Chuẩn hóa lại tài liệu | 06/07/2026 | 06/07/2026 | Tổng hợp nội bộ |
| 3   | - Viết tài liệu kiến trúc tổng thể của hệ thống <br> - Viết runbook vận hành: deploy, giám sát, backup/restore, quản lý qua SSM | 07/07/2026 | 07/07/2026 | Tổng hợp nội bộ |
| 4   | - Tổng kết kiến thức toàn khóa: bản đồ dịch vụ đã học và mối liên hệ giữa các tuần <br> - Tự đánh giá kết quả so với mục tiêu ban đầu | 08/07/2026 | 08/07/2026 | Tổng hợp nội bộ |
| 5   | - Chuẩn bị kịch bản demo ứng dụng AWS FCJ Management và hạ tầng <br> - Soạn nội dung trình bày sản phẩm | 09/07/2026 | 09/07/2026 | Tổng hợp nội bộ |
| 6   | - Trình bày/bàn giao sản phẩm <br> - Dọn dẹp toàn bộ tài nguyên AWS để tránh phát sinh chi phí <br> - Kết thúc kỳ thực tập | 10/07/2026 | 10/07/2026 | Tổng hợp nội bộ |

### Nội dung thực hiện chi tiết:

> **Ghi chú:** Tuần này tập trung vào rà soát tài liệu, tổng kết và bàn giao nên không có hình minh họa cho tuần này.

#### Thứ 2 — Rà soát và chuẩn hóa toàn bộ worklog

Đầu tuần, tôi đọc lại tuần tự **worklog từ Tuần 1 đến Tuần 11** với con mắt của người biên tập. Tôi kiểm tra tính nhất quán về thuật ngữ (cách gọi tên dịch vụ, tên tài nguyên), về format (heading, bảng công việc, cách chèn ảnh và caption) và về đường dẫn ảnh. Một số chỗ diễn đạt hoặc cách trình bày chưa thống nhất giữa các tuần được ghi chú lại để chuẩn hóa. Việc rà soát này cũng giúp tôi nhìn lại toàn bộ mạch phát triển: mỗi tuần là một bước kế thừa tự nhiên từ tuần trước, và cả 12 tuần hợp thành một câu chuyện liền mạch về xây dựng và vận hành hạ tầng AWS.

#### Thứ 3 — Viết tài liệu kiến trúc và runbook vận hành

Tôi viết **tài liệu kiến trúc tổng thể** mô tả hệ thống đã tích hợp ở Tuần 10: sơ đồ mạng (VPC/Subnet/Security Group), tầng ứng dụng (EC2 chạy AWS FCJ Management), tầng dữ liệu (RDS), lớp giám sát (CloudWatch + SNS), lớp sao lưu (AWS Backup) và lớp vận hành (Systems Manager). Kèm theo là một **runbook vận hành** — cẩm nang thao tác cho các tình huống thường gặp: cách triển khai lại ứng dụng, cách xem dashboard và xử lý khi alarm kêu, cách khôi phục từ recovery point, và cách chạy lệnh/vá lỗi qua Session Manager và Run Command. Đây là phần biến trải nghiệm cá nhân thành tài liệu người khác dùng lại được.

#### Thứ 4 — Tổng kết kiến thức toàn khóa và tự đánh giá

Tôi hệ thống lại toàn bộ kiến thức thành một **bản đồ dịch vụ**: nhóm quản trị & danh tính (IAM), lưu trữ (S3), mạng (VPC và các thành phần, VPC Peering, Transit Gateway, Site-to-Site VPN), compute (EC2 Linux/Windows), cơ sở dữ liệu (RDS), hạ tầng dạng mã (CloudFormation), giám sát (CloudWatch), thông báo (SNS), sao lưu (AWS Backup) và vận hành (Systems Manager). Với mỗi nhóm, tôi ghi lại mối liên hệ với các tuần và vai trò trong hệ thống cuối. Sau đó, tôi **tự đánh giá** kết quả so với mục tiêu đặt ra từ những tuần đầu: từ chỗ mới làm quen Console và IAM, tôi đã có thể tự dựng, tích hợp, kiểm thử và vận hành một hệ thống nhiều tầng.

#### Thứ 5 — Chuẩn bị demo và nội dung trình bày

Tôi soạn **kịch bản demo** cho buổi trình bày: mở ứng dụng AWS FCJ Management và thao tác luồng CRUD user để cho thấy phần ứng dụng chạy thật; mở CloudWatch dashboard để minh họa khả năng giám sát; giới thiệu cơ chế sao lưu bằng AWS Backup và cách kết nối an toàn qua Session Manager. Song song, tôi chuẩn bị nội dung trình bày tóm tắt hành trình 12 tuần, các quyết định kiến trúc quan trọng và bài học rút ra — trình bày sao cho người nghe hiểu được cả bức tranh lẫn chi tiết cốt lõi.

#### Thứ 6 — Trình bày, bàn giao và dọn dẹp tài nguyên

Ngày cuối cùng, tôi **trình bày và bàn giao** sản phẩm dựa trên kịch bản đã chuẩn bị, kèm bộ tài liệu kiến trúc và runbook. Sau khi bàn giao, tôi thực hiện **dọn dẹp tài nguyên** cuối kỳ theo đúng thói quen giữ credit đã rèn từ tuần đầu: xóa hoặc stop các EC2 instance, gỡ các stack CloudFormation, giải phóng Elastic IP/NAT Gateway, xóa các tài nguyên mạng và backup không còn cần — tránh phát sinh chi phí sau khi kỳ thực tập kết thúc. Đến đây, hành trình 12 tuần khép lại trọn vẹn: từ một tài khoản AWS trống đến một hệ thống hoàn chỉnh được tài liệu hóa và bàn giao đầy đủ.

### Khó khăn gặp phải:

* **Không nhất quán nhỏ giữa các tuần tài liệu**: qua 11 tuần, cách gọi tên và trình bày có chỗ khác nhau, gây khó khi đọc liền mạch.
  * **Cách giải quyết:** lập một checklist chuẩn hóa (thuật ngữ, format heading/bảng, caption ảnh) và rà từng tuần theo checklist đó thay vì sửa cảm tính.
* **Runbook dễ sa đà vào lý thuyết**: khi viết cẩm nang vận hành, có xu hướng mô tả dài dòng thay vì hướng dẫn thao tác được.
  * **Cách giải quyết:** viết runbook theo dạng tình huống "khi X thì làm Y", bám sát các thao tác thật đã làm ở các tuần trước để tài liệu dùng được ngay.
* **Rủi ro phát sinh chi phí sau khi kết thúc**: nếu quên tài nguyên, chi phí vẫn tiếp tục dù kỳ thực tập đã xong.
  * **Cách giải quyết:** dựa trên bảng inventory (Tuần 10) và kết quả dọn dẹp (Tuần 11) để dọn dẹp lần cuối có hệ thống, đối chiếu từng nhóm tài nguyên trước khi kết thúc.

### Kết quả đạt được tuần 12:

* Rà soát và chuẩn hóa được toàn bộ worklog Tuần 1–11 về thuật ngữ, format và đường dẫn.
* Viết được tài liệu kiến trúc tổng thể và runbook vận hành (deploy, giám sát, backup/restore, quản lý qua SSM).
* Tổng kết kiến thức toàn khóa thành bản đồ dịch vụ và tự đánh giá kết quả so với mục tiêu ban đầu.
* Chuẩn bị hoàn chỉnh kịch bản demo và nội dung trình bày sản phẩm.
* Trình bày/bàn giao sản phẩm và dọn dẹp tài nguyên AWS cuối kỳ để tránh phát sinh chi phí.

### Kiến thức học được:

Tuần 12 khép lại kỳ thực tập bằng những kỹ năng thường bị xem nhẹ nhưng quyết định giá trị thực của một dự án: **tài liệu hóa, tổng kết và bàn giao**. Tôi nhận ra một hệ thống dù tốt đến đâu cũng chỉ hữu ích nếu người khác hiểu và vận hành lại được — nên runbook và tài liệu kiến trúc quan trọng ngang với chính hệ thống. Việc nhìn lại 12 tuần dưới dạng một bản đồ dịch vụ cho tôi thấy rõ bức tranh lớn: các dịch vụ AWS không tồn tại rời rạc mà bổ trợ cho nhau theo từng lớp — mạng, compute, dữ liệu, giám sát, sao lưu và vận hành. Cuối cùng, thói quen **dọn dẹp tài nguyên** nhắc rằng trách nhiệm với chi phí và tài nguyên kéo dài đến tận khi dự án đóng lại, không chỉ trong lúc xây dựng.

### Tổng kết tuần:

Tuần 12 hoàn tất kỳ thực tập 12 tuần: toàn bộ worklog được rà soát và chuẩn hóa, tài liệu kiến trúc và runbook vận hành được viết đầy đủ, kiến thức toàn khóa được tổng kết thành bản đồ dịch vụ, sản phẩm được trình bày và bàn giao, và tài nguyên AWS được dọn dẹp gọn gàng. Nhìn lại cả hành trình — từ những thao tác đầu tiên trên Console và IAM ở Tuần 1 đến một hệ thống nhiều tầng được tích hợp, kiểm thử, tối ưu và tài liệu hóa ở Tuần 12 — tôi thấy rõ sự trưởng thành cả về kiến thức lẫn tư duy vận hành trên nền tảng AWS. Không đưa thông tin nhạy cảm như AWS Account ID, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
