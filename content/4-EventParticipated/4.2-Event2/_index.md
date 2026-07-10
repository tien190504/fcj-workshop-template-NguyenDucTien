---
title: "Event 2"
date: 2026-06-06
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Event Report: "First Cloud Journey Community Day"

### 1. Event Introduction

After my first eye-opening experience at FCAJ Community Day back in May, I signed up to attend the **First Cloud Journey (FCJ) Community Day** — a community gathering that brought together speakers from many different backgrounds: engineers working at **Endava** and **Central Retail Group**, student researchers from **HUTECH** and **Swinburne**, and core members of the FCJ community. While the previous event leaned toward AI Agents and career strategy, this program covered a much wider spectrum: from infrastructure security, containers, and multiplayer gaming on the cloud, to GenAI, soft skills, and career pathways.

What I liked most about this event was that the sessions didn't stop at theory: nearly every technical session came with a live demo — a real-time network attack detection dashboard, a multiplayer rock-paper-scissors game running entirely on serverless infrastructure, and a production-ready Docker packaging workflow. For an intern studying AWS like me, seeing the services I've been learning assembled into complete systems was an extremely valuable experience.

### 2. Participation Goals

Before attending the event, I set a few specific goals for myself:

* Understand how to combine **AWS WAF with Machine Learning** for attack detection — a topic directly related to the security area I'm exploring during my internship.
* Strengthen my fundamentals on **Docker and containerization**, which I had only used at the "run the commands" level without truly understanding the underlying mechanics.
* See how a real **end-to-end serverless architecture** (API Gateway, Lambda, DynamoDB) operates through the multiplayer game case study.
* Learn about **GraphRAG** — a concept I had only heard the name of, without any idea how it differs from regular RAG.
* Listen to career guidance from people ahead of me, especially the path from an entry-level position upward.

### 3. Time

* **Date:** Saturday, 06/06/2026, with the main program running through the morning and early afternoon.

### 4. Location

* The event was held offline in Ho Chi Minh City, organized by the **First Cloud Journey** community together with its partner organizations.

### 5. Agenda Timeline

The program was arranged in a very logical flow: starting from **protecting the infrastructure**, moving to **packaging applications**, then to **people and skills**, and closing with **cutting-edge AI applications**:

| Session | Speaker | Organization | Topic |
| ------- | ------- | ------------ | ----- |
| 1 | Le Hoang Gia Dai | HUTECH University / AWS G3 | AWS WAF combined with a Machine Learning NIDS |
| 2 | Bao Huynh | Endava / ITea Lab | Docker — A Containerization Technology |
| 3 | Tran Trung Vinh | Central Retail Group | Career Path: from IT Helpdesk to Senior Sysadmin |
| 4 | Nguyen Quoc Bao | FCJ Community | Multiplayer in the Cloud with WebSockets |
| 5 | Truong Huy Phuoc | FCJ Community | The Art of Effective Teamwork |
| 6 | Viet Phat | Swinburne University | GraphRAG with Amazon Bedrock & Neptune |

### 6. Session Details

#### Session 1 — AWS WAF & Machine Learning NIDS (Le Hoang Gia Dai)

The opening session raised a very timely problem: traditional WAFs operate on **predefined rules (rule-based)**, which makes them nearly powerless against **Zero-day** attacks — threats that no existing "rule" has ever described. The solution Dai presented was to add a **NIDS (Network Intrusion Detection System)** that detects intrusions based on **behavior**, powered by Machine Learning.

For the data, the team chose the **CSE-CIC-IDS2018** dataset over the aging NSL-KDD, because it contains modern attack scenarios (Web Attacks, Botnet, CSRF...). The preprocessing part made me realize that real-world ML is far messier than textbooks suggest: merging a pile of CSV files, removing corrupted labels, handling negative/NaN/infinite values, and most importantly **balancing the classes** so the model doesn't miss rare attack types.

The team experimented with several algorithms (Random Forest, XGBoost, AE + XGBoost), but **LightGBM** came out on top thanks to its balance between speed and accuracy:

| Metric | Result |
| ------ | ------ |
| Accuracy | 0.9586 |
| Precision (macro) | 0.9653 |
| Recall (macro) | 0.9586 |
| F1 (macro) | 0.9571 |
| AUC-ROC | 0.9982 |

The deployment architecture is fully serverless: AWS WAF logs are pushed by **Kinesis Data Firehose** into **S3**, **Lambda** triggers the ML model for analysis, alerts are centralized in **Security Hub**, and notifications are sent via **SNS**. The demo of the dashboard reacting in real time to attack traffic drew audible reactions from the whole room.

#### Session 2 — Docker: the foundation of Cloud Native (Bao Huynh)

