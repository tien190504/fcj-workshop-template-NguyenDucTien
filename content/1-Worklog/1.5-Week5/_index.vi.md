---
title: "Worklog Tuần 5"
date: 2026-05-18
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Làm quen với Amazon CloudWatch — dịch vụ giám sát và quan sát (observability) của AWS cho hạ tầng và ứng dụng.
* Dựng môi trường thực hành bằng CloudFormation stack `FCJ-CloudWatch-Workshop` với nhóm EC2 instance test (Instance-A → Instance-E).
* Khám phá CloudWatch Metrics: duyệt metric EC2, vẽ nhiều metric trên cùng đồ thị, thêm annotation, dùng metric math (`SEARCH`, `SORT`) và dynamic label.
* Xem các metric mức hệ điều hành do CloudWatch Agent thu thập trong namespace `CWAgent`.
* Làm việc với CloudWatch Logs: cấu hình retention cho log group và tạo metric filter đếm lỗi từ log ứng dụng.
* Tạo CloudWatch Alarm từ metric filter, gắn hành động gửi thông báo qua SNS topic về email và tổng hợp giám sát trên dashboard `CloudWatch-Workshop`.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Tìm hiểu tổng quan CloudWatch: Metrics, Logs, Alarms, Dashboards <br> - Tạo CloudFormation stack `FCJ-CloudWatch-Workshop` dựng hạ tầng workshop <br> - Duyệt metric `CPUUtilization` của EC2 và vẽ đồ thị cho các instance test | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Vẽ 2 metric khác đơn vị (`CPUUtilization`, `EBSWriteBytes`) trên cùng đồ thị với 2 trục Y <br> - Thêm horizontal annotation (ngưỡng 5%) và vertical annotation (`Job start`) cho đồ thị | 19/05/2026 | 19/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Dùng metric math: biểu thức `SEARCH` gom metric hàng loạt, `SORT` xếp hạng instance <br> - Duyệt metric `procstat_memory_rss` trong namespace `CWAgent` <br> - Tạo dynamic label với `${PROP('Dim.…')}`, xử lý lỗi Invalid syntax | 20/05/2026 | 20/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Cấu hình retention 1 tuần cho log group `/ec2/linux/var/log/messages` <br> - Tạo metric filter `PythonAppErrors` đếm dòng log ERROR của ứng dụng Python | 21/05/2026 | 21/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Tạo alarm `PythonApplicationErrorAlarm` (ERROR > 10 trong 1 phút) gắn SNS topic `Error_logs_reach_10` <br> - Xác nhận subscription SNS qua email <br> - Thêm widget alarm vào dashboard `CloudWatch-Workshop` | 22/05/2026 | 22/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Dựng hạ tầng workshop bằng CloudFormation và làm quen CloudWatch Metrics

Ngày đầu tuần, thay vì tạo tay từng tài nguyên như các tuần trước, tôi dùng **CloudFormation** để dựng toàn bộ môi trường thực hành. Stack `FCJ-CloudWatch-Workshop` chạy tại Region `ap-southeast-1` hoàn tất với trạng thái **CREATE_COMPLETE**; tab Events liệt kê hơn 100 sự kiện tạo tài nguyên, trong đó có các nested stack sinh ra nhóm EC2 instance test. Đây là lần đầu tôi thấy rõ giá trị của Infrastructure as Code: một template tạo ra cả loạt máy chủ đồng nhất chỉ trong vài phút.

> **Ảnh minh chứng:**
> ![Stack FCJ-CloudWatch-Workshop tạo thành công với trạng thái CREATE_COMPLETE](/images/1.5-Week5/create-cloudformation-stack.png)

Tiếp theo, tôi mở **CloudWatch → Metrics → All metrics** và duyệt vào nhóm **EC2 → Per-Instance Metrics**. Lọc theo tên metric `CPUUtilization`, console tìm thấy 11 kết quả tương ứng với các instance đang chạy trong tài khoản — đồ thị ban đầu trống vì chưa chọn metric nào để vẽ.

> **Ảnh minh chứng:**
> ![Duyệt metric CPUUtilization của EC2 trong CloudWatch, tìm thấy 11 kết quả](/images/1.5-Week5/browse-ec2-cpu-metrics.png)

Tôi tick chọn `CPUUtilization` của hai instance `ssmStack/Instance-A/test-instance` và `ssmStack/Instance-B/test-instance` do stack tạo ra. Các điểm dữ liệu xuất hiện trên đồ thị (khoảng 7.3% và 0.4%) — vì instance vừa khởi động nên mới chỉ có datapoint đầu tiên.

