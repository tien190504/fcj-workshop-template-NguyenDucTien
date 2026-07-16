---
title: "Week 4 - Amazon RDS database and the two-tier application model"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Get familiar with Amazon RDS — AWS's managed database service.
* Build the `first-cloud-vpc` network infrastructure with separate Security Groups for the application tier (`first-cloud-app-sg`) and the database tier (`first-cloud-db-sg`).
* Create a DB subnet group, launch an EC2 app server and the RDS MySQL instance `first-cloud-db-instance`.
* Connect to RDS from EC2 using the mysql client: create the `first_cloud_users` database, the `user` table and load data.
* Connect the **AWS FCJ Management** web application to RDS and verify the data rendered in the browser.
* Monitor RDS through CloudWatch metrics, explore automated backups and practice restoring a snapshot.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Learn about Amazon RDS and the 2-tier app/database model <br> - Create VPC `first-cloud-vpc` (`10.0.0.0/16`) with 4 subnets <br> - Create Security Group `first-cloud-app-sg` (SSH 22, HTTP 80, HTTPS 443, Custom TCP 5000) <br> - Create Security Group `first-cloud-db-sg` (MySQL/Aurora 3306 only) | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Create the DB subnet group `first-cloud-db-subnet-group` for RDS <br> - Launch EC2 `first-cloud-app-server` (`t2.micro`) in the public subnet <br> - Connect to the app server via SSH using MobaXterm | 12/05/2026 | 12/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Create the RDS MySQL instance `first-cloud-db-instance` (`db.t4g.micro`) <br> - Connect to RDS from EC2 using the mysql client <br> - Create the `first_cloud_users` database, the `user` table and INSERT 18 records <br> - Verify the data with `SHOW TABLES`, `SELECT` | 13/05/2026 | 13/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Connect the AWS FCJ Management application to RDS, running on port 5000 <br> - Verify the data from RDS rendered on the web <br> - Review RDS monitoring metrics on the Monitoring tab (CloudWatch) | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Review the RDS automated backup configuration and automatic snapshots <br> - Practice restoring a snapshot into the new instance `first-cloud-db-restore` <br> - Wrap up the week and clean up unused resources | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Work Details:

#### Monday — Building the network foundation and tiered app/db Security Groups

On the first day of the week, I built the network infrastructure for the RDS lab. I created the VPC `first-cloud-vpc` with CIDR block `10.0.0.0/16`, enabling DNS hostnames/DNS resolution; the Resource map confirmed the VPC was in the **Available** state with 4 subnets and 4 route tables.

> **Evidence:**
> ![Details of the first-cloud-vpc after creation](/images/1.4-Week4/create-first-cloud-vpc.png)

Unlike previous weeks where each machine used a single Security Group, this week I followed a **2-tier** design: the application tier and the database tier each get their own SG. `first-cloud-app-sg` serves the app server with 4 inbound rules: **SSH (22)** for administration, **HTTP (80)** / **HTTPS (443)** for the web and **Custom TCP 5000** for the Node.js application.

> **Evidence:**
> ![Creating Security Group first-cloud-app-sg with 4 inbound rules](/images/1.4-Week4/create-first-cloud-app-sg.png)

`first-cloud-db-sg` for RDS is far more minimal: exactly **one** inbound rule, **MySQL/Aurora (TCP 3306)** — the database has no reason to expose any other port. Putting the two SGs side by side made the least-privilege principle crystal clear: each tier only opens the ports it actually serves.

> **Evidence:**
> ![Creating Security Group first-cloud-db-sg with only port 3306 open](/images/1.4-Week4/create-first-cloud-db-sg.png)

#### Tuesday — DB subnet group and the EC2 app server

RDS is not placed directly into a single subnet like EC2; it requires a **DB subnet group** — a set of subnets spread across multiple Availability Zones from which AWS picks where to place the database. I created `first-cloud-db-subnet-group` from the subnets of `first-cloud-vpc`; the **Complete** status confirmed the subnet group was valid.