Bao Huynh from Endava presented Docker in a way that made me understand it's not just a "packaging tool" but a prerequisite for Microservices and CI/CD. The comparison table with traditional virtual machines helped me grasp the essence very quickly:

| Characteristic | Virtual Machines | Containers (Docker) |
| -------------- | ---------------- | ------------------- |
| Operating system | Each VM has its own Guest OS (heavy) | Shares the Host OS kernel (lightweight) |
| Size | Gigabytes | Just a few MB to a few hundred MB |
| Startup time | Measured in minutes | Measured in milliseconds |
| Performance | Limited by the Hypervisor | Near-native |
| Density per host | Around 10–100 VMs | Up to thousands of containers |

The part I appreciated most was the **Image Layers** mechanism: an image consists of multiple **read-only** layers generated by Dockerfile instructions (`FROM`, `RUN`, `COPY`...), plus a single **writable** layer when the container runs. Thanks to this, Docker can reuse its **cache** — unchanged layers are never rebuilt — which dramatically shortens CI/CD pipeline times. Until now I had been rebuilding from scratch every time I changed code; it turns out that simply ordering the Dockerfile instructions sensibly saves a huge amount of time.

#### Session 3 — From IT Helpdesk to Senior Sysadmin (Tran Trung Vinh)

Vinh from Central Retail recounted his own career journey as a competency framework: **Helpdesk** (handling day-to-day incidents) → building his own **Networking & Linux lab** → moving into **Infrastructure** (virtualization, cloud) → **Senior Sysadmin** (automation and security). The key point he emphasized was the shift in **mindset**, not just in skills:

* From managing physical servers with manual configuration to the cloud model of **pay-as-you-go**, elastic scaling, and leveraging Managed Services.
* From "clicking around the console" to **Infrastructure as Code** with Terraform — infrastructure lives in configuration files, with repeatable and consistent deployments.

The advice that stuck with me most: *a portfolio of hands-on projects is worth far more than stacking up certificates without practice*. As a student working through my internship workshop, that line hit the "study just to pass certifications" mentality I used to have.

#### Session 4 — Multiplayer in the Cloud: Godot & AWS WebSockets (Nguyen Quoc Bao)

This was the most fun session for me, featuring a demo of a **multiplayer Rock-Paper-Scissors game** running entirely serverless. Bao explained why **WebSocket** was chosen over UDP/ENet: UDP is faster, but WebSocket provides a persistent **full-duplex** connection with high reliability — a great fit for turn-based games or lobby systems.

The architecture flow: **Godot Client → API Gateway (WebSocket) → Lambda → DynamoDB**. API Gateway uses a **Route Key** (`$request.body.action`) to route each request type (`$connect`, `$disconnect`, custom actions) to the corresponding Lambda function. Since Lambda is **stateless** — it remembers nothing between invocations — the entire game state must live in DynamoDB, with each item containing `connectionId` (partition key), `status` (waiting/matched), `opponentId`, and `choice` (rock/paper/scissors).

The "gotchas" section was the most valuable part to me:

* **Stale connections (GoneException):** a player disconnects abruptly but their data remains in DynamoDB → Lambda tries to send a message to a connection that no longer exists and throws an error. You must catch this exception and clean up the garbage data immediately.
* **The cost of Scan:** using `Scan` to find waiting players works at demo scale, but as users grow it becomes both slow and expensive — you need to switch to a **Global Secondary Index (GSI)**.
* **Latency from statelessness:** every piece of state must be continuously read from and written to DynamoDB — that's the price you pay for serverless scalability.

#### Session 5 — The Art of Effective Teamwork (Truong Huy Phuoc)

Sandwiched between the technical sessions was a very down-to-earth soft-skills talk. Phuoc distilled team performance into **4 golden rules**:

1. **Clear, shared goals** — the whole team looks in the same direction.
2. **Right person, right place** — assign work based on each member's strengths.
3. **Open communication** — active listening, honest but constructive feedback.
4. **Personal accountability** — once you take on a task, you commit to finishing it.

He also stressed that tools (Slack, Discord, Trello) aren't there "for show" but to keep information flowing continuously, preventing knowledge from living in only one person's head. Listening to this, I immediately thought of my own capstone team — we were missing exactly rules #1 and #4.

#### Session 6 — GraphRAG with Amazon Bedrock & Neptune (Viet Phat)

The closing session was also the most mind-expanding for me. Phat from Swinburne framed the problem with an example question: *"Where is the headquarters of the company acquired by the corporation founded by Jeff Bezos?"* — traditional RAG struggles badly with this kind of question because it requires **multi-hop reasoning**: connecting multiple scattered entities together. **GraphRAG** solves this by organizing knowledge into a **graph** (nodes and edges), allowing you to traverse the relationships.

