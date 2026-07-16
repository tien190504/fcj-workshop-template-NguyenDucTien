---
title: "First Cloud Journey (FCJ) Community Day"
date: 2026-06-06
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Bài thu hoạch sự kiện "First Cloud Journey Community Day"

### 1. Giới thiệu sự kiện

Sau lần đầu "vỡ òa" với FCAJ Community Day hồi tháng 5, tôi tiếp tục đăng ký tham dự **First Cloud Journey (FCJ) Community Day** — ngày hội cộng đồng quy tụ diễn giả từ nhiều đơn vị khác nhau: kỹ sư đang làm việc tại **Endava**, **Central Retail Group**, sinh viên nghiên cứu từ **HUTECH**, **Swinburne** và các thành viên nòng cốt của cộng đồng FCJ. Nếu sự kiện trước nghiêng về AI Agent và chiến lược nghề nghiệp, thì lần này chương trình trải rộng hơn hẳn: từ bảo mật hạ tầng, container, game multiplayer trên cloud, GenAI cho đến kỹ năng mềm và lộ trình sự nghiệp.

Điều tôi thích ở sự kiện này là các phiên không dừng ở lý thuyết: gần như phiên kỹ thuật nào cũng có demo chạy thật — dashboard phát hiện tấn công mạng theo thời gian thực, một game oẳn tù tì multiplayer chạy hoàn toàn trên hạ tầng serverless, và quy trình đóng gói ứng dụng bằng Docker sẵn sàng cho production. Với một sinh viên thực tập đang học AWS như tôi, được nhìn thấy các dịch vụ mình học ghép lại thành hệ thống hoàn chỉnh là trải nghiệm rất đáng giá.

### 2. Mục tiêu tham gia

Trước khi đến sự kiện, tôi đặt ra cho mình vài mục tiêu cụ thể:

* Hiểu cách kết hợp **AWS WAF với Machine Learning** để phát hiện tấn công — chủ đề liên quan trực tiếp đến mảng bảo mật tôi đang tìm hiểu trong kỳ thực tập.
* Củng cố kiến thức nền về **Docker và containerization**, thứ mà tôi mới chỉ dùng ở mức chạy lệnh chứ chưa thật sự hiểu cơ chế bên dưới.
* Xem một kiến trúc **serverless thực tế end-to-end** (API Gateway, Lambda, DynamoDB) vận hành ra sao qua case study game multiplayer.
* Tìm hiểu **GraphRAG** — khái niệm tôi mới nghe tên chứ chưa hình dung được nó khác RAG thường ở đâu.
* Lắng nghe kinh nghiệm định hướng nghề nghiệp từ người đi trước, đặc biệt là con đường từ vị trí entry-level đi lên.

### 3. Thời gian

* **Ngày diễn ra:** Thứ Bảy, 06/06/2026, chương trình chính kéo dài trong buổi sáng và đầu giờ chiều.

### 4. Địa điểm

* Sự kiện được tổ chức offline tại Thành phố Hồ Chí Minh, do cộng đồng **First Cloud Journey** phối hợp cùng các đơn vị đồng hành tổ chức.

### 5. Nội dung theo timeline

Chương trình được sắp xếp theo mạch khá hợp lý: bắt đầu từ **bảo vệ hạ tầng**, sang **đóng gói ứng dụng**, đến **con người và kỹ năng**, rồi khép lại bằng **ứng dụng AI tiên tiến**:

| Phiên | Diễn giả | Đơn vị | Chủ đề |
| ----- | -------- | ------ | ------ |
| 1 | Lê Hoàng Gia Đại | ĐH HUTECH / AWS G3 | AWS WAF kết hợp Machine Learning NIDS |
| 2 | Bảo Huỳnh | Endava / ITea Lab | Docker — công nghệ Containerization |
| 3 | Trần Trung Vinh | Central Retail Group | Career Path: từ IT Helpdesk đến Senior Sysadmin |
| 4 | Nguyễn Quốc Bảo | Cộng đồng FCJ | Multiplayer trên Cloud với WebSockets |
| 5 | Trương Huy Phước | Cộng đồng FCJ | Nghệ thuật làm việc nhóm hiệu quả |
| 6 | Việt Phát | ĐH Swinburne | GraphRAG với Amazon Bedrock & Neptune |