> **Evidence:**
> ![DB subnet group first-cloud-db-subnet-group created successfully](/images/1.4-Week4/create-first-cloud-db-subnet-group.png)

Next, I launched the EC2 instance `first-cloud-app-server` (`t2.micro`, ap-southeast-1a) in the public subnet with `first-cloud-app-sg` attached — this machine would act as the app server connecting to RDS in the following days.

> **Evidence:**
> ![Launching EC2 first-cloud-app-server](/images/1.4-Week4/create-first-cloud-app-server.png)

Once the instance was running steadily, I SSH'd in with **MobaXterm** using the key pair: `ec2-user@54.169.53.137`, greeted by the Amazon Linux 2023 banner and the prompt `ip-10-0-7-96` — the app server's private IP inside the VPC.

> **Evidence:**
> ![SSH into the app server using MobaXterm](/images/1.4-Week4/connect-ec2-instance.png)

#### Wednesday — Creating the RDS MySQL instance and seeding data from EC2

The most important day of the week: I created the RDS instance `first-cloud-db-instance` with the **MySQL Community** engine, class `db.t4g.micro`, placed in the subnet group and attached to the prepared `first-cloud-db-sg`. The Console showed the **Creating** status along with a note that the database would take a few minutes to launch; the Connectivity & security tab listed the connection options (code snippets, CloudShell, Endpoints).

> **Evidence:**
> ![RDS instance first-cloud-db-instance being created](/images/1.4-Week4/create-rds.png)

Once RDS was ready, from the SSH session on the app server I used the mysql client to connect to the database endpoint (the server reported version 8.4.9). I created a new database and switched into it:

```sql
CREATE DATABASE IF NOT EXISTS first_cloud_users;
USE first_cloud_users;
```

> **Evidence:**
> ![Connecting to RDS from EC2 and creating the first_cloud_users database](/images/1.4-Week4/connect-db.png)

I then created the `user` table (columns `id`, `first_name`, `last_name`, `email`, `phone`, `comments`, `status`) and ran the `INSERT` statement to load sample data — the result was `Query OK, 18 rows affected`: 18 records written to RDS with no errors and no duplicates.

> **Evidence:**
> ![Creating the user table and INSERTing 18 records into RDS](/images/1.4-Week4/create-table-add-data.png)

To verify, I ran `SHOW DATABASES`, `SHOW TABLES` and `SELECT * FROM user` in turn — the result set returned all 18 rows with every column populated, confirming the database on RDS was ready to serve the application.

> **Evidence:**
> ![Verifying the data with SHOW TABLES and SELECT * FROM user](/images/1.4-Week4/show-data.png)

#### Thursday — Connecting the application to RDS and monitoring with CloudWatch

I configured the **AWS FCJ Management** application on the app server to point at the RDS endpoint and ran it on port 5000. Visiting `54.169.53.137:5000` from my local machine, the Records page displayed all 18 users with View / Edit / Delete actions — the key difference from Week 3 being that the data now lives on a separately **managed database**, no longer sharing a machine with the application.

> **Evidence:**
> ![AWS FCJ Management application reading data from RDS on port 5000](/images/1.4-Week4/web.png)

I then explored the RDS instance's **Monitoring** tab — the place that aggregates the CloudWatch metrics a managed database provides out of the box: BinLogDiskUsage, BurstBalance, CPUCreditBalance, CPUCreditUsage…

> **Evidence:**
> ![RDS CloudWatch metrics: BinLogDiskUsage, BurstBalance, CPUCreditBalance](/images/1.4-Week4/monitor-rds-metrics.png)

Most interesting was the operational metric group: **CPUUtilization** rose and fell as I ran SQL statements, **DatabaseConnections** spiked exactly when the application connected, alongside DiskQueueDepth, EBSByteBalance% and FreeableMemory. Watching the charts reflect the very actions I had just performed made me appreciate the value of AWS monitoring the entire database tier for you.

