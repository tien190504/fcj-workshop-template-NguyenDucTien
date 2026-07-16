---
title: "FCAJ Community Day 2026"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Event Report: "FCAJ Community Day 2026"

### 1. About the Event

On May 23rd, 2026, I had the opportunity to attend **FCAJ Community Day 2026** — the annual event organized by the **First Cloud AI Journey (FCAJ)** community in collaboration with **AWS Study Group**. Before going, I pictured a purely technical seminar, but the event turned out to feel more like a strategic forum: the speakers didn't just present technology, they raised a bigger question — where will engineers stand when AI is fundamentally reshaping the cost of building software?

What struck me right from the start was the message running through the entire program: the gap between a flashy demo and a system that actually operates in an enterprise is enormous, and the value of an engineer in 2026 lies in closing that gap.

> ![The MC opening the event](/images/4.1-Event1/opening.png)
> *The MC opening the program and introducing the speakers*

### 2. My Goals for Attending

As an intern currently studying AWS, I set a few concrete goals before heading to the event:

* Get an updated picture of the IT job market under the impact of AI, to position my own learning path.
* Hear battle-tested perspectives on the AWS services I am studying (CloudFront, VPC, Bedrock...) from people who operate them daily.
* Learn how real enterprises (banks, fintech) bring AI into production — something self-study materials rarely cover.
* Connect with the community and observe how experienced engineers think and ask questions.

### 3. Time

* **Date:** Saturday, May 23rd, 2026; the main program took place in the morning (roughly 08:30 – 12:30).

### 4. Venue

* The event was held offline at *Bitexco Financial Tower, No. 2 Hai Trieu Street, Sai Gon Ward, District 1, Ho Chi Minh City*.

### 5. Program Timeline

The program followed a logical flow that I really appreciated: it opened with **strategic thinking**, moved down to **foundational infrastructure**, and finished with **complex AI Agent applications** — exactly how a real system gets built.

| Session | Speaker | Topic |
| ------- | ------- | ----- |
| Opening | Nguyễn Gia Hưng (Solutions Architect @ AWS Vietnam, Founder of FCAJ) | The future of work: the Jevons Paradox and the era of Platform Engineering |
| 2 | Tịnh Trương (Platform Engineer @ GoTymeX) | Context is Everything — optimizing AI with context and memory systems |
| 3 | Hải Anh (Pacific Vietnam) | Amazon Q & QuickSight — from raw data to AI Agents that take action |
| 4 | Nguyễn Huấn Thịnh (DevOps Engineer) | CloudFront — security at the edge and the Flat-rate Pricing model |
| 5 | Uyển & Thảo (Team UTMorpho) | A 3-Agent architecture turning hand-drawn sketches into real UI in a 36-hour hackathon |
| 6 | Đức Đào (Solutions Architect @ Cloud Kinetics) | Decoding the non-determinism of LLMs |
| 7 | Vy Lâm (Senior Business Systems Analyst @ VPBank) | Multi-Agent Systems in enterprise-grade credit assessment |

### 6. Session Details

#### Opening Session — The Jevons Paradox and the future of engineering (Nguyễn Gia Hưng)

Mr. Hưng opened with an example I will remember for a long time: LED bulbs save 90% of the electricity compared to incandescent bulbs, yet humanity's total lighting consumption never went down — light became so cheap that people lit up everything, from staircases to gardens. That is the **Jevons Paradox**: as efficiency increases, total consumption explodes. Applied to software: AI makes writing code cheaper, so the amount of software will explode rather than shrink — dragging along a massive "landfill" of source code that needs to be operated, maintained and scaled.

From there he pointed out the market shift: demand is moving from **"demo engineers" to "product engineers"** — people who can take systems to production and scale them, i.e. **Platform Engineering**. Three things he advised preparing: solid hard-skill foundations (a degree is still an important HR filter), domain knowledge of a specific industry (banking, logistics, healthcare...), and a real, runnable product instead of code sitting idle on GitHub.

