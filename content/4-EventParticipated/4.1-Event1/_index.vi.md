---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---



# Bài thu hoạch sự kiện “FCAJ Community Day”

**Ngày diễn ra:** 23/05/2026

### Mục Đích Của Sự Kiện

* Tạo không gian để những người tham dự không chỉ nghe hội thảo mà còn kết nối và truyền cảm hứng cho nhau.
* Chia sẻ các kiến thức thực tế xoay quanh công nghệ AI, Cloud và các câu chuyện kinh nghiệm từ các chuyên gia.
* Cập nhật tình hình thị trường việc làm IT trong bối cảnh AI phát triển mạnh mẽ.

### Danh Sách Diễn Giả

* **Nguyễn Gia Hưng** - Solution Architect tại AWS Việt Nam.
* **Tình Trương** - Platform Engineer tại GoTymeX.
* **Hải Anh** - Pacific Việt Nam.
* **Nguyễn Hấn Thịnh** - DevOps Engineer.
* **Uyển & Thảo** - Thành viên nhóm UTM Morpo.
* **Đức Đào** - Solution Architect tại Cloud Kinetics.
* **Vy Lâm** - Senior Business Systems Analyst tại VPBank.

### Nội Dung Nổi Bật

#### Thị trường việc làm & Sự bùng nổ của AI (Nguyễn Gia Hưng)

* Sự phát triển của AI làm chi phí tạo ra phần mềm rẻ hơn, dẫn đến nhu cầu và số lượng phần mềm tăng khủng khiếp.
* Xuất hiện các cơ hội việc làm mới như đi sửa code do AI sinh ra, vận hành và scale hệ thống (Platform Engineering).
* Bằng đại học vẫn rất quan trọng trong khâu tuyển dụng để vượt qua vòng lọc hồ sơ của HR.
* Ứng viên cần có kiến thức nghiệp vụ thực tế và sản phẩm thật (product) thay vì chỉ làm demo.

#### Làm chủ Context khi sử dụng AI (Tình Trương)

* AI thường đưa ra kết quả không tốt do thiếu ngữ cảnh (context), không phải do model kém.
* Hội chứng "Internet Puller": Việc ném tất cả mọi thông tin, code, plugin vào AI mà không chọn lọc sẽ làm AI bị nhiễu và tốn token.
* Cần cung cấp context đúng trọng tâm: Mục tiêu (Goal), tình huống, giới hạn (constraints) và tiêu chí thành công.
* Khái niệm "Second AI Brain": Hệ thống lưu trữ, truy xuất ngữ cảnh và bộ nhớ để giúp AI cá nhân hóa và làm việc hiệu quả hơn.

#### Phân tích dữ liệu với Amazon Q & QuickSight (Hải Anh)

* Sử dụng AI Agent để đọc hiểu các file data (như Excel) và tự động tạo ra các dashboard phân tích mà không cần kỹ năng BI chuyên sâu.
* Giới thiệu về MCP (Model Context Protocol) như những "cánh tay nối dài" giúp AI tương tác trực tiếp với các dịch vụ khác.

#### Tối ưu chi phí và bảo mật với Amazon CloudFront (Nguyễn Hấn Thịnh)

* Giới thiệu cơ chế tính giá mới (Flat-rate pricing) của CloudFront giúp doanh nghiệp kiểm soát chi phí, tránh tình trạng "bill spike".
* CloudFront hỗ trợ bảo mật mạnh mẽ: Chống DDoS, tích hợp WAF, hỗ trợ HTTPS/mTLS và tính năng VPC Origin giúp ẩn hoàn toàn hạ tầng khỏi Internet.
* Tối ưu hiệu suất thông qua cơ chế Caching đa tầng và giao thức HTTP/3.

#### Kinh nghiệm thực chiến Hackathon 36 giờ (Uyển & Thảo)

* Hành trình xây dựng ứng dụng AI tạo và chỉnh sửa UI/UX (UTM Morpo) trong 36 tiếng liên tục.
* Kiến trúc sử dụng hệ thống Agent để phân tích hình ảnh (wireframe), tạo CSS/Layout và sinh ra HTML code.
* Những khó khăn thực tế: Hết token limit giữa chừng, AI sinh code lan man (over-generation) và cạn kiệt sức khỏe.

#### Tính "không xác định" của LLM (Đức Đào)

* Dù cấu hình Temperature = 0, kết quả trả về từ các LLM (đặc biệt qua API) vẫn không giống nhau 100%.
* Nguyên nhân kỹ thuật: Do cách máy tính làm tròn số thập phân và cơ chế tính toán song song của GPU.
* Nguyên nhân thương mại: Các nhà cung cấp áp dụng Inference Optimization (gộp nhiều prompt lại để xử lý chung) làm thay đổi điểm số của từ vựng.
* Giải pháp: Chạy nhiều lần để lấy kết quả chung, tự host model ở local, hoặc bắt buộc phải thiết kế hệ thống downstream có khả năng handle lỗi.

#### Xây dựng Multi-Agent cho Doanh nghiệp (Vy Lâm)

