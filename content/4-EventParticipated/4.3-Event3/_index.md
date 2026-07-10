---
title: "Event 3"
date: 2026-06-27
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# Event Report: "FCAJ Community Day - June 2026"

### 1. Event Introduction

This was the third event I attended during my internship, and my return to the **FCAJ (First Cloud AI Journey)** community after a great first impression at the Community Day back in May. The June 2026 edition brought together speakers from **AWS**, **CloudThinker**, **Renova Cloud**, **Cloud Kinetics**, and **Noventis**, with one very clear theme running throughout: GenAI has moved past the "wow, look at this demo" phase and entered the **enterprise execution** phase — where every solution must answer questions about permissions, cost, and risk.

The event ran in parallel across two conference floors combined with a live stream, so the atmosphere felt like a proper conference rather than a small meetup. What I liked most was that nearly every session featured a live demo — from a Voice Bot speaking Vietnamese to answer questions about Apple products, to a DevOps Agent performing root cause analysis on its own while the system was under a DDoS attack. My overall feeling after the morning: AI in infrastructure is no longer something "coming soon" — it is already running in production at Vietnamese banks and enterprises.

> ![Program opening](/images/4.3-Event3/opening.png)
> *Steve Tran opening the program with the first session on AgenticOps*

### 2. Participation Goals

Learning from the two previous events, I set more specific goals this time:

* Understand why practitioners choose **Multi-agent** over a single "Super Agent" — a topic I had heard about at the May Community Day but didn't fully grasp from the operations perspective.
* Learn how **Vietnamese is handled in Voice AI** — a "low-resource language" problem that English-language materials barely cover.
* See what a **DevOps AI Agent** can actually do in an incident-handling workflow, and where its limits are.
* Hear the security perspective on letting AI "touch" enterprise data — especially the private connectivity architecture over **PrivateLink** that I had only studied in theory during my VPC workshop.
* Observe the career paths of those ahead of me to adjust my own learning plan.

### 3. Time

* **Date:** Saturday, 27/06/2026, with the main program taking place in the morning.

### 4. Location

* The event was held offline at **Floor 26 & Floor 36, Bitexco Financial Tower**, No. 2 Hai Trieu Street, Sai Gon Ward, District 1, Ho Chi Minh City, combined with a **live stream** for remote attendees.

### 5. Agenda Timeline

The program flowed from **mindset and career paths**, through **applied AI solutions** (Voice, DevOps, HR), and closed with **security** — exactly the order an enterprise would follow when bringing AI into operations:

| Session | Speaker | Organization | Topic |
| ------- | ------- | ------------ | ----- |
| 1 | Steve Tran | CloudThinker | The future of Cloud Infrastructure and the Multi-agent strategy |
| 2 | Hieu Nghi, Kiet, Trung | R-AI / Renova Cloud | Voice AI: optimizing Vietnamese communication for banking |
| 3 | Bao & Nguyen | Cloud Kinetics | DevOps AI Agent: automating operations, reducing MTTR |
| 4 | Truong & Minh Anh | Noventis | AI in Human Resources with Amazon Q Business |
| 5 | Toan & Nghi | AWS / Renova Cloud | Private, secure connectivity for Enterprise AI & MCP |

### 6. Session Details

#### Session 1 — The future of Cloud Infrastructure and the Multi-agent strategy (Steve Tran)

Steve — founder of CloudThinker — opened with his own career story, and the detail that surprised me most: before becoming a Solutions Architect at AWS, he **failed the Azure exam 3–4 times** due to gaps in his fundamentals. The lesson he drew wasn't "keep retaking until you pass," but rather: solid fundamentals and the ability to read market trends ahead of time are what determine a long-term career path.

> ![AgenticOps for your Cloud session](/images/4.3-Event3/agenticops-session.jpg)
> *Steve Tran presenting "AgenticOps for your Cloud" to the audience*

The technical part revolved around a very timely architecture question: **Single Agent or Multi-agent?** After two years of research, the CloudThinker team found that a "Super Agent" can handle up to 95% of tasks, yet they still chose **specialized Multi-agents** for two reasons I found very convincing:

* **Access control (RBAC):** each agent is granted exactly the permissions for its scope of work — mandatory in an enterprise environment.
* **Context Window optimization:** a specialized agent doesn't get "diluted" with irrelevant data, responding faster and cheaper.

> ![Specialist Agents, Custom Agents slide](/images/4.3-Event3/multi-agent-slide.png)
> *The "Specialist Agents. Custom Agents." slide — each specialized agent owns its own scope of work*

The applications already running in production: incident investigation (cut from hours down to minutes), fully automated FinOps cost optimization, automated code review, and continuous pentesting. The message I noted down: as Microservices systems balloon, complexity and technical debt exceed what humans can oversee — AI won't replace good engineers, but it is the only answer to that "complexity" bottleneck.

> ![AgenticOps platform demo](/images/4.3-Event3/cloudthinker-demo.png)
> *Live demo of CloudThinker's AgenticOps platform*

#### Session 2 — Voice AI for Vietnamese (Hieu Nghi, Kiet, Trung)

This session tackled a very "Vietnamese" problem: Vietnamese is a **low-resource language**, so direct Speech-to-Speech models perform poorly. The most effective architecture today is the chain **Speech-to-Text → LLM → Text-to-Speech** — it sounds like a detour, but it has a major advantage: you can place **content Guardrails at the text layer** before converting back to speech.

> ![Voice Agents Are Streaming Systems slide](/images/4.3-Event3/voice-streaming-systems.jpg)
> *The "Voice Agents Are Streaming Systems" slide — the real-time speech processing pipeline*

The enterprise requirements from the banking sector (VIB, VPBank) sound small but turn out to be really hard: handling **interruptions** when the customer cuts in, **gender detection** for addressing customers correctly in Vietnamese, and handling **pauses** naturally. For the regional accent barrier, the solution was surprisingly pragmatic: adding just 10–20% regional voice data to the training set yields a clear improvement.

> ![Knowing when to talk slide](/images/4.3-Event3/voice-turn-taking.jpg)
> *The "Knowing when to talk" slide — deciding when the bot is allowed to speak seems minor, yet it determines how natural the conversation feels*

The live demo was a Voice Agent answering questions about Apple products, running on **Amazon Bedrock + Knowledge Bases** with low latency and fairly smooth interruption handling. What stayed with me: Vietnamese Voice AI is mature enough for sensitive scenarios like debt reminders or sales — but the speakers stressed that **Human-in-the-loop remains mandatory** for negative customer reactions or complex emotional nuance.

> ![Voice Agent demo](/images/4.3-Event3/voice-demo.jpg)
> *Live Voice Agent demo — the whole room got to "talk" to the bot right on stage*

#### Session 3 — DevOps AI Agent: from reactive to proactive (Bao & Nguyen)

If Voice AI is the "voice" facing the customer, the DevOps Agent is the "action" facing system health. The two speakers from Cloud Kinetics presented the 4-step workflow of an operations Agent:

1. **Categorize:** extract logs/traces as soon as a trigger fires.
2. **Investigate:** perform root cause analysis (RCA) based on the system topology.
3. **Mitigation Plan:** propose remediation — but **a human approves before execution** (human-in-the-loop).
4. **Improve:** recommend improvements based on incident history.

> ![What makes DevOps Agent different slide](/images/4.3-Event3/devops-agent-slide.jpg)
> *The two Cloud Kinetics speakers with the "What makes DevOps Agent different" slide*

A concept new to me was **Agent Space** — a "logical container" defining which resources an agent may touch, based on tags or environments (on-prem/hybrid). The most impressive demo: while the system was under a DDoS attack, the Agent analyzed the root cause on its own and proposed remediation (stop the task, revoke the definition) within a very short time.