Two implementation routes on AWS were analyzed:

* **Fully Managed Route:** use **Amazon Bedrock Knowledge Bases** integrated with **Neptune Analytics** — every step from chunking and entity extraction to creating embeddings and discovering relationships is automated.
* **Custom Route:** use **LlamaIndex** to build the data processing pipeline, store it in **Amazon Neptune**, and query with the **Cypher** language — offering deeper customization and full control over how the graph is built and queried.

### 7. What I Learned

* **Security must shift from rule-based to behavior-based:** WAF is a necessary but insufficient layer of defense; only by adding ML-based anomaly detection can you cope with Zero-days. The right mindset is "prevent first, fix later."
* **Understanding mechanisms beats memorizing commands:** grasping Docker's Image Layers and cache taught me *why* a Dockerfile should be ordered a certain way, instead of just copying templates from the internet.
* **Serverless doesn't mean you can ignore state:** because Lambda is stateless, designing where state lives (DynamoDB), handling dead connections, and optimizing queries (GSI instead of Scan) is the genuinely hard part of the problem.
* **GraphRAG upgrades an LLM's reasoning ability:** when a question requires chaining multiple relationships, a graph structure achieves what pure vector search cannot.
* **A career is a series of mindset shifts:** from fixing incidents → understanding systems → automating them. Hands-on experience and an operations mindset are worth more than a list of certificates.
* **Soft skills are the infrastructure of hard skills:** no matter how powerful the technology, it's meaningless if the team lacks shared goals and personal accountability.

### 8. Notable Technologies

* **Compute & Integration:** AWS Lambda, API Gateway (WebSocket), EC2, ALB.
* **Storage & Database:** Amazon S3, DynamoDB (with GSI), Amazon Neptune.
* **Security & Monitoring:** AWS WAF, Security Hub, GuardDuty, Kinesis Data Firehose, CloudWatch, Amazon SNS.
* **AI/ML & GenAI:** Amazon Bedrock (Knowledge Bases), Neptune Analytics, LightGBM, XGBoost, Random Forest, LlamaIndex, the Cypher query language.
* **DevOps & others:** Docker (Image Layers, build cache), Terraform (IaC), Godot Engine.

### 9. What I Can Apply to Personal Projects

* **Rewrite the Dockerfiles of my old projects:** order the instructions so that rarely-changing layers sit at the bottom to leverage the cache, then measure build times before and after.
* **Apply the DynamoDB lessons to my internship workshop:** whenever I need to look up items by a non-key attribute, use a GSI from the start instead of Scan — cheaper and faster as the data grows.
* **Build a small serverless WebSocket demo:** a chat app or turn-based game with API Gateway + Lambda + DynamoDB, paying attention to handling GoneException to fully understand the connection lifecycle.
* **Bring Terraform into my personal labs:** instead of creating VPCs and subnets by hand on the console like in previous weeks, I'll try describing that same infrastructure in Terraform to get a repeatable lab.
* **Build a portfolio following Vinh's advice:** prioritize completing 1–2 real, running projects with clear architecture and documentation, rather than spreading effort across many unfinished repos.
* **Explore GraphRAG at the beginner level:** start with Bedrock Knowledge Bases (the managed route) before attempting the custom route with LlamaIndex + Neptune.

### 10. Reflections After the Event

Compared to the previous event, I arrived this time with a much more proactive mindset — knowing what I wanted to hear and what I needed to ask. What struck me most was the **diversity of starting points** among the speakers: some are students like me (Dai, Phat) yet have already built a complete NIDS or a GraphRAG pipeline; others worked their way up from Helpdesk like Vinh. That shattered the "I have to be good enough before I dare to build" mentality — everyone starts somewhere; what matters is actually building.

On the technical side, I loved how the demos didn't hide the "ugly" parts: the GoneException errors, the cost of the Scan operation, the latency of a stateless architecture — things that online tutorials rarely mention but that practitioners face every day. The community's motto — *"Where you start doesn't matter — just keep moving forward"* — sounds simple, but it's what stayed with me the longest.

### 11. Conclusion

FCJ Community Day gave me a complete picture of the Cloud-Native ecosystem: intelligent security at the outer layer, standardized containers at the packaging layer, serverless at the operations layer, GraphRAG at the application layer — and people with teamwork skills at the center. Three things I'll do right after the event: (1) optimize my Dockerfiles and try Terraform for my internship labs, (2) build a small serverless WebSocket demo to experience the "gotchas" firsthand, and (3) start building a portfolio oriented around real, hands-on projects. The event reminded me once again that there are no shortcuts on the Cloud learning path, but there is always a community walking alongside you.
