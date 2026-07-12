---
title: "Week 2 Worklog"
date: 2026-04-27
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Master the Amazon VPC architecture: Subnet, Route Table, Internet Gateway, NAT Gateway and the firewall layers inside a VPC (Security Group, Network ACL).
* Build a complete VPC by hand following the [Getting Started with Amazon VPC and AWS Site-to-Site VPN](https://000003.awsstudygroup.com/vi/) lab from AWS Study Group.
* Deploy EC2 instances in public/private subnets and verify connectivity methods: SSH, EC2 Instance Connect Endpoint, Reachability Analyzer.
* Configure an AWS Site-to-Site VPN connecting the main VPC with a simulated on-premise environment.
* Enable VPC Flow Logs and monitor network traffic with CloudWatch.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Study VPC theory: Subnets, Route Table, Internet Gateway, NAT Gateway <br> - Learn about firewalls inside a VPC: Security Group, Network ACLs, VPC Resource Map <br> - Create VPC `ASG` (`10.10.0.0/16`), 4 subnets and an Internet Gateway | 27/04/2026 | 27/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 3   | - Create public/private Route Tables, add route `0.0.0.0/0` → IGW <br> - Attach subnet associations to each route table <br> - Create 3 layered Security Groups <br> - Enable VPC Flow Logs and inspect log streams on CloudWatch | 28/04/2026 | 28/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 4   | - Launch EC2 Public and EC2 Private <br> - SSH into EC2 Public, test internet connectivity and ping EC2 Private <br> - Chain SSH from EC2 Public into EC2 Private using the key pair <br> - Allocate an Elastic IP, create a NAT Gateway to give the private subnet outbound internet access | 29/04/2026 | 29/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 5   | - Analyze the network path with Reachability Analyzer <br> - Create an EC2 Instance Connect Endpoint in the private subnet <br> - Connect to EC2 Private directly from the Console via the EIC Endpoint (no bastion required) | 30/04/2026 | 30/04/2026 | <https://000003.awsstudygroup.com/vi/> |
| 6   | - Build the simulated on-premise environment: VPC `ASG VPN`, subnet, IGW, SG and an EC2 instance acting as the Customer Gateway <br> - Create the Virtual Private Gateway, Customer Gateway and VPN Connection <br> - Enable Route Propagation on the public/private route tables | 01/05/2026 | 01/05/2026 | <https://000003.awsstudygroup.com/vi/> |

### Work Details:

#### Monday — Building the network foundation: VPC, Subnets and Internet Gateway

On the first day of the week, I studied the lab's theory section to understand the role of each component: Subnets partition the VPC's address range across Availability Zones, Route Tables decide where traffic flows, the Internet Gateway is the door to the internet for public subnets, while the NAT Gateway lets private subnets initiate outbound internet connections without being reachable from outside.

I then created a VPC named `ASG` with CIDR block `10.10.0.0/16`, selecting **VPC only** and keeping the default Tenancy, and enabled **DNS hostnames** and **DNS resolution** so that instances inside the VPC can resolve domain names.

> **Evidence:**
> ![Create VPC ASG](/images/1.2-Week2/create-vpc.png)

Next, I divided the VPC into 4 subnets spread across 2 Availability Zones for high availability:

* `Public Subnet 1` — `10.10.1.0/24` (ap-southeast-1a)
* `Public Subnet 2` — `10.10.2.0/24` (ap-southeast-1b)
* `Private Subnet 1` — `10.10.3.0/24` (ap-southeast-1a)
* `Private Subnet 2` — `10.10.4.0/24` (ap-southeast-1b)

> **Evidence:**
> ![Create Subnets](/images/1.2-Week2/create-subnet.png)

For the 2 public subnets, I turned on **Enable auto-assign public IPv4 address** so that EC2 instances launched there automatically receive a public IP — a prerequisite for direct access from the internet.

> **Evidence:**
> ![Enable auto-assign public IP for Public Subnet 1](/images/1.2-Week2/enable-auto-assign-publicsubnet1.png)
> ![Enable auto-assign public IP for Public Subnet 2](/images/1.2-Week2/enable-auto-assign-publicsubnet2.png)

At the end of the day, I created an Internet Gateway and attached it to the `ASG` VPC. This component is mandatory for traffic from public subnets to reach the internet.

> **Evidence:**
> ![Create Internet Gateway](/images/1.2-Week2/create-internet-gateway.png)

#### Tuesday — Routing, Security Groups and VPC Flow Logs

I created `Route table-Public` attached to the `ASG` VPC, then used **Edit routes** to add the route `0.0.0.0/0` pointing to the Internet Gateway. The route table ended up with 2 Active routes: the local route `10.10.0.0/16` and the internet route through the IGW.

> **Evidence:**
> ![Create Public Route table](/images/1.2-Week2/create-public-route-table.png)
> ![Add route 0.0.0.0/0 to the Internet Gateway](/images/1.2-Week2/add-new-route.png)

I associated the 2 public subnets with this route table via the **Subnet associations** tab, then created an additional `Route table-Private` for the 2 private subnets — initially containing only the local route, keeping the private subnets fully isolated from the internet.

> **Evidence:**
> ![Edit subnet associations](/images/1.2-Week2/edit-subnet-associations.png)
> ![Create Private Route table](/images/1.2-Week2/create-private-route-table.png)

For the firewall layer, I created 3 Security Groups following a layered model:

* `Public subnet - SG`: allows SSH (port 22) from **My IP** and ICMP from anywhere — only my machine can SSH into the bastion.
* `Private subnet - SG`: only allows SSH from `Public subnet - SG` — EC2 Private can never be SSH'd directly from the internet.
* `VPC-Endpoints-SG`: allows HTTPS (port 443) from the `10.10.0.0/16` range — serving VPC endpoints that accept connections from inside the VPC.

> **Evidence:**
> ![Create Public subnet SG](/images/1.2-Week2/create-public-subnet-sg.png)
> ![Create Private subnet SG](/images/1.2-Week2/create-private-subnet-sg.png)
> ![Create VPC Endpoints SG](/images/1.2-Week2/create-vpc-endpoint-sg.png)
> ![Security Group list](/images/1.2-Week2/sg-list.png)

To finish the day, I enabled **VPC Flow Logs** for the `ASG` VPC, shipping logs to the CloudWatch log group `/aws/vpc/flowlogs`. On CloudWatch, each ENI in the VPC produces its own log stream (`eni-xxx-all`), making it possible to trace all traffic in and out of every network interface.

> **Evidence:**
> ![Enable VPC Flow Logs](/images/1.2-Week2/create-vpc-flowlogs.png)
> ![Log streams on CloudWatch](/images/1.2-Week2/logstreams.png)

#### Wednesday — Deploying EC2, connectivity testing and NAT Gateway

I launched 2 EC2 instances (Amazon Linux 2023, `t2.micro`):

* `EC2 Public` placed in Public Subnet 1 with `Public subnet - SG` attached and a public IP — serving as the bastion host.
* `EC2 Private` placed in Private Subnet 2 (`10.10.4.21`) with `Private subnet - SG` attached and no public IP.

> **Evidence:**
> ![Create EC2 Public](/images/1.2-Week2/create-ec2-public.png)
> ![Create EC2 Private](/images/1.2-Week2/create-ec2-private.png)

After SSH-ing into `EC2 Public`, I ran connectivity checks: pinging `8.8.8.8` succeeded (0% packet loss), proving the public subnet reaches the internet through the IGW; pinging `amazon.com` showed 100% packet loss because the target blocks ICMP — a practical lesson that you cannot rely on pinging a domain name alone to conclude the network is down. Pinging `EC2 Private` (`10.10.4.21`) succeeded thanks to the VPC's local route.

> **Evidence:**
> ![Connectivity test from EC2 Public](/images/1.2-Week2/connecting-test.png)
> ![Ping EC2 Private from EC2 Public](/images/1.2-Week2/ping-ec2-private.png)

To chain SSH into `EC2 Private`, I copied the `aws-keypair.pem` key pair onto `EC2 Public`, set `chmod 400` permissions and SSH'd using the private IP. From `EC2 Private`, I tried pinging outward — it failed at first because the private subnet had no route to the internet yet.

> **Evidence:**
> ![Copy key pair onto EC2 Public](/images/1.2-Week2/copy-key-on-ec2-public.png)
> ![SSH from EC2 Public into EC2 Private](/images/1.2-Week2/ssh-to-private.png)

I allocated an **Elastic IP** and created a **NAT Gateway** in the public subnet, then added the route `0.0.0.0/0` → NAT Gateway to `Route table-Private`. Re-testing from `EC2 Private`: pinging `amazon.com` now received replies — the private instance initiates outbound internet connections through NAT while the internet still cannot connect back in.

> **Evidence:**
> ![Allocate Elastic IP](/images/1.2-Week2/allocate-elastic-ip.png)
> ![Create NAT Gateway](/images/1.2-Week2/create-nat-gateway.png)
> ![EC2 Private reaching the internet through the NAT Gateway](/images/1.2-Week2/ping-test-amazon-com.png)

#### Thursday — Reachability Analyzer and EC2 Instance Connect Endpoint

I used **VPC Reachability Analyzer** to create an analysis path from `EC2 Public` to `EC2 Private`, verifying the network configuration with tooling instead of manual checks. The result was **Reachable**, and the Path details view showed every hop the traffic passes through (source ENI → security group → route table → destination ENI) — extremely useful for diagnosing connectivity issues without SSH-ing into any machine.

> **Evidence:**
> ![Create Reachability Analyzer](/images/1.2-Week2/create-reachability-analyzer.png)
> ![Traffic path details](/images/1.2-Week2/path-details.png)

Next, I created an **EC2 Instance Connect Endpoint** (`EC2 private endpoint`) in the private subnet with `VPC-Endpoints-SG` attached. This endpoint enables SSH connections to instances in private subnets **directly from the AWS Console** — no bastion host, no public IP, and no ports exposed to the internet.

> **Evidence:**
> ![Create EIC Endpoint](/images/1.2-Week2/create-eic-endpoint.png)
> ![EIC Endpoint in Available state](/images/1.2-Week2/create-ec2-endpoint.png)

Once the endpoint reached the **Available** state, I chose Connect on `EC2 Private` with connection type **Connect using EC2 Instance Connect Endpoint** — landing straight in the shell of instance `10.10.4.21` right in the browser. Compared with the SSH-chaining approach from the previous day, this method is considerably safer and more convenient.

> **Evidence:**
> ![Connect to EC2 Private via the EIC Endpoint](/images/1.2-Week2/connecting-ec2-private-instance.png)

#### Friday — Site-to-Site VPN: connecting AWS with a simulated on-premise environment

On the last day of the week, I built a **simulated on-premise environment** using a separate VPC named `ASG VPN` (with a CIDR range distinct from the main VPC's `10.10.0.0/16`), including its own subnet, Internet Gateway and Security Group.

> **Evidence:**
> ![Create VPC ASG VPN](/images/1.2-Week2/create-asg-vpn.png)
> ![Create subnet for the VPN VPC](/images/1.2-Week2/create-vpn-subnet.png)
> ![Create Internet Gateway for the VPN VPC](/images/1.2-Week2/create-internet-gateway-vpn.png)
> ![Create Security Group for the VPN public subnet](/images/1.2-Week2/create-vpn-public-sg.png)

Inside this VPC, I launched the EC2 instance `Customer Gateway instance` playing the role of the on-premise VPN device (running VPN software to establish the IPSec tunnels).

> **Evidence:**
> ![EC2 Customer Gateway instance](/images/1.2-Week2/create-ec2-gateway.png)

On the AWS side, I created the Site-to-Site VPN components one by one:

1. **Virtual Private Gateway (VGW)** — the VPN endpoint on the AWS side, attached to the `ASG` VPC.
2. **Customer Gateway (CGW)** — registered with the public IP of the `Customer Gateway instance`, representing the on-premise VPN device.
3. **VPN Connection** — linking the VGW with the CGW; AWS automatically provisions 2 IPSec tunnels for high availability.

> **Evidence:**
> ![Create Virtual Private Gateway](/images/1.2-Week2/create-vpn-gateway.png)
> ![Create Customer Gateway](/images/1.2-Week2/create-customer-gateway.png)
> ![Create VPN Connection](/images/1.2-Week2/create-vpn-connection.png)

Finally, I enabled **Route Propagation** on both `Route table-Public` and `Route table-Private` so the route tables automatically learn routes to the on-premise network from the Virtual Private Gateway, instead of adding static routes manually.

> **Evidence:**
> ![Enable Route Propagation for Route table Public](/images/1.2-Week2/enable-route-propagation-public.png)
> ![Enable Route Propagation for Route table Private](/images/1.2-Week2/enable-route-propagation-private.png)

### Week 2 Achievements:

* Understood and can explain the roles of Subnet, Route Table, Internet Gateway and NAT Gateway in the VPC architecture.
* Built the complete `ASG` VPC (`10.10.0.0/16`) with 4 subnets across 2 Availability Zones, enabling DNS hostnames/resolution and auto-assign public IP for the public subnets.
* Configured the routing model correctly: public subnets reach the internet through the Internet Gateway, private subnets get one-way outbound access through the NAT Gateway (with an Elastic IP).
* Designed layered Security Groups: the bastion only accepts SSH from My IP, the private instance only accepts SSH from the public subnet's SG, and the VPC endpoint only accepts HTTPS from inside the VPC.
* Deployed and verified EC2 Public/Private connectivity through multiple methods: direct SSH, chained SSH via the bastion, and EC2 Instance Connect Endpoint from the Console.
* Used Reachability Analyzer to analyze traffic paths and diagnose network configuration.
* Enabled VPC Flow Logs and read per-ENI log streams on CloudWatch.
* Set up an AWS Site-to-Site VPN: Virtual Private Gateway, Customer Gateway, VPN Connection with 2 IPSec tunnels and Route Propagation.
* Practical lesson learned: a domain that blocks ICMP failing to answer ping does not mean the internet connection is down — verify with multiple targets (public IPs such as `8.8.8.8`) before drawing conclusions.
* No sensitive information such as AWS Account IDs or key pairs is included in this report.