* Sử dụng Multi-Agent System để giải bài toán đánh giá tín dụng cho Startup, nơi dữ liệu rất đa chiều (tài chính, thị trường, đội ngũ).
* Single Agent sẽ bị quá tải context, loãng chuyên môn và tạo ra rủi ro (single point of failure).
* Kiến trúc Multi-Agent hoạt động như một hội đồng: Có Manager, Financial Analyst, Market Analyst, Team Evaluator cùng hoạt động song song và kiểm tra chéo nhau.
* Nhấn mạnh tầm quan trọng của Guardrails & Compliance trong môi trường doanh nghiệp (bảo mật dữ liệu, chống prompt injection, audit trail).
* Triển khai dự án AI trong doanh nghiệp cần có bài toán ROI rõ ràng và lộ trình (Roadmap) cụ thể.

### Những Gì Học Được

#### Về Tư Duy Phát Triển

* **Business-first approach:** Mọi giải pháp công nghệ hay AI đều phải xuất phát từ việc giải quyết bài toán của người dùng cuối hoặc doanh nghiệp.
* **Sản phẩm thật:** Trong thời đại AI, kỹ năng tạo ra một sản phẩm hoàn chỉnh và vận hành được quan trọng hơn là chỉ làm các bài tập demo.
* **Context Engineering:** Nắm được kỹ năng cô đọng và thiết kế ngữ cảnh chính xác cho AI là kỹ năng cốt lõi trong tương lai.

#### Về Kiến Trúc Kỹ Thuật

* Hiểu rõ khi nào nên dùng Single Agent và khi nào bắt buộc phải dùng Multi-Agent (khi cần đa chuyên môn và có tính quyết định cao).
* Nhận thức được bản chất không xác định (probabilistic) của LLM ngay cả khi Temperature = 0 để thiết kế hệ thống linh hoạt, chống lỗi.
* Biết cách bảo vệ hệ thống AI qua việc áp dụng Guardrails, chặn Prompt Injection và bảo mật VPC.

### Ứng Dụng Vào Công Việc

* **Tối ưu hóa Prompts:** Ngừng ném dữ liệu rác (Internet Puller) vào AI, thay vào đó thiết lập rõ Goal, Situation, và Constraints cho mỗi task.
* **Thiết kế hệ thống chịu lỗi:** Khi làm việc với LLM API, phải xây dựng cơ chế test kỹ lưỡng và handle các format sai lệch từ AI do tính không xác định của nó.
* **Áp dụng Multi-Agent:** Đề xuất và thử nghiệm mô hình chia nhỏ các LLM Agents theo từng chức năng (như hội đồng) cho các task phức tạp tại công ty.
* **Đề cao Security:** Luôn tích hợp kiểm tra bảo mật (API Key rotation, data filtering) trước khi đưa bất kỳ công cụ AI hoặc MCP nào vào môi trường production của doanh nghiệp.

### Trải nghiệm trong event

Tham gia sự kiện **FCAJ Community Day** mang lại cho tôi rất nhiều góc nhìn sâu sắc về cả khía cạnh kỹ thuật lẫn kỹ năng sinh tồn trong ngành IT hiện nay. Một số trải nghiệm nổi bật:

#### Cập nhật bức tranh thực tế về thị trường

* Những chia sẻ thẳng thắn từ anh Hưng giúp tôi nhận ra áp lực cũng như cơ hội khi AI đang thay đổi cách làm phần mềm.
* Việc trang bị kiến thức nghiệp vụ và tự làm ra sản phẩm thực tế là chìa khóa để cạnh tranh.

#### Góc nhìn từ môi trường Enterprise

* Rất ấn tượng với phần chia sẻ của chị Vy Lâm về cách ứng dụng AI trong ngân hàng.
* Tôi nhận ra ở môi trường doanh nghiệp lớn, việc code cho AI chạy được chỉ là một phần nhỏ, phần lớn nỗ lực phải dành cho Security, Compliance và tính toán ROI.

#### Kiến thức Technical sâu sắc

* Việc hiểu được lý do tại sao AI sinh ra kết quả khác nhau dù set Temperature = 0 (từ anh Đức) giúp tôi gỡ bỏ được nhiều lầm tưởng về LLM.
* Biết thêm các giải pháp tối ưu mạng và chi phí với Amazon CloudFront.

#### Bài học rút ra

* Đừng trì hoãn việc học hỏi và ứng dụng AI đúng cách, vì AI đang tiến hóa vô cùng nhanh.
* Không nên phó mặc hoàn toàn cho AI mà thiếu đi sự kiểm soát, tư duy phản biện và kiến thức nền tảng vững chắc của một kỹ sư phần mềm.

#### Một số hình ảnh khi tham gia sự kiện

* *(Thêm các hình ảnh của bạn tại đây)*

> Tổng thể, FCAJ Community Day không chỉ là một sự kiện cập nhật công nghệ mà còn là nơi đánh thức tư duy, kết nối cộng đồng và định hướng rõ ràng hơn con đường phát triển sự nghiệp trong kỷ nguyên GenAI.
