---
title: "Week 9 Worklog"
date: 2026-06-15
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

* Learn about **AWS Systems Manager (SSM)** — a suite of tools for centralized operational management of servers (managed nodes).
* Provision the lab infrastructure: the `windows-lab-ssm-vpc` VPC and launch 2 EC2 instances running Windows.
* Create an IAM Role granting SSM permissions to EC2 and attach it to both instances so they become managed nodes.
* Use **Fleet Manager** to check node status and **Session Manager** to connect to Windows without RDP/SSH.
* Use **Patch Manager** to scan and install patches on the instances.
* Use **Run Command** to run PowerShell commands remotely on multiple instances at once.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Learn the fundamentals of Systems Manager <br> - Create the `windows-lab-ssm-vpc` VPC (`10.0.0.0/16`) <br> - Launch 2 Windows EC2 instances (`Windows-Lab-SSM-1`, `Windows-Lab-SSM-2`) | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Create the IAM Role `Windows-Lab-SSM-Role` (trusted entity EC2) <br> - Attach the role to both instances to grant SSM permissions | 16/06/2026 | 16/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Check managed nodes in Fleet Manager (Ping status Online) <br> - Use Session Manager to connect PowerShell into both Windows instances | 17/06/2026 | 17/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Use Patch Manager to scan and install patches on the 2 instances (Install operation) | 18/06/2026 | 18/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Use Run Command to run a PowerShell script on both instances <br> - View the returned command output | 19/06/2026 | 19/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Detailed work performed:

#### Monday — Provisioning the VPC and launching two Windows EC2 instances

At the start of the week, I prepared the infrastructure for the Systems Manager lab. First was the `windows-lab-ssm-vpc` VPC (`vpc-0d3e2442f0ee81061`) with the `10.0.0.0/16` CIDR, in the **Available** state, consisting of 4 subnets and 4 route tables — the network foundation for the Windows machines.

> **Evidence:**
> ![The windows-lab-ssm-vpc VPC with the 10.0.0.0/16 CIDR in the Available state with 4 subnets](/images/1.9-Week9/create-windows-lab-vpc.png)

Next, I launched the first Windows instance `Windows-Lab-SSM-1` (`i-0d5de246eb19c0e70`, `t3.micro`) — the instance switched to **Running**.

> **Evidence:**
> ![Launching the EC2 Windows-Lab-SSM-1 (t3.micro) in the Running state](/images/1.9-Week9/launch-first-windows-instance.png)

I then launched a second instance `Windows-Lab-SSM-2` (`i-038c7ec288a048e41`) in a different AZ (`ap-southeast-1b`). Both instances were **Running**; `Windows-Lab-SSM-1` had private IP `10.0.0.52` and public IP `13.250.16.49` — these two Windows machines would be the targets managed by SSM.

> **Evidence:**
> ![Both instances Windows-Lab-SSM-1 and Windows-Lab-SSM-2 in the Running state](/images/1.9-Week9/launch-second-windows-instance.png)

#### Tuesday — Creating an IAM Role for SSM and attaching it to the instances

For EC2 to be managed by Systems Manager, the instance needs an **IAM Role** with SSM permissions. I created the role `Windows-Lab-SSM-Role` with the trusted entity **AWS Service: EC2** — the role appeared in the Roles list with the banner "Role Windows-Lab-SSM-Role created".

> **Evidence:**
> ![The IAM Role Windows-Lab-SSM-Role created with the EC2 trusted entity](/images/1.9-Week9/create-ssm-iam-role.png)

I then attached this role to each instance. For `Windows-Lab-SSM-1` (`i-0d5de246eb19c0e70`), the console reported **"Successfully attached Windows-Lab-SSM-Role to instance"**; the details showed the IAM role attached and the instance sitting in the subnet `windows-lab-ssm-subnet-public1-ap-southeast-1a`.

> **Evidence:**
> ![Windows-Lab-SSM-Role successfully attached to the Windows-Lab-SSM-1 instance](/images/1.9-Week9/attach-role-to-instance-1.png)

Similarly, I attached the role to `Windows-Lab-SSM-2` (`i-038c7ec288a048e41`, subnet `windows-lab-ssm-subnet-public2-ap-southeast-1b`) — also receiving a successful attach notification. At this point, both instances had the permissions needed to connect to Systems Manager.

> **Evidence:**
> ![Windows-Lab-SSM-Role successfully attached to the Windows-Lab-SSM-2 instance](/images/1.9-Week9/attach-role-to-instance-2.png)

#### Wednesday — Fleet Manager and Session Manager

After attaching the role, I opened **Fleet Manager** in Systems Manager. The Managed Nodes section showed **2 nodes** — both Windows instances in the **Running** state, Ping status **Online**, SSM Agent version `3.3.4515.0`. This confirmed that the SSM Agent on the machines had successfully connected back to the service.

> **Evidence:**
> ![Fleet Manager showing 2 Windows managed nodes both Online](/images/1.9-Week9/fleet-manager-managed-nodes.png)

I used **Session Manager** to connect into `Windows-Lab-SSM-1` — opening PowerShell right in the browser without RDP or opening any port. Running `ipconfig` as a test, the result correctly returned IPv4 `10.0.0.52` and default gateway `10.0.0.1`.

