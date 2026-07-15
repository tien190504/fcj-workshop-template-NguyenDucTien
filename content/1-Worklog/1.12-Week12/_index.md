---
title: "Week 12 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objectives:

* Close out the 12-week internship: **review documentation, evaluate results, and prepare to hand over** the product.
* Review all worklogs from Week 1–11 to ensure consistency, accuracy, and coherence.
* Write the architecture documentation and an **operational runbook**: how to deploy, monitor, back up/restore, and manage via Systems Manager.
* Summarize the knowledge from the entire course and self-evaluate the results against the original objectives.
* Prepare the product presentation/demo and **clean up resources** at the end of the term to avoid incurring costs.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Review all worklogs from Week 1–11 <br> - Check consistency of terminology, format, and links <br> - Standardize the documentation | 08/31/2026 | 08/31/2026 | Internal compilation |
| 3   | - Write the overall system architecture documentation <br> - Write the operational runbook: deploy, monitor, backup/restore, manage via SSM | 09/01/2026 | 09/01/2026 | Internal compilation |
| 4   | - Summarize the knowledge from the whole course: a map of the services learned and the connections between weeks <br> - Self-evaluate the results against the original objectives | 09/02/2026 | 09/02/2026 | Internal compilation |
| 5   | - Prepare the demo script for the AWS FCJ Management application and the infrastructure <br> - Draft the product presentation content | 09/03/2026 | 09/03/2026 | Internal compilation |
| 6   | - Present/hand over the product <br> - Clean up all AWS resources to avoid incurring costs <br> - Conclude the internship | 09/04/2026 | 09/04/2026 | Internal compilation |

### Detailed work performed:

> **Note:** This week focused on documentation review, summarizing, and handover, so there are no illustrations for this week.

#### Monday — Reviewing and standardizing all worklogs

At the start of the week, I reread the **worklogs from Week 1 to Week 11** sequentially with an editor's eye. I checked consistency of terminology (how services and resources are named), of format (headings, task tables, how images and captions are inserted), and of image paths. A few places where the wording or presentation was inconsistent across weeks were noted for standardization. This review also let me look back at the whole development arc: each week is a natural inheritance from the previous one, and the 12 weeks together form a continuous story of building and operating AWS infrastructure.

#### Tuesday — Writing the architecture documentation and operational runbook

I wrote the **overall architecture documentation** describing the system integrated in Week 10: the network diagram (VPC/Subnet/Security Group), the application tier (EC2 running AWS FCJ Management), the data tier (RDS), the monitoring layer (CloudWatch + SNS), the backup layer (AWS Backup), and the operations layer (Systems Manager). Alongside it is an **operational runbook** — a handbook of actions for common situations: how to redeploy the application, how to view the dashboard and act when an alarm fires, how to restore from a recovery point, and how to run commands/patch via Session Manager and Run Command. This is the part that turns personal experience into documentation others can reuse.

#### Wednesday — Summarizing the course knowledge and self-evaluation

I organized all the knowledge into a **service map**: the administration & identity group (IAM), storage (S3), networking (VPC and its components, VPC Peering, Transit Gateway, Site-to-Site VPN), compute (EC2 Linux/Windows), database (RDS), infrastructure as code (CloudFormation), monitoring (CloudWatch), notifications (SNS), backup (AWS Backup), and operations (Systems Manager). For each group, I recorded its connection to the weeks and its role in the final system. Then I **self-evaluated** the results against the objectives set in the early weeks: from just getting acquainted with the Console and IAM, I could now build, integrate, test, and operate a multi-tier system on my own.

#### Thursday — Preparing the demo and presentation content

I drafted a **demo script** for the presentation: opening the AWS FCJ Management application and running the CRUD user flow to show the application actually working; opening the CloudWatch dashboard to illustrate the monitoring capability; introducing the backup mechanism with AWS Backup and secure connectivity via Session Manager. In parallel, I prepared presentation content summarizing the 12-week journey, the important architectural decisions, and the lessons learned — presented so the audience grasps both the big picture and the core details.

#### Friday — Presenting, handover, and resource cleanup

On the final day, I **presented and handed over** the product based on the prepared script, together with the architecture documentation and runbook. After the handover, I performed the end-of-term **resource cleanup** following the credit-saving habit trained since the first week: deleting or stopping the EC2 instances, removing the CloudFormation stacks, releasing Elastic IPs/NAT Gateways, and deleting the network and backup resources no longer needed — to avoid incurring costs after the internship ends. At this point, the 12-week journey closed completely: from an empty AWS account to a complete system that is fully documented and handed over.

### Difficulties encountered:

* **Small inconsistencies across the weeks of documentation**: over 11 weeks, naming and presentation differed in places, making continuous reading harder.
  * **Resolution:** build a standardization checklist (terminology, heading/table format, image captions) and review each week against that checklist rather than fixing by feel.
* **The runbook tends to drift into theory**: when writing an operational handbook, there is a tendency to describe at length rather than give actionable instructions.
  * **Resolution:** write the runbook in a "when X, do Y" situational form, staying close to the real actions performed in previous weeks so the documentation is immediately usable.
* **Risk of incurring costs after the end**: if resources are forgotten, charges continue even though the internship is over.
  * **Resolution:** use the inventory table (Week 10) and the cleanup results (Week 11) to perform a systematic final cleanup, cross-checking each resource group before concluding.

### Week 12 Achievements:

* Reviewed and standardized all worklogs from Week 1–11 for terminology, format, and links.
* Wrote the overall architecture documentation and operational runbook (deploy, monitor, backup/restore, manage via SSM).
* Summarized the course knowledge into a service map and self-evaluated the results against the original objectives.
* Fully prepared the demo script and product presentation content.
* Presented/handed over the product and cleaned up the AWS resources at the end of the term to avoid incurring costs.

### Knowledge gained:

Week 12 closed the internship with skills that are often underrated but decide the real value of a project: **documentation, summarizing, and handover**. I realized that no matter how good a system is, it is only useful if others can understand and operate it again — so the runbook and architecture documentation are as important as the system itself. Looking back at the 12 weeks as a service map showed me the big picture clearly: AWS services do not exist in isolation but complement each other layer by layer — network, compute, data, monitoring, backup, and operations. Finally, the habit of **cleaning up resources** reminded me that responsibility for cost and resources extends all the way to when the project closes, not just while building it.

### Weekly summary:

Week 12 completed the 12-week internship: all worklogs were reviewed and standardized, the architecture documentation and operational runbook were fully written, the course knowledge was summarized into a service map, the product was presented and handed over, and the AWS resources were cleaned up neatly. Looking back over the whole journey — from the first actions on the Console and IAM in Week 1 to a multi-tier system integrated, tested, optimized, and documented in Week 12 — I clearly see the growth in both knowledge and operational mindset on the AWS platform. No sensitive information such as the AWS Account ID, full email address or account-identifying ARNs is included in this report.