### 6. Nội dung từng phiên

#### Phiên 1 — AWS WAF & Machine Learning NIDS (Lê Hoàng Gia Đại)

Phiên mở màn đặt ra một vấn đề rất thời sự: WAF truyền thống hoạt động theo **luật định sẵn (rule-based)** nên gần như bất lực trước các cuộc tấn công **Zero-day** — thứ chưa từng có "luật" nào mô tả. Giải pháp anh Đại trình bày là bổ sung một hệ thống **NIDS (Network Intrusion Detection System)** phát hiện xâm nhập dựa trên **hành vi (behavior-based)** bằng Machine Learning.

Về dữ liệu, nhóm chọn bộ **CSE-CIC-IDS2018** thay vì NSL-KDD cũ kỹ, vì nó chứa các kịch bản tấn công hiện đại (Web Attack, Botnet, CSRF...). Phần tiền xử lý làm tôi nhận ra làm ML thực tế "trần trụi" hơn sách vở nhiều: gộp hàng loạt file CSV, xóa nhãn lỗi, xử lý giá trị âm/NaN/vô hạn, và đặc biệt là **cân bằng lớp dữ liệu** để mô hình không bỏ sót các loại tấn công hiếm gặp.

Nhóm thử nghiệm nhiều thuật toán (Random Forest, XGBoost, AE + XGBoost) nhưng **LightGBM** thắng cuộc nhờ cân bằng tốt giữa tốc độ và độ chính xác:

| Chỉ số | Kết quả |
| ------ | ------- |
| Accuracy | 0.9586 |
| Precision (macro) | 0.9653 |
| Recall (macro) | 0.9586 |
| F1 (macro) | 0.9571 |
| AUC-ROC | 0.9982 |

Kiến trúc triển khai hoàn toàn serverless: log của AWS WAF được **Kinesis Data Firehose** đẩy về **S3**, **Lambda** kích hoạt mô hình ML để phân tích, cảnh báo tập trung về **Security Hub** và gửi thông báo qua **SNS**. Phần demo dashboard phản ứng theo thời gian thực trước lưu lượng tấn công khiến cả hội trường trầm trồ.

#### Phiên 2 — Docker: nền tảng của Cloud Native (Bảo Huỳnh)

Anh Bảo Huỳnh từ Endava trình bày Docker theo cách khiến tôi hiểu ra nó không chỉ là "công cụ đóng gói" mà là điều kiện tiên quyết cho Microservices và CI/CD. Bảng so sánh với máy ảo truyền thống giúp tôi nắm bản chất rất nhanh:

| Đặc tính | Virtual Machines | Containers (Docker) |
| -------- | ---------------- | ------------------- |
| Hệ điều hành | Mỗi VM một Guest OS riêng (nặng) | Chia sẻ kernel của Host OS (nhẹ) |
| Dung lượng | Hàng GB | Chỉ vài MB đến vài trăm MB |
| Thời gian khởi động | Tính bằng phút | Tính bằng mili giây |
| Hiệu suất | Bị giới hạn bởi Hypervisor | Gần như native |
| Mật độ trên một host | Khoảng 10–100 VM | Có thể lên đến hàng nghìn container |

Phần tôi tâm đắc nhất là cơ chế **Image Layers**: một image gồm nhiều tầng **read-only** sinh ra từ các lệnh trong Dockerfile (`FROM`, `RUN`, `COPY`...), cộng với một tầng **writable** duy nhất khi container chạy. Nhờ vậy Docker tái sử dụng được **cache** — tầng nào không đổi thì không build lại — giúp rút ngắn đáng kể thời gian trong pipeline CI/CD. Trước giờ tôi cứ build lại từ đầu mỗi lần sửa code, hóa ra chỉ cần sắp xếp thứ tự lệnh trong Dockerfile hợp lý là tiết kiệm được rất nhiều thời gian.

