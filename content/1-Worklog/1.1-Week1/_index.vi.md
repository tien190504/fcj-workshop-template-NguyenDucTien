---
title: "Tuần 1 - Làm quen với AWS: Console, IAM, Amazon S3 và EC2"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---



### Mục tiêu tuần 1:

* Làm quen với AWS Management Console và các thao tác kiểm tra thông tin tài khoản, Region, Billing/Credits.
* Tìm hiểu IAM ở mức cơ bản, gồm user, group, role, policy và lỗi thiếu quyền truy cập.
* Thực hành Amazon S3 cơ bản: tạo bucket và upload object.
* Thử tạo EC2 instance, ghi nhận lỗi AMI không hợp lệ theo Region và rút kinh nghiệm khi chọn AMI.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2   | - Đăng nhập AWS Management Console <br> - Kiểm tra Region đang sử dụng <br> - Kiểm tra thông tin tài khoản và khu vực Billing/Credits | 20/04/2026 | 20/04/2026 | <https://000001.awsstudygroup.com/vi/> |
| 3   | - Tìm hiểu IAM user group và IAM user <br> - Tạo `AdminGroup` <br> - Tạo/kiểm tra `AdminUser` và `OperatorUser` | 21/04/2026 | 21/04/2026 | <https://000002.awsstudygroup.com/vi/> |
| 4   | - Tạo IAM role `AdminRole` <br> - Gắn policy `AdministratorAccess` <br> - Kiểm tra Switch Role và lỗi `AccessDenied` khi user thiếu quyền | 22/04/2026 | 22/04/2026 | <https://000002.awsstudygroup.com/vi/> |
| 5   | - Tạo S3 bucket `aws-s3-demo-tien` tại Region `ap-southeast-1` <br> - Upload file `download.jpg` lên bucket <br> - Kiểm tra trạng thái upload thành công | 23/04/2026 | 23/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 6   | - Thử launch EC2 instance tại Region `ap-southeast-2` <br> - Chọn instance type `t3.micro` <br> - Ghi nhận lỗi AMI ID không hợp lệ và rút kinh nghiệm chọn AMI theo đúng Region | 24/04/2026 | 24/04/2026 | <https://000001.awsstudygroup.com/vi/> |

### Nội dung thực hiện chi tiết:

#### AWS Console và Credits

Trong ngày đầu tiên, tôi đăng nhập vào AWS Management Console để làm quen với giao diện quản trị dịch vụ AWS. Tôi kiểm tra Region đang sử dụng, quan sát khu vực Console Home và truy cập phần Billing/Credits để nắm được số credit còn lại, số credit đã sử dụng và các credit đang hoạt động.

> **Ảnh minh chứng:**

> ![AWS Console Home](/images/1.1-Week1/aws-console-home.png)

> ![AWS Credits](/images/1.1-Week1/aws-credit.png)

#### IAM User Group, User và Role

Tôi thực hành với dịch vụ IAM để hiểu cách AWS quản lý danh tính và phân quyền. Tôi tạo user group `AdminGroup`, kiểm tra danh sách IAM users, tạo các user như `AdminUser` và `OperatorUser`, sau đó theo dõi số lượng user hiển thị trong IAM console.

Tiếp theo, tôi tạo IAM role `AdminRole` và gắn policy `AdministratorAccess` để role có quyền quản trị. Qua phần chi tiết role, tôi kiểm tra policy đã được gắn và xem role xuất hiện trong danh sách Roles.

> **Ảnh minh chứng:**
> ![AdminGroup](/images/1.1-Week1/admin-group.png)
> ![AdminUserAndOperatorUser](/images/1.1-Week1/admin-user-operator-user.png)
> ![OperatorUser](/images/1.1-Week1/admin-role.png)
> ![AdminRoleDetails](/images/1.1-Week1/admin-role-details.png)


#### Kiểm tra quyền truy cập và Switch Role

Khi sử dụng `OperatorUser`, tôi gặp lỗi `AccessDenied` khi truy cập một số chức năng như liệt kê IAM users hoặc xem thông tin yêu cầu quyền cao hơn. Sau đó, tôi kiểm tra cơ chế Switch Role sang `AdminRole` để thấy sự khác biệt giữa user thông thường và role có quyền quản trị.

Qua phần này, tôi hiểu rằng một IAM user chỉ thực hiện được hành động khi có policy cho phép trực tiếp hoặc thông qua group/role phù hợp. Nếu thiếu quyền, AWS Console sẽ hiển thị lỗi cụ thể về action bị từ chối.

> **Ảnh minh chứng:**
> ![AccessDenied](/images/1.1-Week1/access-denied.png)
> ![OperatorUser](/images/1.1-Week1/operator-user.png)

> ![SwitchRole](/images/1.1-Week1/switch-role.png)
> ![AdminRole](/images/1.1-Week1/admin-role-console.png)

#### S3 bucket và upload object

Tôi chuyển sang Amazon S3 để thực hành lưu trữ object. Tôi tạo bucket `aws-s3-demo-tien` tại Region `ap-southeast-1`, sau đó upload file `download.jpg` lên bucket. Kết quả upload hiển thị thành công với trạng thái `Succeeded`.

Hoạt động này giúp tôi hiểu bucket là nơi lưu trữ object trong S3, mỗi bucket thuộc một Region cụ thể và có thể dùng để quản lý file theo dạng object storage.

> **Ảnh minh chứng:**
> ![S3Bucket](/images/1.1-Week1/s3-bucket.png)
> ![S3Upload](/images/1.1-Week1/s3-upload.png)

### Kết quả đạt được tuần 1:

* Nắm được các thao tác cơ bản trên AWS Management Console như chọn Region, tìm dịch vụ và kiểm tra thông tin tài khoản.
* Biết cách kiểm tra Billing/Credits để theo dõi credit còn lại, credit đã sử dụng và trạng thái credit.
* Hiểu khái niệm IAM identity, user group, role, managed policy và cách quyền truy cập ảnh hưởng đến thao tác trên AWS Console.
* Tạo và kiểm tra được `AdminGroup`, `AdminUser`, `OperatorUser`, `AdminRole`.
* Gắn được policy `AdministratorAccess` cho `AdminRole` và quan sát sự khác biệt quyền giữa user/role.
* Nhận diện được lỗi `AccessDenied` khi IAM user thiếu quyền thực hiện action.
* Tạo được S3 bucket `aws-s3-demo-tien` tại Region `ap-southeast-1`.
* Upload thành công file `download.jpg` lên S3 bucket và kiểm tra trạng thái upload.
* Biết nhận diện lỗi AMI không hợp lệ khi thử launch EC2 instance và hiểu cần chọn AMI phù hợp với Region.
* Không đưa thông tin nhạy cảm như AWS Account ID vào nội dung báo cáo.