#### Session 2 — Context is Everything (Tịnh Trương)

This was the session that hit closest to home for me. Mr. Tịnh named a habit he called the **"Internet Puller"** — throwing everything (PDFs, screenshots, plugins, notes) into the AI chat window without any filtering, which crushes the signal-to-noise ratio, makes the AI lose focus and wastes tokens. The biggest barrier to AI performance is not the model, but how we feed it information.

The standard context framework he proposed has 4 parts: **Goal** (the end objective), **Situation** (system context), **Constraints** (technical/budget restrictions) and **Evidence/Success Criteria** (supporting data and evaluation criteria). He also introduced the **"Second AI Brain"** strategy — essentially a RAG pipeline: Store (structured knowledge, e.g. on S3/Obsidian) → Retrieve (via a Vector DB) → Generate (responses via Amazon Bedrock) → Learn (feed knowledge back into the store).

> ![Mr. Tịnh Trương opening the Context Is Everything session](/images/4.1-Event1/context-engineering-session.png)
> *Mr. Tịnh Trương opening the session "Context Is Everything — Making AI Actually Work for You"*

> ![The 4-part context framework slide](/images/4.1-Event1/context-framework-slide.png)
> *The "What Does Context Mean?" slide — the 4-part context framework: Goal, Situation, Constraints, Evidence*

#### Session 3 — Amazon Q & QuickSight (Hải Anh)

Ms. Hải Anh demonstrated **self-service BI**: feed in a raw data file and ask questions in natural language to get analytical dashboards back — no deep BI skills required. But what caught my attention more was the concept of **Agentic Workflows**: the AI doesn't stop at drawing charts, it acts as a colleague that takes action — summarizing meetings, drafting emails, suggesting next steps.

Architecturally, the key is **MCP (Model Context Protocol)** — a standardized protocol for attaching tools to Agents. Instead of hardcoding connections to Jira/Gmail/Salesforce, MCP separates the model layer from the action layer, letting Agents automatically discover and interact with tools. I wrote down one very concise phrase: MCP turns a chatbot from "someone who can talk" into "someone who can do".

> ![Opening of the Amazon Q & QuickSight session](/images/4.1-Event1/amazon-q-session.png)
> *Opening of the "Friendly AI Assistant w/ Amazon Quick" session*

> ![Live QuickSight demo](/images/4.1-Event1/quicksight-demo.png)
> *Live demo: from raw data to analytical dashboards using natural language*

#### Session 4 — CloudFront: security at the edge and Flat-rate Pricing (Nguyễn Huấn Thịnh)

This session was closest to what I am practicing right now (I had just studied VPC in week 2 of my internship). Some scale numbers: nearly 700 PoPs across more than 50 countries on a redundant 400 GbE backbone. Two things resonated with me:

* **Flat-rate Pricing** (launched 11/2025): moving from unpredictable pay-as-you-go to fixed plans (Free/Pro/Business/Premium), eliminating the risk of a **bill spike** during DDoS attacks or sudden traffic surges — CDN cost becomes predictable.
* **VPC Origin**: instead of exposing the origin (ALB/EC2) to the internet, CloudFront creates a "tunnel" through ENIs straight into the private subnet — completely hiding the origin infrastructure, minimizing the attack surface, even eliminating the need for a public ALB. Hearing this, I immediately connected it to the VPC endpoint lab I had just done: the same philosophy of "expose nothing to the internet unless absolutely necessary".

On top of that: near-instant attack mitigation at the Edge (versus standard WAF solutions that take minutes to activate), mTLS for financial APIs, HTTP/3 (QUIC) and data compression that significantly reduce transfer size and latency.

> ![CloudFront session - distributed defense](/images/4.1-Event1/cloudfront-session.png)
> *Mr. Nguyễn Huấn Thịnh with the slide "Distributed attacks with a distributed defense"*