#### Phiên 3 — Từ IT Helpdesk đến Senior Sysadmin (Trần Trung Vinh)

Anh Vinh từ Central Retail kể lại hành trình sự nghiệp của chính mình như một khung năng lực: **Helpdesk** (xử lý sự cố hằng ngày) → tự dựng **lab Networking & Linux** → tiếp cận **Infrastructure** (ảo hóa, cloud) → **Senior Sysadmin** (tự động hóa và bảo mật). Điểm mấu chốt anh nhấn mạnh là sự thay đổi về **tư duy**, không chỉ về kỹ năng:

* Từ quản lý máy chủ vật lý, cấu hình thủ công sang mô hình cloud **pay-as-you-go**, co giãn tự động và tận dụng Managed Services.
* Từ "click tay trên console" sang **Infrastructure as Code** với Terraform — hạ tầng nằm trong file cấu hình, triển khai lặp lại được và nhất quán.

Lời khuyên tôi ghi lại đậm nhất: *một portfolio với dự án thực chiến có giá trị hơn nhiều so với việc gom chứng chỉ mà thiếu thực hành*. Là sinh viên đang làm workshop thực tập, câu này đánh trúng tâm lý "học để thi chứng chỉ" mà tôi từng có.

#### Phiên 4 — Multiplayer trên Cloud: Godot & AWS WebSockets (Nguyễn Quốc Bảo)

Đây là phiên vui nhất với tôi vì có demo game **oẳn tù tì (Rock-Paper-Scissors) multiplayer** chạy hoàn toàn serverless. Anh Bảo giải thích lý do chọn **WebSocket** thay vì UDP/ENet: UDP nhanh hơn nhưng WebSocket cho kết nối **full-duplex** duy trì liên tục và độ tin cậy cao — phù hợp với game theo lượt (turn-based) hoặc hệ thống lobby.

Luồng kiến trúc: **Godot Client → API Gateway (WebSocket) → Lambda → DynamoDB**. API Gateway dùng **Route Key** (`$request.body.action`) để điều hướng từng loại yêu cầu (`$connect`, `$disconnect`, các action tùy biến) đến đúng Lambda function. Vì Lambda **stateless** — không nhớ gì giữa hai lần gọi — nên toàn bộ trạng thái trò chơi phải lưu trong DynamoDB, với mỗi item gồm `connectionId` (khóa chính), `status` (waiting/matched), `opponentId` và `choice` (búa/kéo/bao).

Phần "gotchas" là phần tôi thấy giá trị nhất:

* **Stale connections (GoneException):** người chơi thoát đột ngột nhưng dữ liệu vẫn nằm trong DynamoDB → Lambda gửi tin đến một kết nối không còn tồn tại và văng lỗi. Phải bắt exception này và dọn dữ liệu rác ngay.
* **Chi phí của Scan:** dùng `Scan` để tìm người chơi đang chờ trận thì chạy được ở quy mô demo, nhưng khi người dùng tăng lên sẽ vừa chậm vừa đắt — cần chuyển sang **Global Secondary Index (GSI)**.
* **Độ trễ do stateless:** mọi trạng thái đều phải đọc/ghi DynamoDB liên tục, đây là cái giá phải trả để đổi lấy khả năng scale của serverless.

#### Phiên 5 — Nghệ thuật làm việc nhóm hiệu quả (Trương Huy Phước)

Xen giữa các phiên kỹ thuật là một phiên kỹ năng mềm rất "đời". Anh Phước đúc kết hiệu suất nhóm vào **4 quy tắc vàng**:

