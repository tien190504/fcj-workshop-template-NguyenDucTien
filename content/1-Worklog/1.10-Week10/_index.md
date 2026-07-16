---
title: "Week 10 - Finalizing and integrating the whole system"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

* Enter the final phase of the internship: **finalize and integrate** the scattered pieces built over the past 9 weeks into a unified system.
* Review and inventory all resources created across the weeks; redraw the overall architecture diagram.
* Standardize the infrastructure using **Infrastructure as Code (CloudFormation)** and consistent naming/tagging conventions.
* Integrate: redeploy the **AWS FCJ Management** application (EC2 + RDS) in a standard VPC with role-appropriate Security Groups.
* Attach **CloudWatch** monitoring (dashboard, alarm + SNS) and bring the instance under **Systems Manager** management.
* Enable **AWS Backup** for EC2/RDS via a backup plan and run an end-to-end test of the whole integrated system.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Review all resources created from Week 1–9 <br> - Inventory the VPCs, EC2, RDS, S3, IAM… <br> - Redraw the overall system architecture diagram | 22/06/2026 | 22/06/2026 | Internal compilation |
| 3   | - Standardize the infrastructure with CloudFormation templates <br> - Apply consistent naming conventions and tags to resources | 23/06/2026 | 23/06/2026 | Internal compilation |
| 4   | - Redeploy the AWS FCJ Management application on EC2 in the standard VPC <br> - Connect the application to the RDS database <br> - Attach role-appropriate app/db Security Groups | 24/06/2026 | 24/06/2026 | Internal compilation |
| 5   | - Create a CloudWatch dashboard and alarms for EC2/RDS <br> - Attach an SNS notification action <br> - Bring EC2 into Systems Manager (managed node) | 25/06/2026 | 25/06/2026 | Internal compilation |
| 6   | - Configure an AWS Backup plan for EC2/RDS <br> - Run an end-to-end test of the whole integrated system flow | 26/06/2026 | 26/06/2026 | Internal compilation |

### Detailed work performed:

> **Note:** This week focused on reviewing, integrating, and standardizing existing infrastructure, so there are no illustrations for this week.

#### Monday — Reviewing resources and redrawing the overall architecture

At the start of the week, I spent time looking back over the whole 9-week journey rather than creating new resources. I reviewed and inventoried what had been built: the VPCs (`ASG`, `Windows-vpc`, `Linux-vpc`, `windows-lab-ssm-vpc`…), the EC2 instances, the RDS `first-cloud-db-instance`, the S3 buckets, the IAM users/roles, along with advanced networking components like VPC Peering and Transit Gateway. Listing them into an inventory table helped me clearly see which resources were still in use and which were "leftovers" from old labs. From that inventory, I redrew the **overall architecture diagram**: a standard VPC containing the AWS FCJ Management application (EC2) and the database (RDS), monitored by CloudWatch, backed up by AWS Backup, and managed by Systems Manager.

#### Tuesday — Standardizing the infrastructure with Infrastructure as Code

From Week 5 onward I had been using CloudFormation to provision infrastructure, but many resources in the early weeks were still created by hand in the Console. On this day, I gathered the frequently repeated manual steps (VPC, Subnet, Route Table, Security Group) into a **CloudFormation template** so the infrastructure could be reproduced consistently. I also unified the **naming conventions** and **tags** (e.g., `Project`, `Environment`, `Owner`) for resources — something the previous weeks did inconsistently, which made lookups difficult. This was the step of turning scattered experience into a repeatable mold.

#### Wednesday — Integrating the AWS FCJ Management application with RDS in a standard VPC

I redeployed the **AWS FCJ Management** application (familiar since Week 3 and Week 4) onto an EC2 instance inside the standard VPC, and connected the application to the **RDS** database instead of a local database. The Security Groups were attached following the tiered model learned in Week 2–4: the application tier opens the web port and port 5000, while the database tier only accepts MySQL connections (3306) from the application tier's Security Group. A quick test of the CRUD user flow (List/Add/Edit) showed the application reading/writing data correctly from RDS.