The line I remember most from this session, though, was about **Observability**: logs, metrics, and traces are the "fuel" of an AI Agent — without good observability data, even the smartest Agent is useless. A case study of a large university cutting incident resolution time from 2 hours to 28 minutes (a 77% MTTR reduction) proved the point: the Agent is an "amplifier" of an engineer's skills, not a replacement.

> ![Best Fit For slide](/images/4.3-Event3/devops-best-fit.jpg)
> *The "Best Fit For..." slide — the DevOps Agent shines most on large-scale systems with good Observability*

#### Session 4 — Amazon Q in Human Resources (Truong & Minh Anh)

An application angle I had never considered: using **Amazon Q Business** to automate recruitment. HR's problem with public AI tools is the risk of leaking candidate data; Amazon Q solves this with a secure internal AI environment that learns from the company's official data.

> ![HR teams today face many critical challenges slide](/images/4.3-Event3/hr-challenges.jpg)
> *The two Noventis speakers opening with the everyday "pain points" of HR teams*

The **zero-shot reasoning** demo genuinely impressed me: the AI automatically filtered out a chemical engineer applying for a Cloud position — not because keywords didn't match, but because it truly "understood" the mismatch between the JD and the CV. Key features: multi-source connectivity (Gmail, Drive, SharePoint, Jira), candidate scoring on a Strong/Good/Low scale, and building a low-code recruitment tracking pipeline. The highlighted advantage: no lock-in to a closed ecosystem like Microsoft's or Google's.

> ![Amazon Quick Suite introduction](/images/4.3-Event3/amazon-quick-suite.jpg)
> *Introducing the Amazon Quick Suite toolset applied to the recruitment workflow*

Two practical benefits: reducing **burnout** for teams shouldering the workload of vacant positions, and shortening **time-to-hire** by removing subjective bias from the first screening round. Sitting through this session, I also drew a lesson for myself: once AI joins the screening round, a candidate's CV — including mine someday — must be written clearly enough to be "compatible" with the algorithm.

#### Session 5 — Private, secure connectivity for Enterprise AI & MCP (Toan & Nghi)

The final session was also the most technically "heavy" one, and the closest to what I'm currently studying. The problem: **MCP (Model Context Protocol)** lets Amazon Q connect flexibly to third parties (Zalo, WhatsApp, Gmail, Jira, GitHub), but for the banking and finance sector (BFSI), letting that data traverse the public internet is unacceptable.

> ![MCP Server Connector slide](/images/4.3-Event3/mcp-connector.jpg)
> *The MCP Server Connector slide for Amazon Q — the starting point of the secure connectivity problem*

The architecture presented uses **VPC Connection, Interface Endpoints (PrivateLink)**, and notably **Route 53 Resolver** so the entire data flow stays within the private network — completely eliminating man-in-the-middle (MITM) and internet-borne DoS risks. This is exactly the "expose nothing to the internet unless you must" philosophy I had encountered in my VPC Endpoint lab and the CloudFront session at the May event — now lifted up to the AI application layer.

> ![Solution VPC Connection for Quick slide](/images/4.3-Event3/mcp-architecture.png)
> *The "Solution: VPC Connection for Quick" slide — the private connectivity architecture for Amazon Q*

What I appreciated most was that the speakers didn't dodge the **cost** discussion: Route 53 Resolver alone runs about $180/month, and total infrastructure costs (ALB, Secrets Manager...) range from $250–350/month excluding data transfer. Enterprises must run their own **Cost-Benefit Analysis** between this investment and the risk of leaking strategic data. The session's conclusion: in the Enterprise environment, **Zero Trust is no longer optional** — security is the "passport" that allows AI to touch sensitive data.

### 7. What I Learned

