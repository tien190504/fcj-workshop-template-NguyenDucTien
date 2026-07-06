---
title: "Week 1 Worklog"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---



### Week 1 Objectives:

* Become familiar with AWS Management Console and basic tasks for checking account information, Region, and Billing/Credits.
* Learn IAM at a basic level, including user, group, role, policy, and access permission errors.
* Practice basic Amazon S3 operations: create a bucket and upload an object.
* Try launching an EC2 instance, record the invalid AMI error by Region, and learn from the AMI selection process.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Sign in to AWS Management Console <br> - Check the active Region <br> - Check account information and the Billing/Credits section | 20/04/2026 | 20/04/2026 | <https://000001.awsstudygroup.com/vi/> |
| 3   | - Learn about IAM user groups and IAM users <br> - Create `AdminGroup` <br> - Create/check `AdminUser` and `OperatorUser` | 21/04/2026 | 21/04/2026 | <https://000002.awsstudygroup.com/vi/> |
| 4   | - Create IAM role `AdminRole` <br> - Attach policy `AdministratorAccess` <br> - Check Switch Role and the `AccessDenied` error when a user lacks permissions | 22/04/2026 | 22/04/2026 | <https://000002.awsstudygroup.com/vi/> |
| 5   | - Create S3 bucket `aws-s3-demo-tien` in Region `ap-southeast-1` <br> - Upload file `download.jpg` to the bucket <br> - Check the successful upload status | 23/04/2026 | 23/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/> |
| 6   | - Try launching an EC2 instance in Region `ap-southeast-2` <br> - Choose instance type `t3.micro` <br> - Record the invalid AMI ID error and learn to choose an AMI for the correct Region | 24/04/2026 | 24/04/2026 | <https://000001.awsstudygroup.com/vi/> |

### Detailed Implementation:

#### AWS Console and Credits

On the first day, I signed in to AWS Management Console to become familiar with the AWS service management interface. I checked the active Region, observed the Console Home area, and accessed the Billing/Credits section to understand the remaining credits, used credits, and active credits.

> **Evidence images:**

> ![AWS Console Home](/images/1.1-Week1/aws-console-home.png)

> ![AWS Credits](/images/1.1-Week1/aws-credit.png)

#### IAM User Group, User and Role

I practiced with the IAM service to understand how AWS manages identities and permissions. I created the user group `AdminGroup`, checked the IAM user list, created users such as `AdminUser` and `OperatorUser`, and then monitored the number of users displayed in the IAM console.

Next, I created the IAM role `AdminRole` and attached the `AdministratorAccess` policy so the role would have administrative permissions. In the role details section, I checked that the policy had been attached and verified that the role appeared in the Roles list.

> **Evidence images:**
> ![AdminGroup](/images/1.1-Week1/admin-group.png)
> ![AdminUserAndOperatorUser](/images/1.1-Week1/admin-user-operator-user.png)
> ![OperatorUser](/images/1.1-Week1/admin-role.png)
> ![AdminRoleDetails](/images/1.1-Week1/admin-role-details.png)


#### Access Permission Check and Switch Role

When using `OperatorUser`, I encountered the `AccessDenied` error when accessing some features such as listing IAM users or viewing information that required higher permissions. After that, I checked the Switch Role mechanism to switch to `AdminRole` and observe the difference between a regular user and a role with administrative permissions.

Through this section, I understood that an IAM user can only perform an action when a policy allows it directly or through an appropriate group/role. If permissions are missing, AWS Console displays a specific error for the denied action.

> **Evidence images:**
> ![AccessDenied](/images/1.1-Week1/access-denied.png)
> ![OperatorUser](/images/1.1-Week1/operator-user.png)

> ![SwitchRole](/images/1.1-Week1/switch-role.png)
> ![AdminRole](/images/1.1-Week1/admin-role-console.png)

#### S3 bucket and object upload

I switched to Amazon S3 to practice object storage. I created the bucket `aws-s3-demo-tien` in Region `ap-southeast-1`, then uploaded the file `download.jpg` to the bucket. The upload result was displayed successfully with the `Succeeded` status.

This activity helped me understand that a bucket is a place to store objects in S3, each bucket belongs to a specific Region, and it can be used to manage files as object storage.

> **Evidence images:**
> ![S3Bucket](/images/1.1-Week1/s3-bucket.png)
> ![S3Upload](/images/1.1-Week1/s3-upload.png)

### Week 1 Achievements:

* Learned basic operations in AWS Management Console, such as selecting a Region, searching for services, and checking account information.
* Learned how to check Billing/Credits to monitor remaining credits, used credits, and credit status.
* Understood IAM identity, user group, role, managed policy, and how access permissions affect actions in AWS Console.
* Created and checked `AdminGroup`, `AdminUser`, `OperatorUser`, and `AdminRole`.
* Attached the `AdministratorAccess` policy to `AdminRole` and observed the permission difference between a user and a role.
* Identified the `AccessDenied` error when an IAM user lacks permission to perform an action.
* Created the S3 bucket `aws-s3-demo-tien` in Region `ap-southeast-1`.
* Successfully uploaded the file `download.jpg` to the S3 bucket and checked the upload status.
* Learned to identify the invalid AMI error when trying to launch an EC2 instance and understood that the AMI must match the selected Region.
* Did not include sensitive information such as the AWS Account ID in the report content.
