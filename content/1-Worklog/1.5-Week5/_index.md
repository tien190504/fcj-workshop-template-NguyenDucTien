---
title: "Week 5 Worklog"
date: 2026-05-18
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Get familiar with Amazon CloudWatch — AWS's monitoring and observability service for infrastructure and applications.
* Set up the practice environment with the CloudFormation stack `FCJ-CloudWatch-Workshop`, which provisions a group of test EC2 instances (Instance-A → Instance-E).
* Explore CloudWatch Metrics: browse EC2 metrics, plot multiple metrics on one graph, add annotations, use metric math (`SEARCH`, `SORT`) and dynamic labels.
* View OS-level metrics collected by the CloudWatch Agent in the `CWAgent` namespace.
* Work with CloudWatch Logs: configure log group retention and create a metric filter that counts application log errors.
* Create a CloudWatch Alarm from the metric filter, attach an SNS email notification action, and consolidate monitoring on the `CloudWatch-Workshop` dashboard.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 2   | - Learn CloudWatch fundamentals: Metrics, Logs, Alarms, Dashboards <br> - Create the CloudFormation stack `FCJ-CloudWatch-Workshop` to provision the workshop infrastructure <br> - Browse the EC2 `CPUUtilization` metric and graph it for the test instances | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Plot 2 metrics with different units (`CPUUtilization`, `EBSWriteBytes`) on one graph with 2 Y axes <br> - Add a horizontal annotation (5% threshold) and a vertical annotation (`Job start`) to the graph | 19/05/2026 | 19/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Use metric math: `SEARCH` expressions to gather metrics in bulk, `SORT` to rank instances <br> - Browse the `procstat_memory_rss` metric in the `CWAgent` namespace <br> - Create dynamic labels with `${PROP('Dim.…')}` and fix an Invalid syntax error | 20/05/2026 | 20/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Set a 1-week retention policy for the `/ec2/linux/var/log/messages` log group <br> - Create the `PythonAppErrors` metric filter to count ERROR lines from the Python application logs | 21/05/2026 | 21/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Create the `PythonApplicationErrorAlarm` alarm (ERROR > 10 within 1 minute) attached to the SNS topic `Error_logs_reach_10` <br> - Confirm the SNS subscription via email <br> - Add the alarm widget to the `CloudWatch-Workshop` dashboard | 22/05/2026 | 22/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Detailed work performed:

#### Monday — Provisioning the workshop infrastructure with CloudFormation and getting started with CloudWatch Metrics

At the start of the week, instead of creating each resource by hand as in previous weeks, I used **CloudFormation** to provision the entire practice environment. The `FCJ-CloudWatch-Workshop` stack in the `ap-southeast-1` Region finished with the **CREATE_COMPLETE** status; the Events tab listed over 100 resource-creation events, including nested stacks that spawned the group of test EC2 instances. This was the first time I clearly saw the value of Infrastructure as Code: one template creates a whole fleet of identical servers in just a few minutes.

> **Evidence:**
> ![FCJ-CloudWatch-Workshop stack created successfully with CREATE_COMPLETE status](/images/1.5-Week5/create-cloudformation-stack.png)

Next, I opened **CloudWatch → Metrics → All metrics** and browsed into the **EC2 → Per-Instance Metrics** group. Filtering by the metric name `CPUUtilization`, the console found 11 matches corresponding to the instances running in the account — the graph was initially empty because no metric had been selected yet.

> **Evidence:**
> ![Browsing the EC2 CPUUtilization metric in CloudWatch, 11 matches found](/images/1.5-Week5/browse-ec2-cpu-metrics.png)

I ticked `CPUUtilization` for the two instances `ssmStack/Instance-A/test-instance` and `ssmStack/Instance-B/test-instance` created by the stack. Data points appeared on the graph (around 7.3% and 0.4%) — since the instances had only just started, only the first data points were available.

> **Evidence:**
> ![Selecting CPUUtilization of Instance-A and Instance-B to plot the graph](/images/1.5-Week5/select-instance-cpu-metrics.png)

#### Tuesday — Plotting metrics with different units and adding graph annotations

I tried plotting two metrics with **different units** on the same graph: adding `EBSWriteBytes` (unit: Bytes) of Instance-A next to `CPUUtilization` (unit: Percent). CloudWatch automatically split them onto two Y axes — Percent on the left, Bytes on the right (EBS write volume reached ~188M bytes) — making it possible to correlate CPU and I/O of the same machine without either metric being "flattened" by the difference in scale.

> **Evidence:**
> ![Plotting CPUUtilization and EBSWriteBytes on one graph with 2 separate Y axes](/images/1.5-Week5/add-ebs-write-bytes-metric.png)

On the **Options** tab, I added two kinds of annotations to the graph: a **horizontal annotation** marking the 5% CPU threshold (a red dashed horizontal line) and a **vertical annotation** labeled `Job start` marking the moment the working session began. Hovering over a data point, the tooltip showed the exact value of each metric at that time (EBSWriteBytes 188,156,800 — CPUUtilization 7.30). Annotations help anyone reading the graph immediately recognize the alert threshold and important event markers.