#### Thursday — Attaching CloudWatch monitoring and bringing it into Systems Manager

To make the system "observable", I built a **CloudWatch dashboard** consolidating the important metrics of EC2 (CPU) and RDS, and created **alarms** for risky thresholds with an **SNS** action to receive alerts by email — reusing exactly the log/metric/alarm/SNS chain built in Week 5. Then I attached an appropriate IAM Role to bring EC2 into **Systems Manager** as a managed node (the technique from Week 9), so that patching and running remote commands would later be possible without opening RDP/SSH.

#### Friday — Configuring AWS Backup and end-to-end testing

At the end of the week, I enabled **AWS Backup** for EC2 and RDS with a backup plan (reusing the Week 6 knowledge): attaching a resource assignment and backup rule so the system automatically creates recovery points on a schedule. Then I ran a full **end-to-end test**: accessing the application via the browser, manipulating data to confirm the app–RDS path was clear, checking that the alarms/dashboard reflected the correct state, and confirming the instance was present in Systems Manager. At this point, the scattered pieces of 9 weeks had come together into a system with a clear architecture: **standard network → application + database → monitoring → backup → centralized management**.

### Difficulties encountered:

* **Scattered and duplicated resources across the weeks**: after 9 weeks, the account held many VPCs/instances/security groups from different labs, making it hard to tell which were still in use.
  * **Resolution:** create an inventory table and apply consistent tags to classify them; clearly mark "in use" resources versus "to be cleaned up" resources for Week 11.
* **The application did not connect to RDS immediately**: at first the application could not reach the database because the db-tier Security Group did not allow the correct source.
  * **Resolution:** reapply the tiered model — only allow port 3306 from the application tier's Security Group, rather than opening it to the whole network range.
* **Bringing the instance into Systems Manager requires the right permissions**: the newly deployed instance did not appear as a managed node.
  * **Resolution:** attach an IAM Role with SSM permissions and wait for the node to switch to the Online state, exactly as learned in Week 9.

### Week 10 Achievements:

* Reviewed and inventoried all resources created from Week 1–9; redrew the overall architecture diagram.
* Standardized the infrastructure with CloudFormation templates plus consistent naming conventions and tags.
* Redeployed the AWS FCJ Management application on EC2 in the standard VPC, successfully connecting to RDS with role-appropriate Security Groups.
* Built CloudWatch dashboards/alarms for EC2/RDS with SNS notifications; brought EC2 into Systems Manager as a managed node.
* Enabled an AWS Backup plan for EC2/RDS and successfully ran an end-to-end test of the whole integrated system.

### Knowledge gained:

Week 10 taught me that mastering each individual service is only half the job; the other half is **assembling them into a coherent system**. When placed side by side, the scattered pieces of learning suddenly revealed their relationships: the network (Week 2) is the foundation for compute (Week 3) and the database (Week 4); monitoring (Week 5) and backup (Week 6) are two protective loops; and Systems Manager (Week 9) is the operational layer on top. I also came to appreciate the value of **Infrastructure as Code** and **consistent naming/tagging** — things that seem small but determine whether a system can be maintained. Overall, good architecture is not about having many services, but about having services arranged in the right roles and connected the right way.

### Weekly summary:

Week 10 opened the final phase by turning 9 weeks of scattered knowledge into a complete system: network infrastructure standardized with CloudFormation, the AWS FCJ Management application running on EC2 connected to RDS, monitored with CloudWatch + SNS, backed up with AWS Backup, and centrally managed with Systems Manager. The integrated system ran end to end, creating a solid foundation to move into Week 11 focused on testing, debugging, and optimization. No sensitive information such as the AWS Account ID, full email address or account-identifying ARNs is included in this report.