1. **Mục tiêu chung rõ ràng** — cả nhóm nhìn về một hướng.
2. **Đúng người đúng việc** — giao việc theo thế mạnh từng người.
3. **Giao tiếp mở** — lắng nghe chủ động, phản hồi thẳng thắn nhưng tích cực.
4. **Trách nhiệm cá nhân** — đã nhận việc là cam kết hoàn thành.

Anh cũng nhấn mạnh việc dùng công cụ (Slack, Discord, Trello) không phải để "cho có" mà để giữ luồng thông tin xuyên suốt, tránh tình trạng thông tin chỉ nằm trong đầu một người. Nghe xong tôi nghĩ ngay đến nhóm đồ án của mình — thiếu đúng quy tắc số 1 và số 4.

#### Phiên 6 — GraphRAG với Amazon Bedrock & Neptune (Việt Phát)

Phiên khép lại chương trình cũng là phiên "mở não" nhất với tôi. Bạn Phát từ Swinburne đặt vấn đề bằng một câu hỏi ví dụ: *"Công ty được mua lại bởi tập đoàn do Jeff Bezos sáng lập có trụ sở ở đâu?"* — RAG truyền thống rất chật vật với dạng câu hỏi này vì nó đòi hỏi **suy luận đa chặng (multi-hop reasoning)**: phải nối được nhiều thực thể rời rạc với nhau. **GraphRAG** giải quyết bằng cách tổ chức tri thức thành **đồ thị** (nodes và edges), cho phép "đi bộ" (traverse) trên các mối quan hệ.

Hai hướng triển khai trên AWS được phân tích:

* **Fully Managed Route:** dùng **Amazon Bedrock Knowledge Bases** tích hợp **Neptune Analytics** — mọi công đoạn từ chunking, trích xuất thực thể (entity extraction) đến tạo embeddings và khám phá quan hệ đều được tự động hóa.
* **Custom Route:** dùng **LlamaIndex** dựng pipeline xử lý dữ liệu, lưu vào **Amazon Neptune** và truy vấn bằng ngôn ngữ **Cypher** — tùy biến sâu hơn, chủ động kiểm soát cách đồ thị được xây và truy vấn.

### 7. Những kiến thức tôi học được

* **Bảo mật phải chuyển từ rule-based sang behavior-based:** WAF là lớp phòng thủ cần nhưng chưa đủ; kết hợp ML để phát hiện hành vi bất thường mới đối phó được Zero-day. Tư duy đúng là "prevent first, fix later".
* **Hiểu cơ chế quan trọng hơn thuộc lệnh:** nắm được Image Layers và cache của Docker giúp tôi biết *vì sao* nên viết Dockerfile theo thứ tự nào, thay vì chỉ copy mẫu trên mạng.
* **Serverless không có nghĩa là không phải lo trạng thái:** vì Lambda stateless, việc thiết kế nơi lưu trạng thái (DynamoDB), xử lý kết nối chết và tối ưu truy vấn (GSI thay cho Scan) mới là phần khó thật sự của bài toán.
* **GraphRAG nâng cấp khả năng suy luận của LLM:** khi câu hỏi đòi hỏi nối nhiều mối quan hệ, cấu trúc đồ thị làm được điều mà vector search thuần túy không làm nổi.
* **Sự nghiệp là chuỗi chuyển đổi tư duy:** từ sửa sự cố → hiểu hệ thống → tự động hóa. Kinh nghiệm hands-on và operations mindset đáng giá hơn danh sách chứng chỉ.
* **Kỹ năng mềm là hạ tầng của kỹ năng cứng:** công nghệ mạnh đến đâu cũng vô nghĩa nếu nhóm không có mục tiêu chung và trách nhiệm cá nhân.

### 8. Các công nghệ nổi bật

