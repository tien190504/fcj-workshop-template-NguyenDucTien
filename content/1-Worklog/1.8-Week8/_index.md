---
title: "Week 8 Worklog"
date: 2026-06-08
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

* Learn about **AWS Transit Gateway (TGW)** — a network transit hub that connects many VPCs together, replacing the pairwise peering model from the previous week.
* Use **CloudFormation** to provision the `Lab20-Stack` lab environment consisting of multiple VPCs and EC2 hosts for testing.
* Prepare connectivity to the hosts using the `tgw-key.pem` key pair.
* Create the Transit Gateway `lab20-tgw` and attach multiple VPCs to it.
* Create the Transit Gateway route table `lab20-TGW-RT` and associate the attachments for centralized routing.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Learn the fundamentals of Transit Gateway <br> - Use CloudFormation to provision the `Lab20-Stack` lab environment (multiple VPCs + EC2 hosts) | 08/06/2026 | 08/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Prepare the `tgw-key.pem` key pair <br> - Use `scp` to copy the key to the EC2 hosts to prepare for transit connectivity | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Create the Transit Gateway `lab20-tgw` <br> - Check the TGW's initialization status | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Create the first Transit Gateway VPC attachment <br> - Attach the remaining VPCs to the TGW (4 attachments in total) | 11/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Create the Transit Gateway route table `lab20-TGW-RT` <br> - Associate the 4 attachments with the route table for centralized routing | 12/06/2026 | 12/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Detailed work performed:

#### Monday — Provisioning the lab environment with CloudFormation

At the start of the week, I used **CloudFormation** to provision the practice environment for the Transit Gateway lab. The `Lab20-Stack` stack in the `ap-southeast-1` Region finished with the **CREATE_COMPLETE** status; the Events tab had over 100 resource-creation events (including the `FirstHost` host). At the same time, the `My-VPC-Stack` stack from the previous week's lab was being cleaned up (**DELETE_IN_PROGRESS**) to free resources before starting the new lab.

> **Evidence:**
> ![The Lab20-Stack stack created successfully with CREATE_COMPLETE, My-VPC-Stack being deleted](/images/1.8-Week8/deploy-lab-stack.png)

#### Tuesday — Preparing connectivity to the hosts with a key pair

To work with the EC2 hosts across multiple VPCs, I needed to place the `tgw-key.pem` key pair onto an intermediate host. From cmd.exe, I ran `scp -i tgw-key.pem tgw-key.pem ec2-user@54.179.195.54:` and similarly to the host `13.212.12.165`. However, both attempts reported **Host key verification failed / Connection closed** — because the new hosts were not yet in `known_hosts` and the fingerprint verification prompt had not been answered correctly. The EC2 list in the background showed the instances still **Initializing** (private IP e.g. `172.16.3.12`).

> **Evidence:**
> ![Using scp to copy tgw-key.pem to the hosts but hitting a Host key verification failed error](/images/1.8-Week8/scp-key-host-verification-failed.png)

#### Wednesday — Creating the Transit Gateway

Once the infrastructure was ready, I created the **Transit Gateway** to serve as the routing hub. The TGW `lab20-tgw` (`tgw-0053b8d202c91b6ba`) was created successfully — a green banner confirmed "You successfully created … lab20-tgw", with the initial status **Pending** while AWS initialized it. This would be the "hub" that every VPC connects into, instead of peering pair by pair.

> **Evidence:**
> ![The Transit Gateway lab20-tgw created, in the Pending status](/images/1.8-Week8/create-transit-gateway.png)

#### Thursday — Attaching the VPCs to the Transit Gateway

Next, I created **Transit Gateway attachments** to bring the VPCs into the TGW. The first attachment `tgw-attach-09375dbe5a4d001ca` (Resource type **VPC**, `vpc-00b2bcf8f8fa68b5a`) was created successfully, attached to `lab20-tgw`, in the **Pending** status.

> **Evidence:**
> ![Creating the first Transit Gateway VPC attachment, in the Pending status](/images/1.8-Week8/create-tgw-vpc-attachment.png)

I repeated the operation for the remaining VPCs. The Transit gateway attachments screen showed **4 attachments** (Attachment 1 → 4), all pointing to the same `tgw-0053b8d202c91b6ba` with Resource type VPC — one attachment already **Available**, the rest still **Pending**. Thus multiple VPCs were now connected into a single Transit Gateway.

