---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Comprehensively test the system integrated in Week 10: connectivity, routing, recovery, and alerting.
* Perform a **restore drill** — rehearse recovery from an AWS Backup recovery point to make sure the backup is usable.
* Review **security** by the least-privilege principle: tighten Security Groups, check IAM roles/policies, close unused ports.
* **Optimize cost and configuration**: right-size EC2, set reasonable retention for logs/backups, clean up leftover resources.
* Fix the issues found and rerun regression tests to confirm the system is stable.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Test connectivity and routing across the whole system <br> - Use Reachability Analyzer, ping, and check peering/Transit Gateway routes <br> - Record the issues found | 29/06/2026 | 29/06/2026 | Internal compilation |
| 3   | - Rehearse recovery (restore drill) from an AWS Backup recovery point <br> - Test the alarm/SNS by creating a threshold-breach scenario | 30/06/2026 | 30/06/2026 | Internal compilation |
| 4   | - Review security: tighten Security Groups by least privilege <br> - Check IAM roles/policies, remove excess permissions, close unused ports | 01/07/2026 | 01/07/2026 | Internal compilation |
| 5   | - Optimize cost: right-size EC2, set retention for CloudWatch Logs/Backup <br> - Clean up Elastic IPs, NAT Gateways, and leftover resources from old labs | 02/07/2026 | 02/07/2026 | Internal compilation |
| 6   | - Fix the issues found during the week <br> - Rerun regression tests, confirm the system is stable | 03/07/2026 | 03/07/2026 | Internal compilation |

### Detailed work performed:

> **Note:** This week focused on testing, security review, and configuration optimization, so there are no illustrations for this week.

#### Monday — Testing connectivity and routing across the whole system

At the start of the week, I tested systematically rather than randomly. I reused **Reachability Analyzer** (Week 2) to analyze the path between the application tier and the database tier, confirming that traffic reached the correct destination. I also checked the routes of VPC Peering (Week 7) and Transit Gateway (Week 8) — cross-referencing route tables to be sure there were no excess or missing routes. A few points that were not yet reachable (e.g., private ping across VPCs) were recorded in an issue list to be handled on Friday, rather than fixed hastily.

#### Tuesday — Recovery drill and alerting test

A backup is only valuable if it can be restored, so I performed a **restore drill**: selecting a recovery point in AWS Backup (Week 6) and restoring it into a new resource, then checking the data/boot capability of the restored resource. Next, I tested **alerting**: deliberately creating a threshold-breach scenario so the CloudWatch alarm would change state, and confirming that the notification was sent via SNS to email (exactly the chain built in Week 5). "Breaking things in a controlled way" gave me confidence that when a real incident occurs, the system will alert and there is a path to recovery.

#### Wednesday — Security review by least privilege

I reviewed all **Security Groups** by the least-privilege principle learned in Week 2–3: removing overly broad rules (e.g., opening SSH/RDP to `0.0.0.0/0`), keeping only the truly necessary sources, and preferring connectivity via **Session Manager** rather than exposing admin ports to the internet. For IAM, I checked the roles/policies I had created (such as `AdminRole`, `Windows-Lab-SSM-Role`), confirmed each role held only the necessary permissions, and removed the excess ones. This was the step of turning "it runs" into "it runs safely".

#### Thursday — Cost and configuration optimization

Moving on to optimization, I reviewed cost based on the credit-saving habit formed in the early weeks. I **right-sized** EC2 to just-enough types (`t3.micro`/`t3.nano` for light workloads), set reasonable **retention** for CloudWatch Logs and for AWS Backup recovery points to avoid costly indefinite storage (the retention lesson from Week 5). I also cleaned up leftover resources from old labs: releasing unattached **Elastic IPs**, deleting unused **NAT Gateways**, and stopping/terminating instances not serving the main system — the things that quietly incur cost.

#### Friday — Bug fixing and regression testing

On the last day of the week, I handled the issues recorded at the start of the week one by one: adding the missing route for private cross-VPC connectivity, correcting Security Group rules to the right sources, and fixing inconsistent configurations. After each fix, I reran the **regression tests** on the flows checked on Monday–Tuesday to make sure the fixes did not break what was already working. By the end of the week, the issue list was closed and the system was in a stable state, ready for documentation and handover in Week 12.

### Difficulties encountered:

* **Private cross-VPC connectivity was still not working from previous weeks**: as identified in Week 7, peering/Transit Gateway was active but private traffic still could not pass.
  * **Resolution:** add a route pointing to the peer VPC's range in the route table and open the Security Group for the correct source range, then re-verify with ping/Reachability Analyzer.
* **Hard to distinguish real errors from minor warnings during testing**: some test results returned ambiguous states (e.g., ICMP blocked at the destination).
  * **Resolution:** apply the Week 2 lesson — verify with multiple destinations and multiple tools (ping, Reachability Analyzer) before concluding, to avoid fixing the wrong place.
* **Leftover resources incurring hidden cost**: unattached Elastic IPs and forgotten NAT Gateways still incur charges.
  * **Resolution:** use the Week 10 inventory table to review each resource, release the unused ones, and record the reason for keeping/deleting.

### Week 11 Achievements:

* Comprehensively tested the system's connectivity and routing (Reachability Analyzer, ping, peering/Transit Gateway routes) and produced an issue list.
* Successfully rehearsed recovery from an AWS Backup recovery point and tested the alarm → SNS alerting via a threshold-breach scenario.
* Reviewed security by least privilege: tightened Security Groups, closed unnecessary admin ports, removed excess IAM permissions.
* Optimized cost and configuration: right-sized EC2, set retention for logs/backups, cleaned up Elastic IPs/NAT Gateways and leftover resources.
* Finished fixing the issues found and ran regression tests, returning the system to a stable state.

### Knowledge gained:

Week 11 gave me an experience of the least glamorous yet most important phase of a project: **testing, hardening, and optimizing**. I understood that "it runs" and "it runs well" are two different levels — between them lie recovery testing, security tightening, and waste reduction. The restore drill taught me that an untested backup cannot be trusted; the least-privilege review showed that security is a proactive act of removing rather than adding; and cost optimization reminded me that every forgotten resource has its price. Equally important was the discipline of **recording issues before fixing** and **regression testing after every change** — a practice that improves the system without accidentally breaking what already works.

### Weekly summary:

Week 11 raised the integrated system from Week 10 to a reliable level: connectivity and routing were thoroughly tested, recovery and alerting were rehearsed in practice, security was tightened by least privilege, and cost was optimized through right-sizing and cleaning up leftover resources. Every issue found was fixed and regression-tested, returning the system to a stable state — ready for Week 12 to review documentation, evaluate results, and hand over. No sensitive information such as the AWS Account ID, full email address or account-identifying ARNs is included in this report.