> ![Origin cloaking slide](/images/4.1-Event1/cloudfront-origin-cloaking.png)
> *The "Origin cloaking for custom origins" slide — hiding origin infrastructure from the public internet*

#### Session 5 — UTMorpho: a 36-hour hackathon and a 3-Agent architecture (Uyển & Thảo)

The two speakers retold their journey of building an AI tool that turns hand-drawn sketches into complete UI code at LotusHacks. The system splits into 3 specialized Agents: **Vision Analyst** (analyzing the sketch structure), **Style Engine** (setting up CSS) and **Design Architect** (assembling the code). The "Live Preview & Edit" experience was built on WebSocket + API Gateway + Lambda, combined with **HTML Stream** so users watch the UI appear progressively while the code is still being generated.

The "hard-earned lessons" part felt very real: burnout is the biggest obstacle in a high-intensity competition; the team made the strategic call to prioritize the editing experience over cramming in features; and the two survival skills were handling token limits and controlling **AI over-generation** (the AI producing rambling, redundant code).

> ![Team UTMorpho telling their story](/images/4.1-Event1/utmorpho-session.png)
> *Team UTMorpho with the slide "From Zero to Idea — The Brainstorming Journey"*

> ![UTMorpho demo](/images/4.1-Event1/utmorpho-demo.png)
> *Live demo of the sketch-to-UI tool*

#### Session 6 — The non-determinism of LLMs (Đức Đào)

The most "brain-melting" yet most eye-opening session for me. The question: why does setting **Temperature = 0** still not produce 100% identical results across API calls? Two causes:

* **Technical:** floating-point addition on GPUs is **non-associative** — (A+B)+C differs from A+(B+C). In parallel computation, the order of additions changes, creating tiny rounding errors that, accumulated over billions of parameters, are enough to shift the output tokens.
* **Commercial:** API providers optimize cost with **batching** — grouping requests from multiple customers into one GPU run, which changes the logits and breaks determinism.

Mitigation strategies: run an ensemble and take the most common result, self-host or use Provisioned Throughput to control the compute resources, and force output structure with JSON Mode so downstream systems always receive valid structure. Mr. Đức also introduced two measurement metrics: TARr@N (raw string match rate) and TARa@N (parsed answer match rate).

> ![Slide explaining the cause of non-determinism](/images/4.1-Event1/non-determinism-session.png)
> *The slide "The Reality — Why Does This Happen?": floating-point operations on GPUs are non-associative*

#### Session 7 — Multi-Agent Systems in credit assessment (Vy Lâm)

Ms. Vy presented the **"Virtual Credit Committee"** model at a bank: assessing a startup involves multi-dimensional data (financials, market TAM/SAM/SOM, team), which would overload a single Agent's context and dilute its expertise. The solution is decomposition into specialized Agents — **Financial Analyst, Market Analyst, Team Evaluator, Risk Assessor** — running in parallel and cross-checking each other as a checks-and-balances mechanism.

The efficiency numbers were compelling: processing time reduced by ~95% (from 2 weeks down to 2 hours), saving 2–3 billion VND per year for every 100 applications/month. But what I remember most is the **Human-in-the-loop** philosophy: AI assists, humans remain the final approval layer and bear the legal responsibility — an AI "hallucination" turning 10 billion into 100 billion in a bank is no joke. Alongside that come Guardrails against prompt injection, PII protection, API rotation and audit trails — mandatory prerequisites before AI is allowed into an Enterprise environment. She also quoted "Ship in, Ship out": if the business data going in isn't clean, AI just produces garbage faster.

> ![Virtual Credit Committee architecture](/images/4.1-Event1/multi-agent-architecture.png)
> *Ms. Vy Lâm presenting the "Virtual credit committee architecture" slide*

### 7. What I Learned