> **Evidence:**
> ![The list of 4 Transit Gateway VPC attachments all attached to lab20-tgw](/images/1.8-Week8/tgw-vpc-attachments-list.png)

#### Friday — Creating the Transit Gateway route table and associating attachments

On the last day of the week, I created a **Transit Gateway route table** to control routing between the VPCs. The route table `lab20-TGW-RT` (`tgw-rtb-0960e827579c78459`) attached to `lab20-tgw` was created successfully, with the initial status **Pending**.

> **Evidence:**
> ![Creating the Transit Gateway route table lab20-TGW-RT, in the Pending status](/images/1.8-Week8/create-tgw-route-table.png)

Once the route table switched to **Available**, I associated the attachments with it. The Associations tab of `lab20-TGW-RT` showed **4 associations** corresponding to the 4 VPC attachments — some in the **Associated** state, some still **Associating**. At this point, a Transit Gateway with a centralized route table had connected and coordinated routing for multiple VPCs at once.

> **Evidence:**
> ![The lab20-TGW-RT route table Available with 4 associations from the VPC attachments](/images/1.8-Week8/tgw-route-table-associations.png)

### Difficulties encountered:

* **`scp` reporting Host key verification failed**: when copying `tgw-key.pem` to new hosts (`54.179.195.54`, `13.212.12.165`), SSH did not yet have the hosts' fingerprints in `known_hosts` and asked for confirmation; because the prompt was not answered correctly, the connection was closed (Connection closed).
  * **Resolution:** answer `yes` when asked to verify the fingerprint (or add the host to `known_hosts` beforehand), and **wait for the instances to fully reach Running** (they were still Initializing at the time) before performing the `scp`.
* **Many resources in the Pending/Associating state**: the TGW, attachments, and associations all need time to initialize and are not ready immediately after creation.
  * **Resolution:** patiently refresh the list and wait for the status to switch to Available/Associated before moving to the next step (for example, associations can only be made once the route table is Available).
* **Easy to confuse the many VPCs/attachments**: there were up to 4 VPC attachments on a single TGW with near-identical IDs.
  * **Resolution:** name the attachments clearly (Attachment 1 → 4) and cross-check by Transit gateway attachment ID / Resource ID when operating.

### Week 8 Achievements:

* Understood the role of the Transit Gateway as a **centralized routing hub**, addressing the limitations of the pairwise VPC peering model.
* Provisioned the `Lab20-Stack` lab environment with CloudFormation (multiple VPCs + EC2 hosts) and cleaned up the old stack before starting.
* Practiced copying the `tgw-key.pem` key pair to a host with `scp` and identified the Host key verification error.
* Created the Transit Gateway `lab20-tgw` and attached **4 VPC attachments** to a single TGW.
* Created the Transit Gateway route table `lab20-TGW-RT` and associated the 4 attachments for centralized routing.
* Grasped the status lifecycle of TGW resources: Pending → Available, Associating → Associated.

### Knowledge gained:

Week 8 expanded my networking mindset from "pairwise connectivity" (VPC Peering in Week 7) to "centralized connectivity" via the Transit Gateway. The key point is the **hub-and-spoke** model: instead of creating N×(N-1)/2 peering connections between VPCs, you only attach each VPC to the TGW once and let the TGW's route table coordinate traffic — much simpler to manage and easier to scale. I also clearly understood the chain of TGW components: **Transit Gateway → VPC Attachment → TGW Route Table → Association**, each step with its own status that must be ready before proceeding to the next. In addition, the Host key verification error when using `scp` reminded me that working with new hosts always requires handling the fingerprint and ensuring the instance is truly Running.

### Weekly summary:

Week 8 wrapped up with a multi-VPC network centrally connected through AWS Transit Gateway: infrastructure provisioned automatically with the CloudFormation `Lab20-Stack`, the Transit Gateway `lab20-tgw` as the hub, 4 VPCs attached to the TGW, and the route table `lab20-TGW-RT` fully associated with the attachments to coordinate routing. Compared with VPC Peering from the previous week, the Transit Gateway shows a far cleaner and more scalable way to connect many VPCs. No sensitive information such as the AWS Account ID, `.pem` key contents, full email address or account-identifying ARNs is included in this report.