> **Evidence:**
> ![CPUUtilization and DatabaseConnections metrics reflecting the application's activity](/images/1.4-Week4/monitor-rds-cpu-connections.png)

#### Friday — Automated backups and snapshot restore

On the last day of the week, I explored RDS backup capabilities in the **Maintenance & backups** tab: automated backups were enabled (1-day retention) with a fixed backup window, and AWS had already created an **Automated** snapshot in the **Available** state — with no action required from me at all.

> **Evidence:**
> ![RDS automated backup and automatic snapshot in the Available state](/images/1.4-Week4/rds-automated-backup.png)

I practiced **restoring** from this snapshot into a new instance named `first-cloud-db-restore`. The Console reported "Snapshot is being restored", and the Databases list showed 2 instances: `first-cloud-db-instance` (Available) and `first-cloud-db-restore` (Creating). An important takeaway: a restore never overwrites the original database — it always creates a new instance, which keeps the source data safe but also means the application must switch endpoints to use the restored copy.

> **Evidence:**
> ![Restoring the snapshot into the new instance first-cloud-db-restore](/images/1.4-Week4/restore-rds-snapshot.png)

### Week 4 Achievements:

* Understood the role of Amazon RDS — a database managed by AWS (automatic backups, monitoring, patching) compared with self-installing MySQL/MariaDB on EC2 as in Week 3.
* Built the complete infrastructure for the 2-tier model: VPC `first-cloud-vpc`, application-tier SG `first-cloud-app-sg` (SSH, HTTP, HTTPS, TCP 5000) and database-tier SG `first-cloud-db-sg` (MySQL 3306 only).
* Grasped the DB subnet group concept and why RDS requires subnets across multiple Availability Zones.
* Created and successfully connected to the RDS MySQL instance `first-cloud-db-instance` (`db.t4g.micro`) from the EC2 app server via the mysql client.
* Initialized the `first_cloud_users` database and the `user` table, loaded 18 records and verified them with `SHOW DATABASES`, `SHOW TABLES`, `SELECT`.
* Connected the AWS FCJ Management application to RDS and confirmed the data rendered fully on the web over port 5000.
* Learned to read RDS CloudWatch metrics (CPUUtilization, DatabaseConnections, FreeableMemory…) and relate them to the application's actual activity.
* Understood the RDS automated backup mechanism and practiced restoring a snapshot into the new instance `first-cloud-db-restore`.

### Challenges:

* **RDS takes quite a while to create**: the instance stayed in the Creating state for several minutes and at first I suspected a misconfiguration — in reality this is simply the normal time AWS needs to provision a managed database.
* **Database connection refused at first**: I had to re-check the whole dependency chain — RDS inside the subnet group, the correct `first-cloud-db-sg` attached, and the app server inside a VPC with a path to the database — before the mysql client could connect.
* **Restore does not replace the old instance**: I had assumed a restore would "recover in place"; in fact RDS always creates a new instance from the snapshot, so both cost and the application's endpoint need attention.

### Reflection:

Week 4 showed me the difference between **self-operated** and **managed**: it is the same MySQL, but on RDS I did not have to install anything or configure backups, and I received a full monitoring metric suite for free — in exchange for learning RDS-specific concepts like DB subnet groups and snapshots. Splitting the application and the database into two tiers with two dedicated Security Groups also changed how I look at architecture: security does not live in a single firewall but in each tier opening only the ports it needs. If I did this again, I would note down the endpoint and the dependency order (VPC → SG → subnet group → RDS) from the start to save time debugging connection issues.

### Conclusion:

Week 4 completed the picture of a standard web application on AWS: an EC2 app server and an RDS database cleanly separated into tiers, data seeded through the mysql client rendered fully in the AWS FCJ Management web app, along with CloudWatch monitoring and a backup/restore procedure verified through hands-on practice. No sensitive information such as AWS Account IDs, database passwords or key pairs is included in this report.
