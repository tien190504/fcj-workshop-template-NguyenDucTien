---
title: "Worklog Tuần 6"
date: 2026-05-25
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Làm quen với **AWS Backup** — dịch vụ sao lưu (backup) và phục hồi (restore) tập trung của AWS cho nhiều loại tài nguyên.
* Chuẩn bị môi trường thực hành: kiểm tra cấu hình AWS CLI, tạo S3 bucket và tải các file lab (`backup-lab.yaml`, `lambda_function.zip`) lên bucket.
* Dựng hạ tầng workshop bằng **CloudFormation** stack `Buckup-plan` và kiểm chứng ứng dụng web chạy trên EC2.
* Tìm hiểu thành phần của một **Backup plan**: backup rule, backup vault và resource assignment.
* Thiết lập thông báo cho AWS Backup: đăng ký SNS topic và cấu hình `backup-vault-notifications` qua AWS CLI.
* Thực hành tạo **backup job** cho EC2 instance và **restore** recovery point thành instance mới.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Tìm hiểu tổng quan AWS Backup <br> - Kiểm tra cấu hình AWS CLI (`~/.aws`, profile, region) <br> - Tạo S3 bucket `aws-backup-plan-bucket-2026-by-tien-nguyen` <br> - Tải file lab `backup-lab.yaml` và `lambda_function.zip` lên bucket | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Dùng CloudFormation dựng stack `Buckup-plan` từ template `backup-lab.yaml` <br> - Theo dõi quá trình tạo tài nguyên (VPC, Subnet, EC2…) <br> - Kiểm chứng ứng dụng web chạy trên EC2 public IP | 26/05/2026 | 26/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Xem lại **Backup plan** `Buckup-plan` do stack tạo ra <br> - Tìm hiểu backup rule `BACKUP-LAB-RULE`, backup vault mặc định và resource assignment `BACKUP-RESOURCES` | 27/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Kiểm tra SNS topic `BackupNotificationTopic-Buckup-plan` và các subscription <br> - Dùng AWS CLI cấu hình `put-backup-vault-notifications` cho vault `BACKUP-LAB-VAULT` với sự kiện `BACKUP_JOB_COMPLETED`, `RESTORE_JOB_COMPLETED` | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Tạo backup job on-demand cho EC2 instance vào vault `BACKUP-LAB-VAULT` <br> - Theo dõi backup job đến trạng thái Completed <br> - Restore recovery point thành EC2 instance mới | 29/05/2026 | 29/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Nội dung thực hiện chi tiết:

#### Thứ 2 — Chuẩn bị môi trường: AWS CLI, S3 bucket và file lab

Trước khi bắt đầu bài lab AWS Backup, tôi kiểm tra lại cấu hình **AWS CLI** trên máy. Vào thư mục `~/.aws`, tôi thấy có `config` và `credentials`; lệnh `cat config` cho thấy hai profile `default` và `devops` đều dùng region `ap-southeast-1` và output `json`. Chạy `aws configure list` xác nhận access key / secret key đã được nạp từ shared credentials file và region mặc định là `ap-southeast-1`.

> **Ảnh minh chứng:**
> ![Kiểm tra cấu hình AWS CLI: cat config và aws configure list, region ap-southeast-1](/images/1.6-Week6/verify-aws-cli-config.png)

Tiếp theo, tôi tạo một **S3 bucket** để chứa file lab. Bucket `aws-backup-plan-bucket-2026-by-tien-nguyen` được tạo thành công tại Region `ap-southeast-1` — banner xanh báo "Successfully created bucket", danh sách Objects đang trống (0 object).

> **Ảnh minh chứng:**
> ![Tạo S3 bucket aws-backup-plan-bucket-2026-by-tien-nguyen thành công, bucket rỗng](/images/1.6-Week6/create-s3-bucket.png)

Sau đó tôi tải hai file lab lên bucket vào prefix `Backup plan/`: `backup-lab.yaml` (template CloudFormation, 7 KB) và `lambda_function.zip` (mã Lambda, 2.5 KB). Trang Upload: status báo **Upload succeeded** — 2 files, 9.5 KB, 100%, cả hai đều ở trạng thái Succeeded.

> **Ảnh minh chứng:**
> ![Tải backup-lab.yaml và lambda_function.zip lên S3 thành công, 2 files 9.5 KB](/images/1.6-Week6/upload-lab-files-to-s3.png)

#### Thứ 3 — Dựng hạ tầng bằng CloudFormation và kiểm chứng ứng dụng web

Từ template `backup-lab.yaml` vừa tải lên, tôi dùng **CloudFormation** tạo stack `Buckup-plan` tại Region `ap-southeast-1`. Tab Events hiển thị stack đang ở trạng thái **CREATE_IN_PROGRESS**, lần lượt tạo ra các tài nguyên mạng như `Subnet`, `RouteTableAssociation`, `InternetGateway`… — nghĩa là template dựng cả một hạ tầng VPC kèm EC2 chỉ bằng một lần chạy.

