---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Bài thu hoạch sự kiện "FCAJ Community Day 2026"

### 1. Giới thiệu sự kiện

Ngày 23/05/2026, tôi có cơ hội tham dự **FCAJ Community Day 2026** — sự kiện thường niên do cộng đồng **First Cloud AI Journey (FCAJ)** phối hợp cùng **AWS Study Group** tổ chức. Trước khi đi, tôi hình dung đây sẽ là một buổi hội thảo kỹ thuật thuần túy, nhưng thực tế sự kiện mang màu sắc của một diễn đàn chiến lược: các diễn giả không chỉ trình bày công nghệ mà còn đặt câu hỏi lớn hơn — người kỹ sư sẽ đứng ở đâu khi AI đang làm thay đổi tận gốc chi phí tạo ra phần mềm?

Điều khiến tôi ấn tượng ngay từ đầu là thông điệp xuyên suốt: khoảng cách giữa một bản demo hào nhoáng và một hệ thống vận hành được trong doanh nghiệp là rất xa, và giá trị của kỹ sư năm 2026 nằm ở việc lấp được khoảng cách đó.

> ![MC mở màn chương trình](/images/4.1-Event1/opening.png)
> *MC mở màn, giới thiệu chương trình và các diễn giả*

### 2. Mục tiêu tham gia

Là sinh viên thực tập đang học về AWS, tôi đặt ra cho mình mấy mục tiêu cụ thể trước khi đến sự kiện:

* Cập nhật bức tranh thị trường việc làm IT dưới tác động của AI, để tự định vị lộ trình học tập của bản thân.
* Nghe góc nhìn thực chiến về các dịch vụ AWS tôi đang học (CloudFront, VPC, Bedrock...) từ những người vận hành chúng hằng ngày.
* Tìm hiểu cách doanh nghiệp thật (ngân hàng, fintech) đưa AI vào production — điều mà tài liệu tự học ít khi đề cập.
* Kết nối với cộng đồng, quan sát cách các anh chị đi trước tư duy và đặt câu hỏi.

### 3. Thời gian

* **Ngày diễn ra:** Thứ Bảy, 23/05/2026, chương trình chính diễn ra trong buổi sáng (khoảng 08:30 – 12:30).

### 4. Địa điểm

* Sự kiện được tổ chức offline tại *Tòa nhà Bitexco Financial Tower (Tháp Tài chính Bitexco) tọa lạc tại Số 2 đường Hải Triều, Phường Sài Gòn, Quận 1, Thành phố Hồ Chí Minh*.

### 5. Nội dung theo timeline

Chương trình được sắp xếp theo một mạch logic mà tôi rất thích: mở đầu bằng **tư duy chiến lược**, sau đó đi xuống **hạ tầng nền tảng**, và kết thúc ở **ứng dụng AI Agent phức tạp** — đúng như cách một hệ thống thật được xây dựng.

| Phiên | Diễn giả | Chủ đề |
| ----- | -------- | ------ |
| Khai mạc | Nguyễn Gia Hưng (Solutions Architect @ AWS Việt Nam, Founder FCAJ) | Tương lai việc làm: Nghịch lý Jevons và kỷ nguyên Platform Engineering |
| 2 | Tịnh Trương (Platform Engineer @ GoTymeX) | Context is Everything — tối ưu AI bằng hệ thống ngữ cảnh và memory |
| 3 | Hải Anh (Pacific Việt Nam) | Amazon Q & QuickSight — từ dữ liệu thô đến AI Agent biết hành động |
| 4 | Nguyễn Huấn Thịnh (DevOps Engineer) | CloudFront — bảo mật tại biên và cơ chế Flat-rate Pricing |
| 5 | Uyển & Thảo (Team UTMorpho) | Kiến trúc 3-Agent biến bản vẽ tay thành UI trong 36 giờ hackathon |
| 6 | Đức Đào (Solutions Architect @ Cloud Kinetics) | Giải mã tính bất định (Non-determinism) của LLM |
| 7 | Vy Lâm (Senior Business Systems Analyst @ VPBank) | Multi-Agent System trong thẩm định tín dụng chuẩn Enterprise |

### 6. Nội dung từng phiên

#### Phiên khai mạc — Nghịch lý Jevons và tương lai nghề kỹ sư (Nguyễn Gia Hưng)