> **Ảnh minh chứng:**
> ![Chọn CPUUtilization của Instance-A và Instance-B để vẽ đồ thị](/images/1.5-Week5/select-instance-cpu-metrics.png)

#### Thứ 3 — Vẽ nhiều metric khác đơn vị và thêm annotation cho đồ thị

Tôi thử vẽ hai metric **khác đơn vị** trên cùng một đồ thị: thêm `EBSWriteBytes` (đơn vị Bytes) của Instance-A bên cạnh `CPUUtilization` (đơn vị Percent). CloudWatch tự tách thành hai trục Y — Percent bên trái, Bytes bên phải (giá trị ghi EBS lên tới ~188M bytes) — nhờ đó so sánh được tương quan CPU và I/O của cùng một máy mà không metric nào bị "đè bẹp" do khác thang đo.

> **Ảnh minh chứng:**
> ![Vẽ CPUUtilization và EBSWriteBytes trên cùng đồ thị với 2 trục Y riêng](/images/1.5-Week5/add-ebs-write-bytes-metric.png)

Sang tab **Options**, tôi thêm hai loại annotation cho đồ thị: một **horizontal annotation** đánh dấu ngưỡng 5% CPU (đường đứt nét đỏ ngang) và một **vertical annotation** nhãn `Job start` đánh dấu thời điểm bắt đầu phiên làm việc. Di chuột vào datapoint, tooltip hiển thị đúng giá trị từng metric tại thời điểm đó (EBSWriteBytes 188,156,800 — CPUUtilization 7.30). Annotation giúp người xem đồ thị nhận ra ngay ngưỡng cảnh báo và mốc sự kiện quan trọng.

> **Ảnh minh chứng:**
> ![Thêm horizontal annotation ngưỡng 5% và vertical annotation Job start](/images/1.5-Week5/add-graph-annotations.png)

#### Thứ 4 — Metric math (SEARCH, SORT), namespace CWAgent và dynamic label

Ngày nhiều kỹ thuật nhất tuần: thay vì tick chọn từng metric thủ công, tôi dùng **metric math**. Đầu tiên là biểu thức `SEARCH` — tôi mở **Add query / Edit math expression** và nhập `SEARCH("disk_used_percent", 'Average', 300)` để gom toàn bộ metric dung lượng đĩa của các instance vào một biểu thức duy nhất.

> **Ảnh minh chứng:**
> ![Nhập biểu thức SEARCH("disk_used_percent", 'Average', 300) trong Edit math expression](/images/1.5-Week5/edit-search-expression.png)

Sau khi Apply, đồ thị dạng **Stacked area** hiển thị chồng lớp phần trăm đĩa đã dùng của tất cả instance khớp điều kiện tìm kiếm — không cần biết trước danh sách instance, máy mới sinh ra cũng tự động được gom vào.

> **Ảnh minh chứng:**
> ![Kết quả biểu thức SEARCH hiển thị dạng stacked area cho mọi instance khớp điều kiện](/images/1.5-Week5/apply-search-expression.png)

Tiếp theo là biểu thức `SORT`. Tôi thêm `SORT(METRICS(), SUM, DESC, 10)` để xếp hạng các metric đang vẽ theo tổng giá trị giảm dần, giới hạn 10 kết quả.

> **Ảnh minh chứng:**
> ![Biểu thức SORT(METRICS(), SUM, DESC, 10) xếp hạng metric theo tổng giá trị](/images/1.5-Week5/sort-metrics-expression.png)

Sau đó tôi sửa lại thành `SORT(e1, SUM, DESC, 3)` — chỉ giữ **top 3** instance có CPU cao nhất từ kết quả `SEARCH` ở biểu thức `e1`. Kết hợp SEARCH + SORT tạo ra đồ thị "top N máy nóng nhất" tự cập nhật, rất hữu ích khi vận hành nhiều máy chủ.

> **Ảnh minh chứng:**
> ![Sửa thành SORT(e1, SUM, DESC, 3) để lấy top 3 instance có CPU cao nhất](/images/1.5-Week5/sort-top3-instances.png)

Tôi cũng khám phá namespace **CWAgent** — nơi CloudWatch Agent cài trên instance đẩy các metric mức hệ điều hành mà EC2 mặc định không có. Duyệt theo bộ dimension `ImageId, InstanceId, InstanceType, exe, process_name` và lọc `exe: cloudwatch`, metric `procstat_memory_rss`, tôi thu được 4 kết quả — lượng RAM thực (RSS) mà chính tiến trình CloudWatch Agent chiếm trên từng máy, vẽ dạng stacked lên tới ~563.5M bytes.

> **Ảnh minh chứng:**
> ![Metric procstat_memory_rss của tiến trình CloudWatch Agent trong namespace CWAgent](/images/1.5-Week5/cwagent-procstat-metrics.png)

