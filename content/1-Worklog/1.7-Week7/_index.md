---
title: "Week 7 Worklog"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* Learn about **VPC Peering** — the mechanism for private connectivity between two VPCs in AWS.
* Use **CloudFormation** to provision two independent VPCs: `My-VPC-Stack` (CIDR `172.31.0.0/16`) and `HG-VPC-Stack` (CIDR `10.10.0.0/16`).
* Create a Security Group for each VPC (`MY VPC SG`, `HG VPC SG`) opening SSH and ICMP to test connectivity.
* Launch one EC2 instance in each VPC (`EC2 - My VPC`, `EC2 - HG VPC`).
* Test connectivity between the two instances over the **public IP** before peering.
* Create a **VPC peering connection** `lab-vpc-peer` between the two VPCs and verify DNS resolution / private connectivity after peering.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Learn the fundamentals of VPC Peering <br> - Use CloudFormation to provision the `My-VPC-Stack` VPC (`172.31.0.0/16`) <br> - Use CloudFormation to provision the `HG-VPC-Stack` VPC (`10.10.0.0/16`) | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Create the Security Group `MY VPC SG` for the My VPC (open SSH 22, ICMP) <br> - Create the Security Group `HG VPC SG` for the HG VPC | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Launch the EC2 instance `EC2 - My VPC` (`t2.micro`) in My VPC <br> - Launch the EC2 instance `EC2 - HG VPC` (`t2.micro`) in HG VPC | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - SSH into `EC2 - My VPC` using EC2 Instance Connect <br> - Ping the public IP of `EC2 - HG VPC` to test connectivity over the Internet | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Create the VPC peering connection `lab-vpc-peer` (My VPC ↔ HG VPC) <br> - Accept the peering connection <br> - Test DNS resolution and private connectivity between the two VPCs | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Detailed work performed:

#### Monday — Provisioning two VPCs with CloudFormation

At the start of the week, I used **CloudFormation** to provision the network infrastructure for the VPC Peering lab. From the VPC template, I created the first stack `My-VPC-Stack` in the `ap-southeast-1` Region. The stack finished with the **CREATE_COMPLETE** status; the Events tab listed 75 resource-creation events (VPC, Subnet, Route Table, Internet Gateway, NAT Gateway…) — an entire VPC with the `172.31.0.0/16` CIDR provisioned from a single template.

> **Evidence:**
> ![The My-VPC-Stack stack created successfully with CREATE_COMPLETE status](/images/1.7-Week7/deploy-my-vpc-stack.png)

Next, I provisioned the second VPC with the `HG-VPC-Stack` stack (using the same template but changing the parameters to the `10.10.0.0/16` CIDR). Once completed, the Stacks list showed **both** `HG-VPC-Stack` and `My-VPC-Stack` in the **CREATE_COMPLETE** status — meaning I now had two independent VPCs with different address ranges, ready for peering.

> **Evidence:**
> ![Both the HG-VPC-Stack and My-VPC-Stack stacks in CREATE_COMPLETE](/images/1.7-Week7/deploy-hg-vpc-stack.png)

#### Tuesday — Creating a Security Group for each VPC

So that the two instances could test connectivity, I created a separate Security Group for each VPC. First was `MY VPC SG` (sg-07e7bc4685630cb07) in the My VPC (`vpc-0894fadf5d2a0559c`). A green banner confirmed "Security group … was created successfully", and the Inbound rules tab had **3 rules**: SSH (TCP 22) for remote access and two ICMP (All) rules to allow ping.

> **Evidence:**
> ![The MY VPC SG security group created with SSH and ICMP rules](/images/1.7-Week7/create-my-vpc-security-group.png)

Similarly, I created `HG VPC SG` (sg-08d46c738a1e50d94) in the HG VPC (`vpc-080a1fc0b167628ef`). The Security Groups screen listed all the groups: `MY VPC SG` belonging to the My VPC and `HG VPC SG` belonging to the HG VPC — each VPC now had a Security Group allowing SSH and ICMP.

> **Evidence:**
> ![The HG VPC SG security group created, the list showing the SGs of both VPCs](/images/1.7-Week7/create-hg-vpc-security-group.png)

#### Wednesday — Launching an EC2 instance in each VPC

I launched one EC2 instance (`t2.micro`) in each VPC to serve as the endpoints for connectivity testing. The Instances list showed two machines: `EC2 - My VPC` (i-0c92b0698afd409af) in the **Running** state (2/2 checks passed) and `EC2 - HG VPC` (i-0446a743af0985436) still **Pending** with private IP `10.10.2.55` and public IP `18.141.137.14` — exactly as designed, each instance sitting in a VPC with its own address range.

> **Evidence:**
> ![The two instances EC2 - My VPC (Running) and EC2 - HG VPC (Pending) in the two VPCs](/images/1.7-Week7/launch-ec2-instances.png)

#### Thursday — Testing connectivity over the public IP

Before peering, I tested connectivity between the two machines over the **Internet (public IP)**. Using **EC2 Instance Connect** to SSH into `EC2 - My VPC` (private IP `172.31.4.77`), I then ran `ping 18.141.137.14 -c5` — which is the public IP of `EC2 - HG VPC`. The result: **5 packets transmitted, 5 received, 0% packet loss** — the two machines could communicate over the Internet, confirming that the Security Group had opened ICMP correctly.