* **Compute & Integration:** AWS Lambda, API Gateway (WebSocket), EC2, ALB.
* **Storage & Database:** Amazon S3, DynamoDB (kèm GSI), Amazon Neptune.
* **Security & Monitoring:** AWS WAF, Security Hub, GuardDuty, Kinesis Data Firehose, CloudWatch, Amazon SNS.
* **AI/ML & GenAI:** Amazon Bedrock (Knowledge Bases), Neptune Analytics, LightGBM, XGBoost, Random Forest, LlamaIndex, ngôn ngữ truy vấn Cypher.
* **DevOps & khác:** Docker (Image Layers, build cache), Terraform (IaC), Godot Engine.

### 9. Điều có thể áp dụng vào dự án cá nhân

* **Viết lại Dockerfile của các project cũ:** sắp xếp lệnh theo nguyên tắc tầng ít thay đổi nằm dưới để tận dụng cache, đo lại thời gian build trước và sau.
* **Áp dụng bài học DynamoDB vào workshop thực tập:** nếu có bài toán tra cứu theo thuộc tính không phải khóa chính, dùng GSI ngay từ đầu thay vì Scan — rẻ hơn và nhanh hơn khi dữ liệu lớn dần.
* **Thử dựng một demo WebSocket serverless nhỏ:** một ứng dụng chat hoặc game theo lượt với API Gateway + Lambda + DynamoDB, chú ý xử lý GoneException để hiểu trọn vẹn vòng đời kết nối.
* **Đưa Terraform vào lab cá nhân:** thay vì tạo VPC, subnet bằng tay trên console như các tuần trước, tôi sẽ thử mô tả lại chính hạ tầng đó bằng Terraform để có bài lab lặp lại được.
* **Xây portfolio theo lời khuyên của anh Vinh:** ưu tiên hoàn thiện 1–2 dự án chạy thật, có kiến trúc và tài liệu rõ ràng, thay vì dàn trải nhiều repo dở dang.
* **Tìm hiểu GraphRAG ở mức nhập môn:** bắt đầu với Bedrock Knowledge Bases (hướng managed) trước khi thử hướng custom với LlamaIndex + Neptune.

### 10. Cảm nhận sau sự kiện

So với sự kiện trước, lần này tôi đến với tâm thế chủ động hơn hẳn — biết mình muốn nghe gì và cần hỏi gì. Cảm nhận rõ nhất là sự **đa dạng về xuất phát điểm** của các diễn giả: có người là sinh viên như tôi (anh Đại, bạn Phát) nhưng đã xây được hệ thống NIDS hay pipeline GraphRAG hoàn chỉnh; có người đi lên từ Helpdesk như anh Vinh. Điều đó phá bỏ suy nghĩ "phải đủ giỏi mới dám làm" — ai cũng bắt đầu từ một điểm nào đó, quan trọng là bắt tay vào làm thật.

Về chuyên môn, tôi thích cách các phiên demo không giấu phần "xấu xí": lỗi GoneException, chi phí của lệnh Scan, độ trễ của kiến trúc stateless — những thứ tutorial trên mạng hiếm khi nhắc nhưng lại là điều người làm thật phải đối mặt hằng ngày. Câu khẩu hiệu của cộng đồng — *"Bắt đầu từ đâu không quan trọng, hãy tiếp tục bước đi"* — nghe đơn giản nhưng đọng lại trong tôi lâu nhất.

### 11. Kết luận

FCJ Community Day cho tôi một bức tranh trọn vẹn về hệ sinh thái Cloud-Native: bảo mật thông minh ở lớp ngoài, container chuẩn hóa ở lớp đóng gói, serverless ở lớp vận hành, GraphRAG ở lớp ứng dụng — và con người với kỹ năng làm việc nhóm ở trung tâm. Ba việc tôi sẽ làm ngay sau sự kiện: (1) tối ưu lại Dockerfile và thử Terraform cho bài lab thực tập, (2) dựng một demo WebSocket serverless nhỏ để tự trải nghiệm các "gotchas" đã nghe, và (3) bắt đầu xây portfolio theo hướng dự án thực chiến. Sự kiện một lần nữa nhắc tôi rằng con đường học Cloud không có lối tắt, nhưng luôn có cộng đồng đồng hành.
