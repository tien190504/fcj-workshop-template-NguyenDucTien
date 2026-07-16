---
title: "Week 3 - Amazon EC2 lifecycle (Windows/Linux) and web application deployment"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Practice the full Amazon EC2 lifecycle following the [Introduction to Amazon EC2](https://000004.awsstudygroup.com/vi/) lab: build the network, launch, connect, deploy an application and clean up resources.
* Build two separate network environments (`Windows-vpc` and `Linux-vpc`), each with its own subnets and a purpose-built Security Group.
* Launch and connect to a Windows instance via RDP and a Linux instance via SSH (MobaXterm).
* Install XAMPP on the Windows instance, initialize the `awsuser` database with phpMyAdmin and deploy a Node.js application locally.
* Install and configure an Apache web server (plus MariaDB) on Amazon Linux 2023, verifying it through both the public IPv4 address and the public DNS name.
* Deploy the **AWS FCJ Management** web application on the Linux instance and test the full user CRUD flow: List, Add, Details, Edit.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Review EC2, AMI and instance type theory <br> - Create VPC `Windows-vpc` (`10.0.0.0/16`) with 4 subnets across 2 AZs <br> - Enable auto-assign public IPv4 for the public subnets <br> - Create Security Group `Windows-SG` (RDP 3389, HTTP 80, Custom TCP 5000, ICMP) | 04/05/2026 | 04/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 3   | - Launch `Windows-instance` (`t3.micro`) in the public subnet <br> - Decrypt the Administrator password with the key pair and connect via RDP <br> - Install XAMPP, create database `awsuser` and table `user` in phpMyAdmin <br> - Install Node.js and deploy the app at `localhost:5000` <br> - Stop the instance after finishing to save credits | 05/05/2026 | 05/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 4   | - Create VPC `Linux-vpc` (`10.0.0.0/16`) with 4 subnets across 2 AZs <br> - Create Security Group `Linux-SG` (SSH 22, HTTP 80, HTTPS 443, MySQL 3306, Custom TCP 5000, ICMP) <br> - Launch `Linux-instance` (`t3.nano`, Amazon Linux 2023) | 06/05/2026 | 06/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 5   | - SSH into `Linux-instance` with MobaXterm using the key pair <br> - Install Apache (`httpd`) and MariaDB with `dnf`, enable the service with `systemctl` <br> - Verify the web server via public IPv4 and public DNS | 07/05/2026 | 07/05/2026 | <https://000004.awsstudygroup.com/vi/> |
| 6   | - Deploy the **AWS FCJ Management** application on port 5000 <br> - Test the full user CRUD flow: List, Add, View Details, Edit <br> - Review the week and clean up unused resources | 08/05/2026 | 08/05/2026 | <https://000004.awsstudygroup.com/vi/> |

### Work Details:

#### Monday — Building the network environment for the Windows instance

I started the week by preparing a dedicated network environment for the Windows instance instead of using the default VPC. I created a VPC named `Windows-vpc` with CIDR block `10.0.0.0/16`, enabling **DNS hostnames** and **DNS resolution** so the instance would receive a friendly public DNS name later. The Resource map view confirmed the VPC came with 4 subnets and 4 route tables laid out exactly as designed.

> **Evidence:**
> ![Details of the Windows-vpc after creation](/images/1.3-Week3/create-window-vpc.png)

The VPC was divided into 4 subnets across 2 Availability Zones: `Windows-subnet-public1` (`10.0.0.0/20`, ap-southeast-1a), `Windows-subnet-public2` (`10.0.16.0/20`, ap-southeast-1b) and two private subnets `10.0.128.0/20`, `10.0.144.0/20`. For the public subnets I enabled **auto-assign public IPv4 address** — the Console showed the "You have successfully changed subnet settings" banner, and the subnet details confirmed the public subnet was associated with the `Windows-rtb-public` route table.

> **Evidence:**
> ![Subnet list of Windows-vpc with auto-assign public IPv4 enabled](/images/1.3-Week3/list-subnet-window.png)

To finish the day, I created the Security Group `Windows-SG` attached to `Windows-vpc` with 8 inbound rules serving every scenario planned for the week: **RDP (TCP 3389)** for Remote Desktop, **HTTP (TCP 80)**, **Custom TCP 5000** for the Node.js application, and ICMP for connectivity testing. Seeing the rules through the lens of "only open what you actually use" made me much more deliberate than when I used to pick the default group.

> **Evidence:**
> ![Windows-SG created successfully with 8 inbound rules](/images/1.3-Week3/create-window-sg.png)

#### Tuesday — Windows instance: RDP, XAMPP and deploying the app locally

I launched `Windows-instance` (Microsoft Windows Server, `t3.micro`) into the public subnet of `Windows-vpc` with `Windows-SG` attached. The instance entered the **Running** state and received a public IPv4 DNS after about a minute — much faster than I expected for a Windows machine.

> **Evidence:**
> ![Windows-instance running in the EC2 Console](/images/1.3-Week3/create-window-instances.png)

To connect, I chose **Connect using RDP client**, downloaded the remote desktop file and used the key pair to decrypt the Administrator password. Pasting the password into Remote Desktop Connection brought me straight to the Windows Server desktop running on EC2. This was my first time touching a cloud-hosted Windows machine, and honestly it felt no different from a local one.

> **Evidence:**
> ![Connecting to the Windows instance desktop via RDP](/images/1.3-Week3/connect-window-instances.png)

Inside the RDP session, I installed **XAMPP** to get Apache + MariaDB + PHP in one package. In phpMyAdmin (`localhost/phpmyadmin`), I created the database `awsuser` and ran the SQL statement creating the `user` table with columns `id`, `first_name`, `last_name`, `comments`, `status` — MySQL returned "empty result set", confirming the table was created successfully.

> **Evidence:**
> ![Creating the awsuser database and user table in phpMyAdmin on the Windows instance](/images/1.3-Week3/intall-xampp-on-windows-instance.png)

Next, I installed Node.js, ran `npm init` and started the **AWS FCJ Management** application on the instance. Opening `localhost:5000` in the browser inside the RDP session displayed the Records page reading data straight from the `awsuser` database — the application worked end-to-end on the Windows instance.

> **Evidence:**
> ![AWS FCJ Management application running at localhost:5000 on the Windows instance](/images/1.3-Week3/deploy-app-on-windows-instance.png)

At the end of the day, I stopped the instance to save Free Tier credits. The RDP session immediately showed "Your Remote Desktop Services session has ended" — a small but memorable illustration that the RDP session lives and dies with the instance state.

> **Evidence:**
> ![RDP session ended after the Windows instance was stopped](/images/1.3-Week3/windowinstances_stop.png)

#### Wednesday — Building the network and launching the Linux instance

Repeating Monday's workflow, I built a second environment for Linux: VPC `Linux-vpc` (`10.0.0.0/16`) with DNS hostnames/resolution enabled. Doing it a second time took less than half the time of the first — the VPC → Subnet → SG sequence had started to feel natural.

> **Evidence:**
> ![Details of the Linux-vpc after creation](/images/1.3-Week3/create-vpc-linux.png)

The VPC also had 4 subnets across 2 AZs (`Linux-subnet-public1/2`, `Linux-subnet-private1/2`) with the same CIDR layout as the Windows VPC, and I enabled auto-assign public IPv4 for `Linux-subnet-public1`.

> **Evidence:**
> ![Subnet list of Linux-vpc with auto-assign public IPv4 enabled](/images/1.3-Week3/list-subnet-linux.png)

The Security Group `Linux-SG` differs from `Windows-SG` in intent: instead of RDP it opens **SSH (TCP 22)**, plus **HTTP 80 / HTTPS 443** for the web server, **MySQL/Aurora (TCP 3306)** for the database and **Custom TCP 5000** for the application. Comparing the two groups side by side made the "one Security Group per server role" idea click.

> **Evidence:**
> ![Linux-SG created successfully with 7 inbound rules](/images/1.3-Week3/create-linux-sg.png)

I then launched `Linux-instance` (Amazon Linux 2023, `t3.nano`) into the public subnet. The instance list told this week's story at a glance: `Windows-instance` in **Stopped** state from yesterday, `Linux-instance` **Running** and initializing.

> **Evidence:**
> ![Linux-instance running while Windows-instance remains stopped](/images/1.3-Week3/create-linux-instances.png)

#### Thursday — SSH, installing Apache and MariaDB on Amazon Linux 2023

I connected to the instance with **MobaXterm** using SSH: `ec2-user@54.169.253.206` authenticated by the key pair. The Amazon Linux 2023 banner appeared along with the prompt `ip-10-0-8-114` — the instance's private IP inside `Linux-vpc`, a nice reminder that public and private addressing coexist on the same machine.

> **Evidence:**
> ![SSH into the Linux instance using MobaXterm](/images/1.3-Week3/connect-linux-instance.png)

I installed the web and database stack with `dnf`: the `mariadb105` package (version 10.5.29) completed installation, then `sudo systemctl start httpd` and `sudo systemctl enable httpd` started Apache and registered it to start on boot — `systemctl is-enabled httpd` returned `enabled`.

> **Evidence:**
> ![Installing MariaDB and enabling the Apache service](/images/1.3-Week3/setup-apache-server.png)

To verify, I opened the browser from my local machine to the public IPv4 address `54.169.253.206` — Apache's classic **"It works!"** page appeared, proving both the web server and the Security Group's port 80 rule were correct.

> **Evidence:**
> ![Testing Apache via the public IPv4 address](/images/1.3-Week3/use-ipv4-test-apache.png)

I tested once more with the public DNS name `ec2-54-169-253-206.ap-southeast-1.compute.amazonaws.com` and got the same result — confirming the **DNS hostnames** option enabled on VPC creation was doing its job: one address for humans, one for machines, both pointing at the same instance.

> **Evidence:**
> ![Testing Apache via the public DNS name](/images/1.3-Week3/use-dns-test-apache.png)

#### Friday — Deploying AWS FCJ Management and testing the user CRUD flow

On the last day, I deployed the **AWS FCJ Management** application onto the Linux instance and exposed it on port 5000 — the port opened in `Linux-SG` since Wednesday. Visiting `54.169.253.206:5000` from my local machine rendered the Records page with an empty table and the **Add New User** button: the app was live on the internet, no longer just `localhost` like on the Windows instance.

> **Evidence:**
> ![AWS FCJ Management deployed on the Linux instance at port 5000](/images/1.3-Week3/deploy-aws-web.png)

I used **Add New User** to insert a series of user records. The list refreshed with every addition and displayed all 18 records with **View / Edit / Delete** actions on each row — the Create and List parts of CRUD worked flawlessly.

> **Evidence:**
> ![Records list after adding users](/images/1.3-Week3/add-user.png)

Clicking **View** on a record navigated to `/viewuser/18` showing the details of user *Joanna Jedrzejczyk* — full name, email, phone and comments rendered from the database.

> **Evidence:**
> ![User details page](/images/1.3-Week3/details-user.png)

Finally, I tested **Edit** at `/edituser/18`: the form pre-filled every existing field, allowed changes and submitted the update back to MariaDB. With that, the complete flow — network, instance, web server, database and CRUD application — had been verified end to end on AWS.

> **Evidence:**
> ![Edit User form with pre-filled data](/images/1.3-Week3/edit-user.png)

### Learning Outcomes — Week 3 Achievements:

* Built two complete, purpose-separated network environments (`Windows-vpc` and `Linux-vpc`), each with 4 subnets across 2 AZs and auto-assign public IPv4 for the public subnets.
* Designed Security Groups per server role: `Windows-SG` (RDP 3389, HTTP 80, TCP 5000, ICMP) and `Linux-SG` (SSH 22, HTTP 80, HTTPS 443, MySQL 3306, TCP 5000, ICMP).
* Launched and administered both Windows (`t3.micro`) and Linux (`t3.nano`) instances; mastered both connection paths — RDP with key-pair password decryption and SSH via MobaXterm.
* Installed XAMPP on the Windows instance, initialized the `awsuser` database and `user` table with phpMyAdmin, and ran a Node.js application at `localhost:5000`.
* Installed and enabled Apache (`httpd`) and MariaDB 10.5 on Amazon Linux 2023 with `dnf` and `systemctl`, verifying through both public IPv4 and public DNS.
* Deployed the AWS FCJ Management web application publicly on port 5000 and validated the full user CRUD flow: List, Add, Details, Edit.
* Formed the habit of stopping instances when idle to preserve Free Tier credits.

### Challenges:

* **The RDP password cannot be retrieved immediately**: right after launching the Windows instance, "Get password" stayed unavailable for a few minutes until the instance finished initializing — I learned to wait for the status checks instead of assuming something was broken.
* **The RDP session ended abruptly** when the instance was stopped; at first I thought it was a network error, but it turned out to be the expected behavior of the instance lifecycle.
* **Opening the app port**: the application on port 5000 was unreachable from outside until I double-checked that `Linux-SG` had the Custom TCP 5000 inbound rule and that the app was listening on all interfaces, not just `localhost`.
* **Distinguishing IPv4 vs DNS testing**: understanding why both addresses resolve to the same instance required revisiting the VPC's DNS hostnames setting from day 1 — an early configuration whose effect only shows up days later.

### Reflection:

The biggest shift this week was moving from *building infrastructure* (Weeks 1–2) to *running real workloads on it*. Repeating the same VPC → Subnet → Security Group → Instance sequence twice — once for Windows, once for Linux — turned what used to be a checklist into muscle memory, and comparing the two Security Groups side by side taught me more about the principle of least privilege than any theory page. I also felt the practical difference between the two operating models: Windows administration is visual and RDP-driven, while Linux is terminal-driven and much lighter (`t3.nano` was enough). If I did it again, I would automate the repeated network setup rather than clicking through the Console twice.

### Conclusion:

Week 3 closed the loop from empty AWS account to a working web application serving CRUD operations over the internet: two custom VPCs, role-specific Security Groups, a Windows instance running XAMPP + Node.js, and a Linux instance running Apache + MariaDB hosting the AWS FCJ Management app on port 5000. Every step was verified with evidence — from the "It works!" page over both IPv4 and DNS to the Add/Details/Edit user flows. No sensitive information such as AWS Account IDs or key pairs is included in this report.