> **Ảnh minh chứng:**
> ![Stack Buckup-plan đang CREATE_IN_PROGRESS, tạo các tài nguyên VPC/Subnet](/images/1.6-Week6/deploy-cloudformation-stack.png)

Khi stack hoàn tất, EC2 instance mà nó tạo ra đã chạy sẵn một ứng dụng web. Mở public IP `54.254.135.176` của instance trên trình duyệt, trang hiển thị dòng chữ **"AWS Backup is Awesome!!"** — xác nhận ứng dụng đã hoạt động và đây chính là tài nguyên tôi sẽ đem đi sao lưu ở các bước sau.

> **Ảnh minh chứng:**
> ![Ứng dụng web trên EC2 hiển thị "AWS Backup is Awesome!!" tại public IP](/images/1.6-Week6/verify-web-app-running.png)

#### Thứ 4 — Tìm hiểu thành phần của Backup plan

Stack CloudFormation không chỉ dựng EC2 mà còn tạo sẵn một **Backup plan** tên `Buckup-plan`. Vào **AWS Backup → Backup plans**, tôi xem chi tiết plan này: phần **Backup rules** có 1 rule `BACKUP-LAB-RULE` gắn với backup vault **Default**; phần **Resource assignments** có 1 assignment `BACKUP-RESOURCES` sử dụng IAM role `AWSBackupDefaultServiceRole` để xác định tài nguyên nào sẽ được sao lưu theo plan. Mục Advanced backup settings cho thấy Windows VSS đang Disabled, còn S3 backup (Back up ACLs, object tags) được Enabled.

> **Ảnh minh chứng:**
> ![Chi tiết Backup plan Buckup-plan: rule BACKUP-LAB-RULE và assignment BACKUP-RESOURCES](/images/1.6-Week6/backup-plan-details.jpg)

#### Thứ 5 — Thiết lập thông báo: SNS subscription và backup-vault-notifications qua CLI

Để nhận thông báo khi có sự kiện backup/restore, tôi kiểm tra **SNS topic** `BackupNotificationTopic-Buckup-plan` (loại Standard). Tab Subscriptions liệt kê 2 subscription: một protocol **LAMBDA** ở trạng thái **Confirmed** (hàm Lambda xử lý thông báo) và một protocol **EMAIL** trỏ tới hộp thư của tôi, còn ở trạng thái **Pending confirmation** — cần xác nhận qua email thì mới nhận được thông báo.

> **Ảnh minh chứng:**
> ![SNS topic BackupNotificationTopic-Buckup-plan với 2 subscription: Lambda (Confirmed) và Email (Pending)](/images/1.6-Week6/sns-topic-subscriptions.png)

Tiếp đó, tôi dùng **AWS CLI** để gắn thông báo trực tiếp vào backup vault. Ban đầu lệnh `aws sts get-caller-identity` báo lỗi *"Provided region_name doesn't match a supported format"* do giá trị region trong file config bị kẹp dấu `*`; sau khi chạy lại `aws configure` để nhập đúng `ap-southeast-1`, lệnh trả về thông tin tài khoản bình thường. Sau đó tôi chạy `aws backup put-backup-vault-notifications` cho vault `BACKUP-LAB-VAULT` với hai sự kiện `BACKUP_JOB_COMPLETED` và `RESTORE_JOB_COMPLETED`, trỏ tới SNS topic ở trên; lệnh `aws backup get-backup-vault-notifications` xác nhận cấu hình đã được áp dụng đúng.

#### Thứ 6 — Tạo backup job cho EC2 và restore recovery point

Ngày cuối tuần, tôi thực hành sao lưu thật. Tạo một **backup job** on-demand cho EC2 instance `i-03543b0370c6ade54` vào vault `BACKUP-LAB-VAULT`. Trang Jobs hiển thị banner "Backup in progress for instance/…", backup job mới ở trạng thái **Created**, resource type **EC2** — job có thể mất vài phút đến vài giờ tùy kích thước tài nguyên.

> **Ảnh minh chứng:**
> ![Backup job cho EC2 instance đang chạy, trạng thái Created trong vault BACKUP-LAB-VAULT](/images/1.6-Week6/backup-job-in-progress.png)

Sau khoảng 10 phút, backup job chuyển sang **Completed**. Trang chi tiết cho biết: recovery point là một **AMI** (`ami-02df082f4a7bbffd5`), resource name `WA-Lab-Instance`, backup size **8 GiB**, recovery point retention **35 ngày**, IAM role là Default role. Đây là bản chụp đầy đủ của EC2 instance, sẵn sàng cho việc khôi phục.