> **Evidence:**
> ![Adding a 5% horizontal annotation and a Job start vertical annotation](/images/1.5-Week5/add-graph-annotations.png)

#### Wednesday — Metric math (SEARCH, SORT), the CWAgent namespace and dynamic labels

The most technique-heavy day of the week: instead of ticking each metric manually, I used **metric math**. First came the `SEARCH` expression — I opened **Add query / Edit math expression** and entered `SEARCH("disk_used_percent", 'Average', 300)` to gather all instances' disk usage metrics into a single expression.

> **Evidence:**
> ![Entering the SEARCH("disk_used_percent", 'Average', 300) expression in Edit math expression](/images/1.5-Week5/edit-search-expression.png)

After clicking Apply, the **Stacked area** graph displayed the layered disk-used percentages of every instance matching the search condition — no need to know the instance list in advance; newly launched machines are picked up automatically.

> **Evidence:**
> ![SEARCH expression result displayed as a stacked area chart for all matching instances](/images/1.5-Week5/apply-search-expression.png)

Next was the `SORT` expression. I added `SORT(METRICS(), SUM, DESC, 10)` to rank the graphed metrics by their total value in descending order, limited to 10 results.

> **Evidence:**
> ![The SORT(METRICS(), SUM, DESC, 10) expression ranking metrics by total value](/images/1.5-Week5/sort-metrics-expression.png)

I then revised it to `SORT(e1, SUM, DESC, 3)` — keeping only the **top 3** instances with the highest CPU from the `SEARCH` result in expression `e1`. Combining SEARCH + SORT produces a self-updating "top N hottest machines" graph, which is extremely useful when operating many servers.

> **Evidence:**
> ![Revising to SORT(e1, SUM, DESC, 3) to get the top 3 instances with the highest CPU](/images/1.5-Week5/sort-top3-instances.png)

I also explored the **CWAgent** namespace — where the CloudWatch Agent installed on instances pushes OS-level metrics that EC2 does not provide by default. Browsing the dimension set `ImageId, InstanceId, InstanceType, exe, process_name` and filtering by `exe: cloudwatch` with the metric `procstat_memory_rss`, I got 4 results — the actual resident memory (RSS) consumed by the CloudWatch Agent process itself on each machine, plotted as a stacked chart reaching ~563.5M bytes.

> **Evidence:**
> ![The procstat_memory_rss metric of the CloudWatch Agent process in the CWAgent namespace](/images/1.5-Week5/cwagent-procstat-metrics.png)

At the end of the day I practiced **dynamic labels** so that each line's label is generated automatically from its dimensions. At first I mistakenly pasted the `${PROP('Dim.exe')} - ${PROP('Dim.InstanceId')}` syntax into the math expression box, and CloudWatch immediately reported **Error in expression e1 [Invalid syntax]** with the graph area turning red.

> **Evidence:**
> ![Invalid syntax error caused by pasting dynamic label syntax into the math expression box](/images/1.5-Week5/dynamic-label-error.png)

Once I realized `${PROP(…)}` is syntax meant for the **Label** field rather than the expression, I moved it to the Label column of the `SEARCH('{CWAgent,ImageId,InstanceId,…}')` expression — the graph rendered normally again and each line was automatically named after its corresponding process and instance.

> **Evidence:**
> ![Dynamic label working after being placed correctly in the Label field of the SEARCH expression](/images/1.5-Week5/dynamic-label-fixed.png)

#### Thursday — CloudWatch Logs: retention and metric filters

I moved on to **Logs → Log groups**. The account had 2 log groups: `/aws/vpc/flowlogs` (VPC Flow Logs from the Week 2 lab) and `/ec2/linux/var/log/messages` — system logs shipped from EC2. Log groups retain data forever by default (Never expire), which drives up cost, so I lowered the **retention** of `/ec2/linux/var/log/messages` to **1 week**; a green banner confirmed "The retention setting(s) … have been updated".

> **Evidence:**
> ![Updating the retention to 1 week for the /ec2/linux/var/log/messages log group](/images/1.5-Week5/set-log-retention.png)

Next, I created a **metric filter** named `PythonAppErrors` on this log group to count log lines containing errors from the Python application running on the instance. The console reported success, and the Log group details showed **Metric filters: 1** — from this point on, text lines in the log had been transformed into a numeric count metric that can be graphed and alarmed on.

> **Evidence:**
> ![The PythonAppErrors metric filter created on the log group, Metric filters: 1](/images/1.5-Week5/create-metric-filter.png)

#### Friday — Alarms, SNS email notifications and the consolidated dashboard

On the last day of the week, I created the **alarm** `PythonApplicationErrorAlarm` based on the previous day's filter metric, with the condition: `/var/log/messages - ERROR > 10 for 1 datapoints within 1 minute` — if the application writes more than 10 ERROR lines within 1 minute, the alarm switches to the alert state. The alarm was in the **OK** state, and the Actions column showed **Actions enabled** with a Warning action sent to an SNS topic.

