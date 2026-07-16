---
title: "Week 6 - Backup and restore with AWS Backup"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Get familiar with **AWS Backup** — AWS's centralized backup and restore service for many types of resources.
* Prepare the practice environment: verify the AWS CLI configuration, create an S3 bucket, and upload the lab files (`backup-lab.yaml`, `lambda_function.zip`) to the bucket.
* Provision the workshop infrastructure with a **CloudFormation** stack `Buckup-plan` and verify that the web application runs on EC2.
* Learn the components of a **Backup plan**: backup rule, backup vault, and resource assignment.
* Set up notifications for AWS Backup: register an SNS topic and configure `backup-vault-notifications` via the AWS CLI.
* Practice creating a **backup job** for an EC2 instance and **restoring** the recovery point into a new instance.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Learn the fundamentals of AWS Backup <br> - Verify the AWS CLI configuration (`~/.aws`, profile, region) <br> - Create the S3 bucket `aws-backup-plan-bucket-2026-by-tien-nguyen` <br> - Upload the lab files `backup-lab.yaml` and `lambda_function.zip` to the bucket | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Use CloudFormation to provision the stack `Buckup-plan` from the `backup-lab.yaml` template <br> - Track the resource-creation process (VPC, Subnet, EC2…) <br> - Verify the web application running on the EC2 public IP | 26/05/2026 | 26/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Review the **Backup plan** `Buckup-plan` created by the stack <br> - Learn about the backup rule `BACKUP-LAB-RULE`, the default backup vault, and the resource assignment `BACKUP-RESOURCES` | 27/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Check the SNS topic `BackupNotificationTopic-Buckup-plan` and its subscriptions <br> - Use the AWS CLI to configure `put-backup-vault-notifications` for the `BACKUP-LAB-VAULT` vault with the `BACKUP_JOB_COMPLETED`, `RESTORE_JOB_COMPLETED` events | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Create an on-demand backup job for the EC2 instance into the `BACKUP-LAB-VAULT` vault <br> - Track the backup job to the Completed state <br> - Restore the recovery point into a new EC2 instance | 29/05/2026 | 29/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Detailed work performed:

#### Monday — Preparing the environment: AWS CLI, S3 bucket and lab files

Before starting the AWS Backup lab, I verified the **AWS CLI** configuration on my machine. In the `~/.aws` directory I found `config` and `credentials`; the `cat config` command showed two profiles, `default` and `devops`, both using the `ap-southeast-1` region and `json` output. Running `aws configure list` confirmed that the access key / secret key had been loaded from the shared credentials file and that the default region was `ap-southeast-1`.

> **Evidence:**
> ![Verifying the AWS CLI configuration: cat config and aws configure list, region ap-southeast-1](/images/1.6-Week6/verify-aws-cli-config.png)

Next, I created an **S3 bucket** to hold the lab files. The bucket `aws-backup-plan-bucket-2026-by-tien-nguyen` was created successfully in the `ap-southeast-1` Region — a green banner reported "Successfully created bucket" and the Objects list was empty (0 objects).

> **Evidence:**
> ![S3 bucket aws-backup-plan-bucket-2026-by-tien-nguyen created successfully, empty bucket](/images/1.6-Week6/create-s3-bucket.png)

I then uploaded the two lab files to the bucket under the `Backup plan/` prefix: `backup-lab.yaml` (a CloudFormation template, 7 KB) and `lambda_function.zip` (Lambda code, 2.5 KB). The Upload: status page reported **Upload succeeded** — 2 files, 9.5 KB, 100%, both in the Succeeded state.

> **Evidence:**
> ![Uploading backup-lab.yaml and lambda_function.zip to S3 successfully, 2 files 9.5 KB](/images/1.6-Week6/upload-lab-files-to-s3.png)

#### Tuesday — Provisioning infrastructure with CloudFormation and verifying the web application