Anh Hưng mở đầu bằng một ví dụ khiến tôi nhớ mãi: bóng đèn LED tiết kiệm 90% điện so với đèn sợi đốt, nhưng tổng lượng điện chiếu sáng của nhân loại không hề giảm — vì ánh sáng quá rẻ nên người ta thắp sáng mọi nơi, từ cầu thang đến sân vườn. Đó là **Nghịch lý Jevons**: hiệu suất tăng khiến tổng tiêu thụ tăng vọt. Áp vào ngành phần mềm: AI làm chi phí viết code rẻ đi, nên số lượng phần mềm sẽ bùng nổ chứ không giảm — kéo theo một "bãi rác" mã nguồn khổng lồ cần được vận hành, bảo trì và mở rộng.

Từ đó anh chỉ ra sự dịch chuyển của thị trường: nhu cầu chuyển từ **"kỹ sư demo" sang "kỹ sư sản phẩm"** — người có khả năng đưa hệ thống ra production và scale nó, tức là **Platform Engineering**. Ba thứ anh khuyên chuẩn bị: nền tảng hard skills vững (bằng cấp vẫn là bộ lọc quan trọng của HR), kiến thức nghiệp vụ của một ngành cụ thể (ngân hàng, logistics, y tế...), và một sản phẩm thật chạy được thay vì chỉ code nằm trên GitHub.

#### Phiên 2 — Context is Everything (Tịnh Trương)

Đây là phiên tôi thấy "đúng bệnh" của mình nhất. Anh Tịnh gọi tên thói quen **"Internet Puller"** — ném mọi thứ (PDF, screenshot, plugin, note) vào cửa sổ chat AI mà không chọn lọc, khiến tỷ lệ tín hiệu/nhiễu giảm mạnh, AI mất tập trung và tốn token vô ích. Rào cản lớn nhất của hiệu suất AI không nằm ở model, mà ở cách ta cung cấp thông tin.

Khung context chuẩn anh đề xuất gồm 4 phần: **Goal** (mục tiêu cuối), **Situation** (bối cảnh hệ thống), **Constraints** (ràng buộc kỹ thuật/ngân sách) và **Evidence/Success Criteria** (bằng chứng và tiêu chí đánh giá). Anh cũng giới thiệu chiến lược **"Second AI Brain"** — bản chất là một pipeline RAG: Store (lưu tri thức có cấu trúc, ví dụ trên S3/Obsidian) → Retrieve (truy xuất qua Vector DB) → Generate (sinh phản hồi qua Amazon Bedrock) → Learn (cập nhật ngược lại kho tri thức).

> ![Anh Tịnh Trương mở đầu phiên Context Is Everything](/images/4.1-Event1/context-engineering-session.png)
> *Anh Tịnh Trương mở đầu phiên "Context Is Everything — Making AI Actually Work for You"*

> ![Slide khung context chuẩn 4 thành phần](/images/4.1-Event1/context-framework-slide.png)
> *Slide "What Does Context Mean?" — khung context 4 thành phần: Goal, Situation, Constraints, Evidence*

#### Phiên 3 — Amazon Q & QuickSight (Hải Anh)

Chị Hải Anh demo khả năng **BI tự phục vụ**: đưa một file dữ liệu thô vào và đặt câu hỏi bằng ngôn ngữ tự nhiên để nhận về dashboard phân tích — không cần kỹ năng BI chuyên sâu. Nhưng điểm khiến tôi chú ý hơn là khái niệm **Agentic Workflows**: AI không dừng ở việc vẽ biểu đồ mà đóng vai một cộng sự biết hành động — tóm tắt cuộc họp, soạn email, đề xuất next steps.

Về mặt kiến trúc, chìa khóa là **MCP (Model Context Protocol)** — giao thức chuẩn hóa việc gắn công cụ (tools) vào Agent. Thay vì hardcode kết nối tới Jira/Gmail/Salesforce, MCP tách tầng model khỏi tầng hành động, giúp Agent tự nhận diện và tương tác với công cụ. Tôi ghi lại một câu diễn đạt rất gọn: MCP biến chatbot từ "kẻ biết nói" thành "người biết làm".

> ![Mở đầu phiên Amazon Q & QuickSight](/images/4.1-Event1/amazon-q-session.png)
> *Mở đầu phiên "Friendly AI Assistant w/ Amazon Quick"*