> **Evidence:**
> ![Pinging the public IP of EC2 - HG VPC from EC2 - My VPC successfully, 0% packet loss](/images/1.7-Week7/ping-public-ip-success.png)

#### Friday — Creating VPC Peering and testing private connectivity

On the last day of the week, I set up a **VPC peering connection** so the two VPCs could talk to each other over a private path instead of the Internet. The peering request `pcx-0404a6e4d3d4368d5` / `lab-vpc-peer` was created with the **Requester VPC** being My VPC (`172.31.0.0/16`) and the **Accepter VPC** being HG VPC; initially the status was **Pending Acceptance**.

> **Evidence:**
> ![The VPC peering connection lab-vpc-peer created, in the Pending Acceptance status](/images/1.7-Week7/request-vpc-peering.png)

After clicking accept on the request, the peering connection switched to the **Active** status. The console displayed the banner "Your VPC peering connection … has been established" along with an important note: for traffic to flow across the peering, you **must add a route** pointing to the peered VPC in each VPC's route table.

> **Evidence:**
> ![The lab-vpc-peer peering connection switching to the Active status](/images/1.7-Week7/vpc-peering-active.png)

Finally, I tested the **private** connectivity over the peering. From `EC2 - My VPC`, I pinged using the HG VPC instance's **public DNS**: `ping ec2-18-141-137-14.ap-southeast-1.compute.amazonaws.com -c5`. The interesting part: after peering, this hostname resolved to the **private IP `10.10.2.55`** (instead of the public IP) — proving that DNS resolution over the peering was working. However, the ping returned **100% packet loss** because at this point the route table and Security Group had not yet been configured for private cross-VPC traffic — exactly as warned by the "you must add a route" message in the previous step.

> **Evidence:**
> ![Ping via DNS resolving to the private IP 10.10.2.55 but 100% packet loss due to missing route configuration](/images/1.7-Week7/ping-private-ip-failed.png)

### Week 7 Achievements:

* Understood the concept of VPC Peering: private connectivity between two VPCs, different from going over the Internet via public IP.
* Provisioned two independent VPCs with CloudFormation: `My-VPC-Stack` (`172.31.0.0/16`) and `HG-VPC-Stack` (`10.10.0.0/16`).
* Created the Security Groups `MY VPC SG` and `HG VPC SG` for each VPC with SSH (22) and ICMP rules for testing.
* Launched an EC2 instance in each VPC (`EC2 - My VPC`, `EC2 - HG VPC`).
* Verified successful connectivity over the public IP between the two instances (ping with 0% packet loss).
* Created and accepted the VPC peering connection `lab-vpc-peer` (My VPC ↔ HG VPC), bringing it to the Active status.
* Observed the DNS resolution behavior over peering: the public DNS resolves to the target machine's private IP, and understood that an active peering still requires adding routes and opening the Security Group before private traffic will flow.

### Difficulties encountered:

* **Peering Active but private ping still 100% packet loss**: after the peering reached Active, pinging via DNS (resolving to the private IP `10.10.2.55`) still failed because no route pointing to the peered VPC had been added to the route table and the Security Group had not been opened for the other VPC's private range — the console does warn "must add a route to the peered VPC", but it is easy to overlook.
* **Distinguishing public IP from private IP when pinging**: for the same target machine, pinging the public IP (`18.141.137.14`) succeeded (going over the Internet), while pinging via DNS (resolving to the private IP after peering) failed — you must clearly understand the traffic path to avoid confusion when troubleshooting.
* **Managing two VPCs at once**: every action (Security Group, EC2, route) has to select the correct corresponding VPC/address range; it is easy to mix up `172.31.0.0/16` (My VPC) and `10.10.0.0/16` (HG VPC).

### Lessons learned:

Week 7 helped me clearly understand the difference between **public** connectivity (via the Internet Gateway, using the public IP) and **private** connectivity (via VPC Peering, using the private IP). The most impressive part was the chain: provisioning two VPCs' infrastructure with CloudFormation → creating Security Groups → launching EC2 → testing connectivity. The fact that the peering switched to Active but the private ping still failed was a valuable lesson: **peering only opens the "road"; for the "cars" to actually run, you need the "road signs" (route table) and the "gate to pass through" (Security Group)**. The detail that the public DNS automatically resolves to the private IP after peering also shows how intelligently AWS handles DNS resolution in a peered VPC environment. If I continue, I will add two-way routes and open the Security Group for the peer's range to complete the private connectivity.

### Conclusion:

Week 7 wrapped up with a two-VPC model connected via VPC Peering: infrastructure provisioned automatically with CloudFormation, each VPC with its own Security Group and EC2, public connectivity verified successfully, and the peering connection `lab-vpc-peer` in the Active status. The remaining step to enable private connectivity (adding routes + opening the Security Group) was clearly identified through the ping test. No sensitive information such as the AWS Account ID, full email address or account-identifying ARNs is included in this report.