* **Context matters more than Prompt:** input quality determines output quality; context design (Goal – Situation – Constraints – Evidence) is a core skill, not a trick.
* **The Jevons Paradox applied to careers:** AI doesn't reduce the demand for engineers — it explodes the volume of software that needs operating → the opportunity lies in Platform Engineering and the ability to ship products to production.
* **LLMs are probabilistic systems:** even Temperature = 0 doesn't guarantee determinism (due to floating-point non-associativity and batching) → every LLM-integrated system needs multi-layer testing and deviation handling.
* **Multi-Agent is not a trend but an architecture:** decompose expertise to avoid context dilution, add cross-checking mechanisms — but always keep humans at the final decision layer.
* **Security & Compliance are prerequisites:** without Guardrails, audit trails and PII protection, no Enterprise AI project ever makes it past the POC stage.

### 8. Notable Technologies

* **Infrastructure & operations:** EC2, Lambda, API Gateway, Route 53, ECR, Terraform, CDK, CloudWatch, X-Ray.
* **Delivery & security:** CloudFront (Flat-rate, VPC Origin via ENI, HTTP/3), WAF, AWS Shield, PrivateLink, Cognito (MFA/RBAC), mTLS, ACM, Secrets Manager, IAM.
* **AI/ML & data:** Amazon Bedrock (AgentCore, Guardrails), Amazon Q, QuickSight, Claude Sonnet, Vector DB, S3, RAG pipeline.
* **Concepts/protocols new to me:** Model Context Protocol (MCP), Inference Optimization & batching, Multi-Agent Orchestration, Context Engineering.

### 9. What I Can Apply to My Own Projects

* **Fix the "Internet Puller" habit immediately:** from now on, every time I ask an AI for help, I will compose context following the Goal – Situation – Constraints – Success Criteria framework instead of pasting walls of documents.
* **Apply to my internship workshop:** the VPC lab I am working on can be extended toward VPC Origin — placing the origin in a private subnet with CloudFront as the only entrance, following the attack-surface-reduction philosophy I learned in week 2.
* **Fault-tolerant design when calling LLM APIs:** force JSON Mode, validate output schemas, retry with an ensemble when results are non-deterministic — immediately applicable to my personal projects that integrate AI.
* **Experiment with MCP:** build a small Agent that connects to tools via MCP instead of hardcoding, referencing the sample Agent architectures in the awslabs repositories.
* **Practice Bedrock Guardrails and Cognito + JWT** to internalize the "security first, features second" mindset starting from student projects.

### 10. My Impressions

This was the first large-scale tech community event I've attended, and the overall feeling was of "being woken up". Mr. Hưng's frank sharing about the job market made me feel both pressured and better oriented: the degree and fundamentals still matter, but what makes the difference is a real product and domain knowledge. Ms. Vy Lâm's session showed me the enormous gap between "code that runs" and "a system allowed to run inside a bank" — where Security, Compliance and ROI consume most of the effort.

On the technical side, Mr. Đức Đào's session dispelled a misconception I had carried for quite a while (that Temperature = 0 means deterministic), while the CloudFront session connected directly to the VPC lab I had just completed in week 2 — finishing a workshop and then hearing a real operator talk about the same things is a very different experience. The networking atmosphere after the event was also very relaxed; I realized the community is far more willing to share than I thought — you just have to ask.

> ![The Q&A segment at the end of the program](/images/4.1-Event1/qa-closing.png)
> *The lively Q&A segment at the end of the program*

### 11. Conclusion

For me, FCAJ Community Day 2026 was not just a technology update but a "compass recalibration". The message I took home boils down to three points: (1) invest in Context Engineering and a product mindset instead of chasing demos, (2) master Cloud infrastructure because it is the bedrock of every AI system, and (3) don't procrastinate — AI capability is growing exponentially, and this era doesn't eliminate engineers, it only eliminates those who stand still. My next step is to turn what I heard into things that run: complete my internship workshop well, experiment with Bedrock Guardrails, and build a small AI-integrated project following the principles I learned.