Cuối ngày tôi thực hành **dynamic label** để nhãn của từng đường tự sinh theo dimension. Lần đầu tôi dán nhầm cú pháp `${PROP('Dim.exe')} - ${PROP('Dim.InstanceId')}` vào ô biểu thức toán học, CloudWatch báo ngay **Error in expression e1 [Invalid syntax]** và vùng đồ thị chuyển đỏ.

> **Ảnh minh chứng:**
> ![Lỗi Invalid syntax do dán cú pháp dynamic label vào ô math expression](/images/1.5-Week5/dynamic-label-error.png)

Sau khi hiểu ra `${PROP(…)}` là cú pháp dành cho phần **Label** chứ không phải expression, tôi chuyển nó sang cột Label của biểu thức `SEARCH('{CWAgent,ImageId,InstanceId,…}')` — đồ thị vẽ lại bình thường và mỗi đường được đặt tên tự động theo tiến trình và instance tương ứng.

> **Ảnh minh chứng:**
> ![Dynamic label hoạt động sau khi đặt đúng vào phần Label của biểu thức SEARCH](/images/1.5-Week5/dynamic-label-fixed.png)

#### Thứ 5 — CloudWatch Logs: retention và metric filter

Tôi chuyển sang phần **Logs → Log groups**. Tài khoản có 2 log group: `/aws/vpc/flowlogs` (VPC Flow Logs từ bài lab tuần 2) và `/ec2/linux/var/log/messages` — log hệ thống được đẩy lên từ EC2. Log group mặc định lưu trữ vĩnh viễn (Never expire) gây tốn chi phí, nên tôi chỉnh **retention** của `/ec2/linux/var/log/messages` xuống **1 tuần**; banner xanh xác nhận "The retention setting(s) … have been updated".

> **Ảnh minh chứng:**
> ![Cập nhật retention 1 tuần cho log group /ec2/linux/var/log/messages](/images/1.5-Week5/set-log-retention.png)

Tiếp đó, tôi tạo **metric filter** tên `PythonAppErrors` trên log group này để đếm số dòng log chứa lỗi của ứng dụng Python chạy trên instance. Console báo tạo thành công, phần Log group details hiển thị **Metric filters: 1** — từ đây dòng chữ trong log đã được chuyển hóa thành một metric số đếm có thể vẽ đồ thị và đặt cảnh báo.

> **Ảnh minh chứng:**
> ![Metric filter PythonAppErrors được tạo trên log group, Metric filters: 1](/images/1.5-Week5/create-metric-filter.png)

#### Thứ 6 — Alarm, thông báo SNS qua email và dashboard tổng hợp

Ngày cuối tuần, tôi tạo **alarm** `PythonApplicationErrorAlarm` dựa trên metric của filter hôm trước, với điều kiện: `/var/log/messages - ERROR > 10 for 1 datapoints within 1 minute` — nếu trong 1 phút ứng dụng ghi quá 10 dòng ERROR thì chuyển sang trạng thái cảnh báo. Alarm ở trạng thái **OK**, cột Actions hiển thị **Actions enabled** với hành động Warning gửi tới SNS topic.

> **Ảnh minh chứng:**
> ![Alarm PythonApplicationErrorAlarm với điều kiện ERROR > 10 trong 1 phút, trạng thái OK](/images/1.5-Week5/create-error-alarm.png)

Hành động của alarm trỏ tới SNS topic `Error_logs_reach_10` có subscription là email của tôi. AWS gửi thư **"AWS Notification - Subscription Confirmation"** — đáng chú ý là thư này rơi vào mục **Thư rác (Spam)** của Gmail, phải chủ động vào tìm mới thấy.

> **Ảnh minh chứng:**
> ![Email xác nhận subscription SNS nằm trong mục Spam của Gmail](/images/1.5-Week5/sns-confirmation-email.png)

Bấm **Confirm subscription** trong thư, trang SNS hiển thị **"Subscription confirmed!"** kèm subscription ID — từ giờ mỗi lần alarm chuyển trạng thái, tôi sẽ nhận được email cảnh báo thật.

> **Ảnh minh chứng:**
> ![Trang SNS xác nhận Subscription confirmed cho topic Error_logs_reach_10](/images/1.5-Week5/confirm-sns-subscription.png)

Cuối cùng, tôi thêm widget alarm vào **dashboard** để tập trung giám sát về một nơi. Console báo "1 new widget(s) have been added to your dashboard CloudWatch-Workshop".

> **Ảnh minh chứng:**
> ![Thêm widget alarm vào dashboard CloudWatch-Workshop thành công](/images/1.5-Week5/add-alarm-to-dashboard.png)