From the `backup-lab.yaml` template I had just uploaded, I used **CloudFormation** to create the stack `Buckup-plan` in the `ap-southeast-1` Region. The Events tab showed the stack in the **CREATE_IN_PROGRESS** state, creating networking resources one by one such as `Subnet`, `RouteTableAssociation`, `InternetGateway`… — meaning the template provisions an entire VPC together with EC2 in a single run.

> **Evidence:**
> ![The Buckup-plan stack in CREATE_IN_PROGRESS, creating VPC/Subnet resources](/images/1.6-Week6/deploy-cloudformation-stack.png)

Once the stack completed, the EC2 instance it created was already running a web application. Opening the instance's public IP `54.254.135.176` in the browser, the page displayed the text **"AWS Backup is Awesome!!"** — confirming that the application was up and that this is the resource I would back up in the following steps.

> **Evidence:**
> ![The web application on EC2 showing "AWS Backup is Awesome!!" at the public IP](/images/1.6-Week6/verify-web-app-running.png)

#### Wednesday — Understanding the components of a Backup plan

The CloudFormation stack did not just provision EC2; it also created a **Backup plan** named `Buckup-plan`. In **AWS Backup → Backup plans**, I reviewed this plan's details: the **Backup rules** section had 1 rule `BACKUP-LAB-RULE` attached to the **Default** backup vault; the **Resource assignments** section had 1 assignment `BACKUP-RESOURCES` using the IAM role `AWSBackupDefaultServiceRole` to determine which resources are backed up by the plan. The Advanced backup settings section showed Windows VSS Disabled, while S3 backup (Back up ACLs, object tags) was Enabled.

> **Evidence:**
> ![Details of the Buckup-plan Backup plan: rule BACKUP-LAB-RULE and assignment BACKUP-RESOURCES](/images/1.6-Week6/backup-plan-details.jpg)

#### Thursday — Setting up notifications: SNS subscription and backup-vault-notifications via the CLI

To receive notifications on backup/restore events, I checked the **SNS topic** `BackupNotificationTopic-Buckup-plan` (Standard type). The Subscriptions tab listed 2 subscriptions: one **LAMBDA** protocol in the **Confirmed** state (a Lambda function processing notifications) and one **EMAIL** protocol pointing to my inbox, still in the **Pending confirmation** state — it needs email confirmation before notifications can be delivered.

> **Evidence:**
> ![SNS topic BackupNotificationTopic-Buckup-plan with 2 subscriptions: Lambda (Confirmed) and Email (Pending)](/images/1.6-Week6/sns-topic-subscriptions.png)

Next, I used the **AWS CLI** to attach notifications directly to the backup vault. At first the `aws sts get-caller-identity` command reported the error *"Provided region_name doesn't match a supported format"* because the region value in the config file was wrapped in `*` characters; after re-running `aws configure` to enter the correct `ap-southeast-1`, the command returned the account information normally. I then ran `aws backup put-backup-vault-notifications` for the `BACKUP-LAB-VAULT` vault with the two events `BACKUP_JOB_COMPLETED` and `RESTORE_JOB_COMPLETED`, pointing to the SNS topic above; the `aws backup get-backup-vault-notifications` command confirmed that the configuration had been applied correctly.

#### Friday — Creating a backup job for EC2 and restoring the recovery point

On the last day of the week, I practiced a real backup. I created an on-demand **backup job** for the EC2 instance `i-03543b0370c6ade54` into the `BACKUP-LAB-VAULT` vault. The Jobs page showed the banner "Backup in progress for instance/…", the new backup job in the **Created** state, resource type **EC2** — the job may take from a few minutes to several hours depending on the resource size.

> **Evidence:**
> ![The backup job for the EC2 instance running, in the Created state within the BACKUP-LAB-VAULT vault](/images/1.6-Week6/backup-job-in-progress.png)

After about 10 minutes, the backup job switched to **Completed**. The details page showed: the recovery point is an **AMI** (`ami-02df082f4a7bbffd5`), resource name `WA-Lab-Instance`, backup size **8 GiB**, recovery point retention **35 days**, IAM role Default role. This is a full snapshot of the EC2 instance, ready for restoration.