> ![Demo trực tiếp QuickSight](/images/4.1-Event1/quicksight-demo.png)
> *Demo trực tiếp: từ dữ liệu thô đến dashboard phân tích bằng ngôn ngữ tự nhiên*

#### Phiên 4 — CloudFront: bảo mật tại biên và Flat-rate Pricing (Nguyễn Huấn Thịnh)

Phiên này gần với những gì tôi đang thực hành nhất (tôi vừa học VPC ở tuần 2 thực tập). Vài con số về quy mô: gần 700 PoPs tại hơn 50 quốc gia trên mạng backbone 400 GbE redundant. Hai nội dung tôi tâm đắc:

* **Flat-rate Pricing** (ra mắt 11/2025): chuyển từ pay-as-you-go bất định sang các gói cố định (Free/Pro/Business/Premium), giúp doanh nghiệp triệt tiêu rủi ro **bill spike** khi bị DDoS hoặc traffic tăng đột biến — chi phí CDN trở nên dự đoán được.
* **VPC Origin**: thay vì để origin (ALB/EC2) lộ trên internet, CloudFront tạo "đường hầm" qua ENI đi thẳng vào private subnet — ẩn hoàn toàn hạ tầng gốc, giảm tối đa attack surface, thậm chí bỏ được Public ALB. Nghe đến đây tôi liên hệ ngay với bài lab VPC endpoint mình vừa làm: cùng một triết lý "không để thứ gì lộ ra internet nếu không bắt buộc".

Ngoài ra còn có chống tấn công tại Edge gần như tức thì (so với WAF tiêu chuẩn mất vài phút để kích hoạt), mTLS cho API tài chính, HTTP/3 (QUIC) và nén dữ liệu giúp giảm đáng kể dung lượng truyền và độ trễ.

> ![Phiên CloudFront - phòng thủ phân tán](/images/4.1-Event1/cloudfront-session.png)
> *Anh Nguyễn Huấn Thịnh với slide "Distributed attacks with a distributed defense"*

> ![Slide Origin cloaking](/images/4.1-Event1/cloudfront-origin-cloaking.png)
> *Slide "Origin cloaking for custom origins" — ẩn hạ tầng gốc khỏi internet công cộng*

#### Phiên 5 — UTMorpho: 36 giờ hackathon và kiến trúc 3-Agent (Uyển & Thảo)

Hai bạn kể lại hành trình xây dựng công cụ AI biến bản vẽ tay (sketch) thành code UI hoàn chỉnh tại LotusHacks. Hệ thống chia làm 3 Agent chuyên biệt: **Vision Analyst** (phân tích cấu trúc hình vẽ), **Style Engine** (thiết lập CSS) và **Design Architect** (tổng hợp code). Trải nghiệm "Live Preview & Edit" được xây trên WebSocket + API Gateway + Lambda, kết hợp **HTML Stream** để người dùng thấy UI hiện dần ngay khi code đang được sinh ra.

Phần bài học "xương máu" rất thật: burnout là rào cản lớn nhất trong cuộc thi cường độ cao; nhóm đã quyết định chiến lược ưu tiên trải nghiệm chỉnh sửa thay vì nhồi tính năng; và hai kỹ năng sống còn là xử lý giới hạn token cùng kiểm soát **AI over-generation** (AI sinh code lan man, dư thừa).

> ![Team UTMorpho kể lại hành trình](/images/4.1-Event1/utmorpho-session.png)
> *Team UTMorpho với slide "From Zero to Idea — The Brainstorming Journey"*

> ![Demo UTMorpho](/images/4.1-Event1/utmorpho-demo.png)
> *Demo trực tiếp công cụ biến bản vẽ tay thành UI*

#### Phiên 6 — Tính bất định của LLM (Đức Đào)

Phiên "hại não" nhất nhưng cũng mở mang nhất với tôi. Câu hỏi: tại sao đặt **Temperature = 0** mà gọi API nhiều lần vẫn không ra kết quả giống nhau 100%? Hai nguyên nhân:

* **Kỹ thuật:** phép cộng số thực dấu phẩy động trên GPU **không có tính kết hợp** — (A+B)+C khác A+(B+C). Khi tính toán song song, thứ tự cộng thay đổi tạo sai số làm tròn cực nhỏ, nhưng tích lũy qua hàng tỷ tham số thì đủ làm lệch token đầu ra.
* **Thương mại:** nhà cung cấp API tối ưu chi phí bằng **batching** — gộp request của nhiều khách hàng vào một lượt chạy GPU, làm thay đổi logits và phá tính tất định.

Giải pháp giảm thiểu: chạy ensemble lấy kết quả xuất hiện nhiều nhất, self-host hoặc dùng Provisioned Throughput để kiểm soát tài nguyên tính toán, và ép định dạng đầu ra bằng JSON Mode để hệ thống phía sau luôn nhận được cấu trúc hợp lệ. Anh Đức còn giới thiệu hai chỉ số đo lường: TARr@N (độ trùng chuỗi thô) và TARa@N (độ trùng câu trả lời sau phân tách).

> ![Slide giải thích nguyên nhân non-determinism](/images/4.1-Event1/non-determinism-session.png)
> *Slide "The Reality — Why Does This Happen?": phép toán floating-point trên GPU không có tính kết hợp*

#### Phiên 7 — Multi-Agent System trong thẩm định tín dụng (Vy Lâm)

Chị Vy trình bày mô hình **"Hội đồng tín dụng ảo"** tại ngân hàng: bài toán thẩm định startup có dữ liệu đa chiều (tài chính, thị trường TAM/SAM/SOM, đội ngũ) sẽ làm một Agent đơn lẻ quá tải context và loãng chuyên môn. Giải pháp là phân rã thành các Agent chuyên biệt — **Financial Analyst, Market Analyst, Team Evaluator, Risk Assessor** — hoạt động song song và kiểm tra chéo nhau như một cơ chế checks & balances.

Con số hiệu quả rất thuyết phục: thời gian xử lý hồ sơ giảm ~95% (từ 2 tuần còn 2 giờ), tiết kiệm 2–3 tỷ VNĐ/năm cho mỗi 100 hồ sơ/tháng. Nhưng điều tôi nhớ nhất là triết lý **Human-in-the-loop**: AI hỗ trợ, con người vẫn là lớp ký duyệt cuối và chịu trách nhiệm pháp lý — một lỗi "ảo giác" biến 10 tỷ thành 100 tỷ trong ngân hàng là chuyện không thể đùa. Kèm theo đó là Guardrails chống prompt injection, bảo vệ PII, API rotation và audit trail — những thứ bắt buộc phải có trước khi AI được bước vào môi trường Enterprise. Chị cũng nhắc câu "Ship in, Ship out": dữ liệu nghiệp vụ đầu vào không chuẩn thì AI chỉ sản xuất ra rác nhanh hơn.

> ![Kiến trúc Virtual Credit Committee](/images/4.1-Event1/multi-agent-architecture.png)
> *Chị Vy Lâm trình bày slide "Virtual credit committee architecture" — kiến trúc hội đồng tín dụng ảo*

### 7. Những kiến thức tôi học được

* **Context quan trọng hơn Prompt:** chất lượng đầu vào quyết định chất lượng đầu ra; kỹ năng thiết kế ngữ cảnh (Goal – Situation – Constraints – Evidence) là kỹ năng lõi chứ không phải mẹo vặt.
* **Nghịch lý Jevons áp vào nghề nghiệp:** AI không làm giảm nhu cầu kỹ sư mà làm bùng nổ khối lượng phần mềm cần vận hành → cơ hội nằm ở Platform Engineering và năng lực đưa sản phẩm ra production.
* **LLM là hệ thống xác suất:** ngay cả Temperature = 0 cũng không đảm bảo tất định (do floating-point non-associativity và batching) → mọi hệ thống tích hợp LLM đều phải có kiểm thử đa tầng và cơ chế xử lý sai lệch.
* **Multi-Agent không phải trào lưu mà là kiến trúc:** chia nhỏ chuyên môn để tránh loãng context, kèm cơ chế kiểm tra chéo — nhưng luôn cần con người ở lớp quyết định cuối.
* **Security & Compliance là điều kiện tiên quyết:** không có Guardrails, audit trail và bảo vệ PII thì dự án AI Enterprise không bao giờ vượt qua được giai đoạn POC.

### 8. Các công nghệ nổi bật