> **Evidence:**
> ![The PythonApplicationErrorAlarm alarm with the ERROR > 10 within 1 minute condition, in OK state](/images/1.5-Week5/create-error-alarm.png)

The alarm's action pointed to the SNS topic `Error_logs_reach_10` with my email as the subscription. AWS sent the **"AWS Notification - Subscription Confirmation"** email — notably, this message landed in Gmail's **Spam** folder and I only found it by checking there deliberately.

> **Evidence:**
> ![The SNS subscription confirmation email sitting in Gmail's Spam folder](/images/1.5-Week5/sns-confirmation-email.png)

Clicking **Confirm subscription** in the email, the SNS page displayed **"Subscription confirmed!"** along with the subscription ID — from now on, every time the alarm changes state, I will receive a real alert email.

> **Evidence:**
> ![The SNS page confirming Subscription confirmed for the Error_logs_reach_10 topic](/images/1.5-Week5/confirm-sns-subscription.png)

Finally, I added the alarm widget to a **dashboard** to centralize monitoring in one place. The console reported "1 new widget(s) have been added to your dashboard CloudWatch-Workshop".

> **Evidence:**
> ![Alarm widget added to the CloudWatch-Workshop dashboard successfully](/images/1.5-Week5/add-alarm-to-dashboard.png)

Opening the `CloudWatch-Workshop` dashboard, the `PythonApplicationErrorAlarm` widget displayed the ERROR count chart over time along with the red alert threshold. One important detail: the dashboard had **Autosave: Off** — the newly added widget was not saved automatically, and the change is only kept after clicking **Save dashboard**.

> **Evidence:**
> ![The CloudWatch-Workshop dashboard with the Python application error monitoring widget](/images/1.5-Week5/cloudwatch-dashboard.png)

### Week 5 Achievements:

* Understood Amazon CloudWatch's role in infrastructure monitoring: Metrics, Logs, Alarms and Dashboards working together as a closed observability loop.
* Provisioned the workshop environment with the `FCJ-CloudWatch-Workshop` CloudFormation stack and clearly experienced the benefits of Infrastructure as Code.
* Browsed and graphed the `CPUUtilization` metric of the EC2 instances; plotted 2 metrics with different units (`CPUUtilization`, `EBSWriteBytes`) on one graph with 2 Y axes.
* Added horizontal/vertical annotations to mark thresholds and event markers on graphs.
* Used metric math: `SEARCH` to gather metrics in bulk by condition and `SORT` to rank the top N instances by value — combined into a graph that updates itself when new machines appear.
* Read OS-level metrics (`procstat_memory_rss`) collected by the CloudWatch Agent in the `CWAgent` namespace.
* Created dynamic labels with `${PROP('Dim.…')}` and understood where this syntax applies after resolving an Invalid syntax error on my own.
* Configured 1-week retention for the `/ec2/linux/var/log/messages` log group to control log storage costs.
* Created the `PythonAppErrors` metric filter, turning ERROR log lines into a countable metric.
* Created the `PythonApplicationErrorAlarm` alarm (ERROR > 10 within 1 minute) attached to the SNS topic `Error_logs_reach_10`, and confirmed the email subscription successfully.
* Added the alarm widget to the `CloudWatch-Workshop` dashboard and learned how to save the dashboard properly.

### Difficulties encountered:

* **The SNS confirmation email landed in Spam**: AWS's "Subscription Confirmation" message was classified as junk by Gmail; without deliberately checking, the subscription would stay pending forever and the alarm would never deliver any notification.
* **Dynamic label reporting Invalid syntax**: I pasted the `${PROP(…)}` syntax into the math expression box, breaking the expression; only after realizing this is Label-field syntax rather than a metric math function did the graph display correctly.
* **The dashboard does not auto-save**: Autosave is Off by default, so a newly added widget is lost if you forget to click Save dashboard — the console does warn about this, but it is quite easy to overlook.

### Lessons learned:

Week 5 shifted my perspective from "building systems" to "observing systems": no matter how well the infrastructure is built, you need to know whether it is healthy or ailing. The most impressive part was the **log → metric filter → alarm → SNS → email** transformation chain: an ERROR text line in a log file becomes a number, the number crossing a threshold becomes an alert, and the alert lands straight in the operator's inbox. Metric math (`SEARCH`, `SORT`) also demonstrated "rule-based" monitoring instead of "list-based" monitoring — graphs automatically pick up new machines without manual editing. If I were to do it again, I would confirm the SNS subscription right when creating the alarm (and check the Spam folder too) before testing the alert condition.

### Conclusion:

Week 5 wrapped up with a complete monitoring system on CloudWatch: infrastructure provisioned automatically with CloudFormation, metrics graphed and ranked with metric math, application logs turned into an email-sending alarm via SNS, and everything consolidated on the `CloudWatch-Workshop` dashboard. No sensitive information such as the AWS Account ID, full email address or account-identifying ARNs is included in this report.