> **Evidence:**
> ![Backup job Completed: the recovery point is an AMI, backup size 8 GiB, 35-day retention](/images/1.6-Week6/backup-job-completed.png)

Finally, I performed a **restore** from the recovery point I had just created. The restore job finished with the **Completed** state: from the recovery point `ami-02df082f4a7bbffd5`, AWS Backup restored a **new** EC2 instance with the ID `i-0ec6184b15bf8ed52` (different from the original instance), backup size 8 GiB. This verified the entire backup–restore lifecycle end to end.

> **Evidence:**
> ![Restore job Completed: restoring the recovery point into a new EC2 instance i-0ec6184b15bf8ed52](/images/1.6-Week6/restore-job-completed.png)

### Week 6 Achievements:

* Understood the role of AWS Backup as a **centralized** backup/restore service: a single backup plan orchestrates the backup rule, backup vault, and resource assignment.
* Prepared the lab environment: verified the AWS CLI configuration, created the S3 bucket `aws-backup-plan-bucket-2026-by-tien-nguyen`, and uploaded the `backup-lab.yaml` and `lambda_function.zip` files.
* Provisioned the workshop infrastructure with the `Buckup-plan` CloudFormation stack and verified the web application ("AWS Backup is Awesome!!") running on EC2.
* Read and understood the details of the `Buckup-plan` Backup plan: the `BACKUP-LAB-RULE` rule and the `BACKUP-RESOURCES` resource assignment with `AWSBackupDefaultServiceRole`.
* Set up notifications for AWS Backup: checked the subscriptions of the SNS topic `BackupNotificationTopic-Buckup-plan` and configured `backup-vault-notifications` for the `BACKUP-LAB-VAULT` vault via the AWS CLI with the `BACKUP_JOB_COMPLETED`, `RESTORE_JOB_COMPLETED` events.
* Created an on-demand backup job for the EC2 instance and tracked it to the Completed state, with a recovery point in AMI form (8 GiB, 35-day retention).
* Successfully restored the recovery point into a new EC2 instance, completing the backup → restore lifecycle.

### Difficulties encountered:

* **The AWS CLI reported a region format error**: `aws sts get-caller-identity` returned *"Provided region_name doesn't match a supported format"* because the region value in the config file was wrapped in `*` characters; I had to re-run `aws configure` and enter the correct `ap-southeast-1` for the command to work.
* **The email subscription in the Pending confirmation state**: the SNS topic's email subscription had not been confirmed; if I forget to confirm it, no backup/restore notification will be received.
* **The backup job takes time**: the console warned that a backup may take several hours depending on resource size; in practice, the 8 GiB EC2 job took about 10 minutes to reach Completed, so patient monitoring is required.

### Lessons learned:

Week 6 gave me a complete view of AWS's **data-loss protection** capabilities. The most impressive part was how AWS Backup consolidates everything in one place: instead of snapshotting each service manually, I only need one backup plan tying together a rule + vault + resource assignment, and the system handles the rest. The chain of operations **backup job → recovery point (AMI) → restore into a new instance** proves that a backup is only truly valuable when it can be restored — so testing the restore is just as important as creating the backup. In addition, configuring notifications via SNS (combining both Lambda and email) and via the AWS CLI (`put-backup-vault-notifications`) helped me understand that there are multiple ways to automatically alert when a backup/restore event completes.

### Conclusion:

Week 6 wrapped up with a complete backup–restore workflow on AWS Backup: infrastructure provisioned automatically with CloudFormation, a backup plan orchestrating the backup of EC2 into the `BACKUP-LAB-VAULT` vault, notifications attached via SNS and the AWS CLI, and the recovery point restored successfully into a new instance. No sensitive information such as the AWS Account ID, Access Key/Secret Key, full email address or account-identifying ARNs is included in this report.