* **Hạ tầng & vận hành:** EC2, Lambda, API Gateway, Route 53, ECR, Terraform, CDK, CloudWatch, X-Ray.
* **Truyền tải & bảo mật:** CloudFront (Flat-rate, VPC Origin qua ENI, HTTP/3), WAF, AWS Shield, PrivateLink, Cognito (MFA/RBAC), mTLS, ACM, Secrets Manager, IAM.
* **AI/ML & dữ liệu:** Amazon Bedrock (AgentCore, Guardrails), Amazon Q, QuickSight, Claude Sonnet, Vector DB, S3, RAG pipeline.
* **Khái niệm/giao thức mới với tôi:** Model Context Protocol (MCP), Inference Optimization & batching, Multi-Agent Orchestration, Context Engineering.

### 9. Điều có thể áp dụng vào dự án cá nhân

* **Sửa ngay thói quen "Internet Puller":** từ giờ mỗi lần nhờ AI, tôi sẽ soạn context theo khung Goal – Situation – Constraints – Success Criteria thay vì dán cả tràng tài liệu.
* **Áp dụng vào workshop thực tập:** bài lab VPC tôi đang làm có thể mở rộng theo hướng VPC Origin — đặt origin trong private subnet và để CloudFront là lối vào duy nhất, đúng triết lý giảm attack surface đã học ở tuần 2.
* **Thiết kế chịu lỗi khi gọi LLM API:** ép JSON Mode, validate schema đầu ra, retry với ensemble khi kết quả bất định — áp dụng được ngay cho các dự án cá nhân có tích hợp AI.
* **Thử nghiệm MCP:** dựng một Agent nhỏ kết nối tool qua MCP thay vì hardcode, tham khảo các mẫu kiến trúc Agent trên repository awslabs.
* **Thực hành Bedrock Guardrails và Cognito + JWT** để làm quen với tư duy "bảo mật trước, tính năng sau" ngay từ dự án sinh viên.

### 10. Cảm nhận sau sự kiện

Đây là lần đầu tôi dự một sự kiện cộng đồng công nghệ quy mô lớn, và cảm giác chung là "được đánh thức". Những chia sẻ thẳng thắn của anh Hưng về thị trường việc làm khiến tôi vừa áp lực vừa có định hướng rõ hơn: tấm bằng và nền tảng vẫn quan trọng, nhưng thứ tạo khác biệt là sản phẩm thật và kiến thức nghiệp vụ. Phần của chị Vy Lâm cho tôi thấy khoảng cách khổng lồ giữa "code chạy được" và "hệ thống được phép chạy trong ngân hàng" — nơi Security, Compliance và ROI chiếm phần lớn công sức.

Về kỹ thuật, phiên của anh Đức Đào gỡ bỏ một lầm tưởng tôi mang theo khá lâu (rằng Temperature = 0 là tất định), còn phiên CloudFront giúp tôi kết nối trực tiếp với bài lab VPC vừa làm ở tuần 2 — học ở workshop xong được nghe người vận hành thật nói về nó là một trải nghiệm rất khác. Không khí networking sau sự kiện cũng rất thoải mái; tôi nhận ra cộng đồng sẵn sàng chia sẻ hơn mình nghĩ nhiều, chỉ cần mình chủ động đặt câu hỏi.

> ![Phần giao lưu hỏi đáp cuối chương trình](/images/4.1-Event1/qa-closing.png)
> *Phần giao lưu hỏi đáp sôi nổi ở cuối chương trình*

### 11. Kết luận

FCAJ Community Day 2026 với tôi không chỉ là một buổi cập nhật công nghệ mà là một lần "chỉnh la bàn". Thông điệp tôi mang về gói gọn trong ba ý: (1) đầu tư vào Context Engineering và tư duy sản phẩm thay vì chạy theo demo, (2) học vững hạ tầng Cloud vì đó là bệ đỡ của mọi hệ thống AI, và (3) đừng trì hoãn — sức mạnh AI đang tăng theo cấp số nhân, kỷ nguyên này không đào thải kỹ sư, nó chỉ đào thải những người đứng yên. Việc tiếp theo của tôi là biến những gì đã nghe thành thứ chạy được: hoàn thành tốt workshop thực tập, thử nghiệm Bedrock Guardrails và xây một dự án nhỏ có tích hợp AI theo đúng những nguyên tắc đã học.