Mở dashboard `CloudWatch-Workshop`, widget `PythonApplicationErrorAlarm` hiển thị biểu đồ số lỗi ERROR theo thời gian kèm ngưỡng cảnh báo màu đỏ. Một chi tiết quan trọng: dashboard có **Autosave: Off** — widget vừa thêm chưa được lưu tự động, phải bấm **Save dashboard** thì thay đổi mới được giữ lại.

> **Ảnh minh chứng:**
> ![Dashboard CloudWatch-Workshop với widget theo dõi lỗi ứng dụng Python](/images/1.5-Week5/cloudwatch-dashboard.png)

### Kết quả đạt được tuần 5:

* Hiểu vai trò của Amazon CloudWatch trong giám sát hạ tầng: Metrics, Logs, Alarms và Dashboards phối hợp thành một vòng quan sát khép kín.
* Dựng được môi trường workshop bằng CloudFormation stack `FCJ-CloudWatch-Workshop` và cảm nhận rõ lợi ích của Infrastructure as Code.
* Duyệt và vẽ được metric `CPUUtilization` của các EC2 instance; vẽ 2 metric khác đơn vị (`CPUUtilization`, `EBSWriteBytes`) trên cùng đồ thị với 2 trục Y.
* Thêm được horizontal/vertical annotation để đánh dấu ngưỡng và mốc sự kiện trên đồ thị.
* Sử dụng metric math: `SEARCH` gom metric hàng loạt theo điều kiện, `SORT` xếp hạng top N instance theo giá trị — kết hợp thành đồ thị tự cập nhật khi có máy mới.
* Đọc được metric mức hệ điều hành (`procstat_memory_rss`) do CloudWatch Agent thu thập trong namespace `CWAgent`.
* Tạo được dynamic label với `${PROP('Dim.…')}` và hiểu đúng vị trí áp dụng của cú pháp này sau khi tự xử lý lỗi Invalid syntax.
* Cấu hình retention 1 tuần cho log group `/ec2/linux/var/log/messages` để kiểm soát chi phí lưu trữ log.
* Tạo metric filter `PythonAppErrors` biến dòng log ERROR thành metric đếm được.
* Tạo alarm `PythonApplicationErrorAlarm` (ERROR > 10 trong 1 phút) gắn SNS topic `Error_logs_reach_10`, xác nhận subscription email thành công.
* Thêm widget alarm vào dashboard `CloudWatch-Workshop` và biết cách lưu dashboard đúng cách.

### Khó khăn gặp phải:

* **Email xác nhận SNS rơi vào mục Spam**: thư "Subscription Confirmation" của AWS bị Gmail xếp vào Thư rác; nếu không chủ động kiểm tra thì subscription mãi ở trạng thái pending và alarm không gửi được thông báo nào.
* **Dynamic label báo Invalid syntax**: tôi dán cú pháp `${PROP(…)}` vào ô math expression nên biểu thức lỗi; phải hiểu ra đây là cú pháp của phần Label chứ không phải hàm metric math thì đồ thị mới hiển thị đúng.
* **Dashboard không tự lưu**: mặc định Autosave: Off, widget thêm vào sẽ mất nếu quên bấm Save dashboard — console có cảnh báo nhưng khá dễ bỏ qua.

### Bài học rút ra:

Tuần 5 thay đổi góc nhìn của tôi từ "dựng hệ thống" sang "quan sát hệ thống": hạ tầng tốt đến đâu cũng cần biết nó đang khỏe hay ốm. Điều ấn tượng nhất là chuỗi chuyển hóa **log → metric filter → alarm → SNS → email**: một dòng chữ ERROR trong file log trở thành con số, con số vượt ngưỡng trở thành cảnh báo, và cảnh báo đến thẳng hộp thư của người vận hành. Metric math (`SEARCH`, `SORT`) cũng cho thấy cách giám sát "theo quy tắc" thay vì "theo danh sách" — đồ thị tự gom máy mới mà không cần chỉnh tay. Nếu làm lại, tôi sẽ xác nhận subscription SNS ngay khi tạo alarm (và kiểm tra cả mục Spam) trước khi kiểm thử điều kiện cảnh báo.

### Kết luận:

Tuần 5 khép lại với một hệ giám sát hoàn chỉnh trên CloudWatch: hạ tầng dựng tự động bằng CloudFormation, metric được vẽ và xếp hạng bằng metric math, log ứng dụng được chuyển thành alarm gửi email qua SNS, và tất cả tập trung trên dashboard `CloudWatch-Workshop`. Không đưa thông tin nhạy cảm như AWS Account ID, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