* **Multi-agent is a governance decision, not just a performance one:** choosing multiple specialized agents over one Super Agent is mainly about RBAC and context windows — an "enterprise operations" perspective I had never considered when reading about AI Agents.
* **The detour architecture is sometimes the right architecture:** the STT → LLM → TTS chain is slower than Speech-to-Speech but allows Guardrails at the text layer — a lesson in trading latency for control.
* **Observability is the prerequisite for operational AI:** before dreaming of a DevOps Agent, the system needs proper logs/metrics/traces. "Garbage in, garbage out" applies to infrastructure AI too.
* **Human-in-the-loop appeared in every session:** from approving mitigation plans and handling negative customer reactions to the interview round after AI screening — humans remain the final decision layer in critical business operations.
* **Security has a concrete price tag:** this was the first time I saw a technical session state $250–350/month for a private architecture outright — and teach CBA thinking instead of just chanting "we must be secure."
* **Solid fundamentals beat fast certificates:** Steve's story of failing the Azure exam 3–4 times is a reminder that gaps in foundational knowledge can't be papered over with exam cramming.

### 8. Notable Technologies

* **Infrastructure & operations:** Amazon ECS, VPC, Lambda, ALB, AWS Secrets Manager.
* **Networking & security:** VPC PrivateLink, Interface Endpoints, Route 53 Resolver, Zero Trust architecture.
* **AI/ML & GenAI:** Amazon Bedrock (Foundation Models: Nova, Claude), Amazon Q Business/Developer, Knowledge Bases, Amazon DevOps Guru.
* **Protocols & concepts:** MCP (Model Context Protocol), Agent Space, RBAC for AI Agents, Observability (logs/metrics/traces), MTTD/MTTR, Human-in-the-loop.

### 9. What I Can Apply to Personal Projects

* **Extend my internship VPC lab:** try building a private connection via Interface Endpoints to an AI service (e.g., calling Bedrock through PrivateLink) instead of going over the internet — applying the Session 5 architecture at student scale.
* **Start with Observability:** before thinking about agents, I'll build the habit of enabling full CloudWatch Logs/Metrics on every lab — so I have the "fuel" ready when I want to experiment with operational AI later.
* **Design a small 2–3 agent system:** each agent with one narrow task and minimal permissions, instead of one giant prompt doing everything — practicing the Multi-agent + RBAC thinking I learned.
* **Always insert a human approval layer:** for any agent demo capable of "acting" (deleting resources, sending messages), add an approval step before execution — a cheap habit that matches Enterprise standards.
* **Estimate costs before building an architecture:** draw up a monthly cost estimate for each lab (the way Session 5 analyzed Route 53 Resolver) to develop FinOps thinking early.
* **Write an "AI-friendly" CV:** present skills and projects clearly, aligned with the JD — because the first screening round I face someday will very likely be an algorithm.

### 10. Reflections After the Event

Arriving at my third event of the internship, I clearly noticed a change in how I listen: back in May I was mostly in awe of the demos, whereas this time I started asking questions like a systems person — what permissions does this agent have, where does the data flow, how much does it cost. That is perhaps the greatest benefit of attending events regularly while practicing hands-on during the internship.

> ![Community attending the event](/images/4.3-Event3/community-photo.jpg)
> *The atmosphere in the hall — the community making heart signs during the networking time*

The two moments that stayed with me most: Steve's story of failing the Azure exam multiple times — hearing it made me feel much lighter about all the times I've botched my labs; and the closing advice of the event: *don't spend too much time thinking, start executing — build small PoCs, learn from real mistakes, because in the AI era, execution speed is the greatest competitive advantage*. Along with that came the line repeated as the program's central message: **"AI won't replace humans, but people who use AI well will replace those who don't."**

> ![Closing group photo](/images/4.3-Event3/group-photo.jpg)
> *A group photo with the speakers and the community closing out the program*

### 11. Conclusion

FCAJ Community Day June 2026 showed me that the 2026 enterprise AI landscape stands on three pillars: **specialized Multi-agents** for controlling permissions and context, **AI as a skill amplifier** rather than a replacement in critical systems, and **Zero Trust security** as a prerequisite despite its cost. Three things I will do right away: (1) add full Observability to my current labs, (2) experiment with a private connection to Bedrock via Interface Endpoints as an extension of my VPC workshop, and (3) build a small multi-agent PoC with a human approval layer. In the true spirit of the event — think less, start building.