> **Evidence:**
> ![Session Manager connecting into Windows-Lab-SSM-1, running ipconfig returning IP 10.0.0.52](/images/1.9-Week9/session-manager-connect-instance-1.png)

Similarly, I opened a Session Manager session to `Windows-Lab-SSM-2` (`i-038c7ec288a048e41`) and got the machine's PowerShell window — proving that both machines were accessible via Session Manager.

> **Evidence:**
> ![Session Manager connecting PowerShell into Windows-Lab-SSM-2](/images/1.9-Week9/session-manager-connect-instance-2.png)

#### Thursday — Patch Manager

I moved on to **Patch Manager** to manage patches. I performed the **Patch now** operation with the **Install** operation, reboot option **NoReboot**, targeting both instances (`i-0d5de246eb19c0e70`, `i-038c7ec288a048e41`). The Association execution summary page returned **Status: Success**, the Summary section showed **Success=2**, and the Scan/Install operation summary chart was entirely **Succeeded**.

> **Evidence:**
> ![Patch Manager installing patches on 2 instances with the result Success=2](/images/1.9-Week9/patch-manager-install-summary.png)

#### Friday — Run Command running PowerShell remotely

At the end of the week, I used **Run Command** to run a command on multiple instances at once. Command ID `3db39f20-dfd3-45de-9bef-58fd98ed25bd` (the `aws:runPowerShellScript` document) was sent successfully — Overall status **Success**, **2 targets / 2 completed / 0 error / 0 delivery timed out**, both instances reporting Success.

> **Evidence:**
> ![Run Command running on 2 instances with overall status Success, 2/2 completed](/images/1.9-Week9/run-command-success.png)

Opening the detailed output on instance `i-038c7ec288a048e41`, the `aws:runPowerShellScript` step returned **Response code 0**, and the Output section listed the Windows user accounts (`Administrator`, `DefaultAccount`, `Guest`, `ssm-user`, `WDAGUtilityAccount`) — with a note at the end reading "The command completed with one or more errors". So the command still executed and returned data, despite a minor warning in the output.

> **Evidence:**
> ![Run Command output on the instance listing the Windows accounts, response code 0](/images/1.9-Week9/run-command-output.png)

### Difficulties encountered:

* **The instances need an IAM Role before they appear in Fleet Manager**: when first launched, the two EC2 instances did not have permissions yet, so they were not managed nodes; the SSM Agent could not connect back to the service.
  * **Resolution:** create the IAM Role `Windows-Lab-SSM-Role` (EC2 trusted entity, SSM permissions) and attach it to both instances; then wait for the nodes to switch to Ping status **Online** in Fleet Manager.
* **Run Command reporting "completed with one or more errors" despite a Success status**: the PowerShell command's output had an error note at the end even though the response code was 0 and the overall status was Success.
  * **Resolution:** read the Output/Error section carefully to distinguish real errors from minor warnings; here the command still returned the correct account list, so the main result was still usable.
* **Connecting to Windows without wanting to open RDP/a port**: exposing RDP to the Internet carries security risks.
  * **Resolution:** use **Session Manager** to access PowerShell via the browser, with no need to open port 3389 or use a key — more convenient and more secure.

### Week 9 Achievements:

* Understood the role of AWS Systems Manager as a suite of tools for **centralized operational management** of many servers.
* Provisioned the `windows-lab-ssm-vpc` VPC and launched 2 Windows EC2 instances (`Windows-Lab-SSM-1`, `Windows-Lab-SSM-2`).
* Created the IAM Role `Windows-Lab-SSM-Role` and attached it to both instances to turn them into managed nodes.
* Confirmed 2 nodes **Online** in Fleet Manager and connected into Windows using Session Manager (without RDP/SSH).
* Installed patches on both instances with Patch Manager, resulting in **Success=2**.
* Ran a PowerShell script remotely on both instances with Run Command (2/2 Success) and read the returned output.

### Knowledge gained:

Week 9 gave me an overview of how to **operate a fleet of servers at scale** without logging directly into each machine. The key point is the chain: **IAM Role (SSM permissions) → SSM Agent connects → managed node → use the SSM tools**. Once an instance is a managed node, I can: view the fleet's health with Fleet Manager, connect securely with Session Manager (instead of RDP/SSH, without opening ports), automatically patch with Patch Manager, and run bulk commands with Run Command. The most impressive were Session Manager and Run Command: one lets you access a Windows machine securely through the browser, the other lets you execute the same command across many machines in just seconds — an approach that is far cleaner and more secure than manually RDP-ing into each machine.

### Weekly summary:

Week 9 wrapped up with a complete Windows server management workflow using AWS Systems Manager: infrastructure consisting of a VPC and 2 Windows EC2 instances, an IAM Role granting permissions to turn both machines into managed nodes, Fleet Manager monitoring status, Session Manager for secure connectivity, Patch Manager for patching, and Run Command for bulk command execution — all successful (Success=2 for both patching and the command). Compared with administering each machine manually, Systems Manager demonstrates a far more centralized, secure, and scalable way to operate. No sensitive information such as the AWS Account ID, full email address or account-identifying ARNs is included in this report.