> **Ảnh minh chứng:**
> ![Backup job Completed: recovery point là AMI, backup size 8 GiB, retention 35 ngày](/images/1.6-Week6/backup-job-completed.png)

Cuối cùng, tôi thực hiện **restore** từ recovery point vừa tạo. Restore job hoàn tất với trạng thái **Completed**: từ recovery point `ami-02df082f4a7bbffd5`, AWS Backup phục hồi thành một EC2 instance **mới** có ID `i-0ec6184b15bf8ed52` (khác instance gốc), backup size 8 GiB. Như vậy toàn bộ vòng đời sao lưu — phục hồi đã được kiểm chứng đầu-cuối.

> **Ảnh minh chứng:**
> ![Restore job Completed: khôi phục recovery point thành EC2 instance mới i-0ec6184b15bf8ed52](/images/1.6-Week6/restore-job-completed.png)

### Kết quả đạt được tuần 6:

* Hiểu vai trò của AWS Backup như một dịch vụ sao lưu/phục hồi **tập trung**: một backup plan điều phối backup rule, backup vault và resource assignment.
* Chuẩn bị được môi trường lab: kiểm tra cấu hình AWS CLI, tạo S3 bucket `aws-backup-plan-bucket-2026-by-tien-nguyen` và tải lên các file `backup-lab.yaml`, `lambda_function.zip`.
* Dựng hạ tầng workshop bằng CloudFormation stack `Buckup-plan` và kiểm chứng ứng dụng web ("AWS Backup is Awesome!!") chạy trên EC2.
* Đọc hiểu chi tiết Backup plan `Buckup-plan`: rule `BACKUP-LAB-RULE`, resource assignment `BACKUP-RESOURCES` với `AWSBackupDefaultServiceRole`.
* Thiết lập thông báo cho AWS Backup: kiểm tra subscription SNS topic `BackupNotificationTopic-Buckup-plan` và cấu hình `backup-vault-notifications` cho vault `BACKUP-LAB-VAULT` qua AWS CLI với sự kiện `BACKUP_JOB_COMPLETED`, `RESTORE_JOB_COMPLETED`.
* Tạo được backup job on-demand cho EC2 instance, theo dõi đến trạng thái Completed với recovery point dạng AMI (8 GiB, retention 35 ngày).
* Restore thành công recovery point thành một EC2 instance mới, hoàn tất vòng đời backup → restore.

### Khó khăn gặp phải:

* **AWS CLI báo lỗi định dạng region**: `aws sts get-caller-identity` trả về *"Provided region_name doesn't match a supported format"* vì giá trị region trong file config bị kẹp dấu `*`; phải chạy lại `aws configure` nhập đúng `ap-southeast-1` thì lệnh mới hoạt động.
* **Email subscription ở trạng thái Pending confirmation**: subscription email của SNS topic chưa được xác nhận, nếu quên bấm confirm thì sẽ không nhận được thông báo backup/restore nào.
* **Backup job cần thời gian**: console cảnh báo backup có thể mất vài giờ tùy kích thước tài nguyên; thực tế job EC2 8 GiB mất khoảng 10 phút mới chuyển sang Completed nên cần kiên nhẫn theo dõi.

### Bài học rút ra:

Tuần 6 cho tôi cái nhìn hoàn chỉnh về khả năng **chống mất mát dữ liệu** trên AWS. Điều ấn tượng nhất là cách AWS Backup gom mọi thứ về một nơi: thay vì tự chụp snapshot từng dịch vụ, tôi chỉ cần một backup plan gắn rule + vault + resource assignment là hệ thống tự lo phần còn lại. Chuỗi thao tác **backup job → recovery point (AMI) → restore thành instance mới** chứng minh rằng bản sao lưu chỉ thực sự có giá trị khi restore được — nên việc kiểm thử phục hồi cũng quan trọng như việc tạo backup. Bên cạnh đó, việc cấu hình thông báo qua SNS (kết hợp cả Lambda và email) và qua AWS CLI (`put-backup-vault-notifications`) giúp tôi hiểu có nhiều cách để tự động cảnh báo khi sự kiện backup/restore hoàn tất.

### Kết luận:

Tuần 6 khép lại với một quy trình sao lưu — phục hồi hoàn chỉnh trên AWS Backup: hạ tầng được dựng tự động bằng CloudFormation, backup plan điều phối việc sao lưu EC2 vào vault `BACKUP-LAB-VAULT`, thông báo được gắn qua SNS và AWS CLI, và recovery point được restore thành công thành instance mới. Không đưa thông tin nhạy cảm như AWS Account ID, Access Key/Secret Key, địa chỉ email đầy đủ hay ARN chứa định danh tài khoản vào nội dung báo cáo.
