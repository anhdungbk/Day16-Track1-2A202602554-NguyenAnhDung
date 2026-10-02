# Memo Teardown — Cursor

**Họ tên:** Nguyễn Anh Dũng 
**MSSV:** 2A202602554
**Vì sao chọn sản phẩm này:** Cursor là một ví dụ rõ về cách một sản phẩm AI không dừng ở việc “gắn chatbot vào IDE” mà liên tục đẩy mức trừu tượng lên cao hơn: từ autocomplete, chỉnh sửa nhiều file, agent tự dùng terminal, đến cloud agent và hệ thống điều phối nhiều agent. Chuỗi thay đổi này đủ dài để quan sát cách sản phẩm xây moat quanh workflow thay vì chỉ phụ thuộc vào model nền.

---

## §1. Timeline các cập nhật lớn

| Thời điểm | Cập nhật | Context lúc đó | Nguyên lý |
|---|---|---|---|
| **03/2023** | Cursor xuất hiện như một code editor xây quanh AI, tích hợp chat và chỉnh sửa code ngay trong editor thay vì phải copy/paste qua ChatGPT. [Nguồn](https://news.ycombinator.com/item?id=35285047) | Sau khi ChatGPT bùng nổ, developer bắt đầu dùng LLM để hỏi code nhưng workflow còn rời rạc: viết code trong IDE, copy sang chatbot, rồi copy kết quả ngược lại. | **x10 UX / workflow integration:** lợi thế ban đầu không nhất thiết là model tốt hơn, mà là giảm mạnh số bước trong một workflow có tần suất rất cao. |
| **07/2024** | Ra mắt **Composer (beta)** cho phép chỉnh sửa nhiều file trong một phiên. [Nguồn](https://cursor.com/changelog/0-37-x) | AI coding lúc đó vẫn chủ yếu mạnh ở autocomplete hoặc chỉnh một đoạn code. Khi model context dài hơn và khả năng sửa code tốt hơn, giới hạn “một file / một đoạn” bắt đầu trở thành nút thắt. | **Tăng abstraction level:** từ hỗ trợ từng dòng code sang giao một “ý định” để hệ thống tự thay đổi nhiều phần liên quan. Đây là bước đầu rời khỏi wrapper chat đơn thuần. |
| **11–12/2024** | Cursor đưa **Agent** vào Composer: agent tự chọn context, dùng terminal, đọc lỗi linter, chạy command và có thể chỉnh nhiều vị trí. [Nguồn 1](https://cursor.com/changelog/0-43-x) · [Nguồn 2](https://cursor.com/changelog/0-44-x) | Model thế hệ mới đã đủ tốt để không chỉ sinh code mà còn tham gia vòng lặp “đọc → sửa → chạy → quan sát lỗi → sửa tiếp”. Thị trường bắt đầu dịch từ copilot sang agent. | **Vòng lặp học/feedback loop:** chất lượng không còn đến từ một lần sinh code, mà từ việc agent liên tục quan sát trạng thái môi trường rồi tự sửa. |
| **05/2025** | Ra mắt **Background Agent**, cho phép chạy task song song ở môi trường từ xa; đồng thời làm mới cách quản lý context và pricing. [Nguồn](https://cursor.com/changelog/0-50) | Khi agent bắt đầu xử lý task dài hơn, việc buộc developer ngồi chờ trong editor trở thành bottleneck. Nhu cầu mới là “delegate task” thay vì “chat cùng AI”. | **Async delegation / human leverage:** AI tạo giá trị lớn hơn khi chiếm thời gian máy thay vì chiếm thời gian người. Đây là bước từ pair-programmer sang junior engineer có thể nhận việc. |
| **10/2025** | Cursor giới thiệu bộ tính năng **Enterprise** gồm Hooks, Analytics, Audit Log, Sandbox Mode và rule quản trị. [Nguồn](https://cursor.com/blog/enterprise) | Khi agent bắt đầu được dùng trong doanh nghiệp, vấn đề không còn chỉ là “code tốt không” mà còn là quyền truy cập, audit, security, policy và đo lường tác động. | **Vertical workflow moat + switching cost tổ chức:** với enterprise, moat nằm ở governance, dữ liệu sử dụng, policy và tích hợp quy trình nội bộ — những thứ model nền khó hấp thụ chỉ bằng nâng cấp model. |
| **02/2026** | **Cloud Agents with Computer Use**: mỗi agent có VM riêng, có thể chạy phần mềm vừa tạo, test, chụp ảnh/quay video và trả về PR có artifact để review. [Nguồn](https://cursor.com/changelog/02-24-26) | Agent coding thường thất bại ở bước cuối vì không thật sự “dùng” được ứng dụng như người dùng. Computer use giúp đóng vòng kiểm thử end-to-end. | **Closed-loop execution:** agent không chỉ tạo output mà tự xác minh output trong môi trường thật. Đây là bước quan trọng để tiến từ “sinh code” sang “hoàn thành công việc”. |
| **04/2026** | Ra mắt **Cursor SDK**, cho phép developer gọi chính runtime/harness/model của Cursor để xây agent lập trình bằng code. [Nguồn](https://cursor.com/changelog/sdk-release) | Khi Cursor đã tích lũy harness, cloud VM, model routing và kinh nghiệm orchestration, phần giá trị này có thể được productize ra ngoài IDE. | **Platformization / moat từ hạ tầng:** biến năng lực nội bộ thành platform cho bên thứ ba. Giá trị chuyển từ giao diện editor sang agent runtime + infra + orchestration. |
| **09/2026** | Ra mắt **Projects**: một project có thể giữ context trong nhiều tháng, giao việc cho hàng nghìn subagent và thực hiện recurring work mà không cần prompt lại từng lần. [Nguồn](https://cursor.com/blog/projects) | Sau giai đoạn agent đơn lẻ, bài toán tiếp theo là quản lý nhiều agent, duy trì mục tiêu dài hạn và giảm micro-management của con người. | **Move up the stack:** khi tác vụ cấp thấp được tự động hóa, sản phẩm phải đẩy user lên tầng cao hơn — từ “viết code” → “giao task” → “quản lý outcome”. Đây cũng là bước xây moat bằng orchestration và memory dài hạn. |

### Vì sao chọn những mốc này

Tôi chọn các mốc trên vì mỗi mốc làm thay đổi **vai trò của Cursor trong workflow**, chứ không chỉ tăng chất lượng một feature nhỏ. Chuỗi chính có thể đọc thành: **AI-assisted editor → multi-file editor → agent → background worker → enterprise platform → autonomous cloud agent → SDK/platform → multi-agent project system**.

Tôi loại các mốc như tăng tốc Cursor Tab, auto-import hay các bản sửa UI vì dù hữu ích, chúng chủ yếu là tối ưu chất lượng cục bộ. Chúng không thay đổi đáng kể “Cursor được thuê để làm việc gì” hoặc mức trách nhiệm mà user giao cho hệ thống.

---

## §2. Tệp user & JTBD

|  | Early adopters | Tệp hiện tại |
|---|---|---|
| **Đặc điểm** | Developer cá nhân hoặc engineer ở startup nhỏ, đã dùng VS Code, sẵn sàng thử công cụ AI mới, thường xuyên dùng ChatGPT/Copilot nhưng khó chịu vì phải copy/paste code và context. Các thảo luận Hacker News năm 2023 tập trung trực tiếp vào việc so Cursor với VS Code + Copilot. [Nguồn](https://news.ycombinator.com/item?id=37888477) | Từ developer cá nhân đến engineering team lớn và enterprise. Cursor hiện nhấn mạnh agent, governance, analytics, self-hosted/cloud workflow; tài liệu năm 2026 cho biết sản phẩm được sử dụng rộng trong các doanh nghiệp lớn. [Nguồn](https://cursor.com/blog/cursor-leads-gartner-mq-2026) |
| **JTBD chính** | “Giúp tôi viết, sửa và hiểu code nhanh hơn mà không phải rời IDE hoặc tự mang context qua lại giữa editor và chatbot.” | “Giúp team giao một phần đáng kể công việc phát triển phần mềm cho AI, nhưng vẫn kiểm soát được chất lượng, security, chi phí, quyền truy cập và quá trình review.” |
| **Trước đó họ làm bằng cách nào** | VS Code/JetBrains + GitHub Copilot cho autocomplete; khi gặp task khó thì copy code sang ChatGPT/Claude, hỏi, copy kết quả về IDE rồi tự chạy/test. | Kết hợp IDE + issue tracker + CI/CD + code review + internal tooling + nhiều AI assistant. Các task dài vẫn cần engineer theo dõi, chia nhỏ, chạy test và sửa lỗi thủ công. |
| **Điều họ trả tiền để nhận được** | Tốc độ coding và giảm context switching. | Developer leverage: một engineer có thể điều phối nhiều agent/task song song, đồng thời enterprise vẫn quản trị được rủi ro. |

### Dịch chuyển tệp

Mốc làm thay đổi tệp user mạnh nhất không phải một feature autocomplete mà là chuỗi **Agent cuối 2024 → Background Agent 2025 → Enterprise controls 2025 → Cloud Agents/Projects 2026**.

Ở giai đoạn đầu, Cursor chủ yếu hấp dẫn những developer sẵn sàng đổi editor để có UX AI tốt hơn. Khi agent có thể chạy terminal, tự test, làm việc ở cloud và tạo PR, “đơn vị giá trị” chuyển từ **một đoạn code tốt** sang **một task được hoàn thành**. Lúc này người mua không chỉ còn là developer cá nhân mà còn có engineering manager, platform team, security team và doanh nghiệp.

### Switching cost — map theo 4 forces

#### 1. Push — Vấn đề với cách làm hiện tại

User bị kéo khỏi workflow cũ vì cách làm truyền thống tốn nhiều thao tác: copy/paste giữa IDE và chatbot, tự gom context, tự chạy lệnh và tự lặp lại vòng debug. Khi task dài hơn, một copilot chỉ autocomplete không còn đủ.

Với enterprise, “push” còn đến từ chi phí nhân lực: nếu mỗi engineer phải tự làm toàn bộ implementation, test và xử lý CI thì tốc độ delivery bị giới hạn bởi số giờ con người.

#### 2. Pull — Sức hút của Cursor

Sức hút ban đầu là **AI nằm ngay trong editor và hiểu codebase**. Sau đó pull tăng mạnh khi Cursor cho phép giao task lớn hơn: agent tự dùng tool, chạy ở background, tạo PR, test UI bằng computer use và gần đây là Projects với nhiều subagent.

Điểm hấp dẫn quan trọng là user không phải thay đổi toàn bộ mental model ngay lập tức: họ vẫn ở trong môi trường giống VS Code, vẫn review diff và vẫn dùng Git/PR quen thuộc.

#### 3. Habit — Thói quen giữ user ở sản phẩm cũ

Developer có switching cost cao với editor vì keyboard shortcut, extension, theme, debug workflow và muscle memory. Việc Cursor dựa trên VS Code giúp giảm đáng kể lực cản này: user không phải học lại toàn bộ editor.

Ngược lại, khi user đã hình thành thói quen dùng Cursor Rules, Agents, Projects, cloud environment và plugin thì habit bắt đầu chuyển sang phía Cursor. Lúc đó rời Cursor không chỉ là đổi model, mà là phải dựng lại workflow đã quen.

#### 4. Anxiety — Nỗi lo khi chuyển đổi

Nỗi lo lớn nhất với AI coding agent là: agent sửa sai code, làm lộ secret, chạy command nguy hiểm, tạo technical debt hoặc tiêu tốn compute không kiểm soát.

Cursor phản ứng bằng các lớp review, sandbox, audit log, rules, analytics, self-hosted agent và artifact để người dùng xác minh kết quả. Đây là lý do enterprise control quan trọng: chúng không trực tiếp làm model thông minh hơn nhưng làm giảm “anxiety” đủ để doanh nghiệp giao task lớn hơn cho agent.

### Lực giữ user mạnh nhất

Theo tôi, lực giữ user mạnh nhất đang dần chuyển từ **habit của editor** sang **workflow switching cost**. Nếu Cursor chỉ là giao diện đẹp gọi model của OpenAI/Anthropic thì user có thể đổi sang đối thủ khá dễ. Nhưng khi team đã có rules, project context, integrations, cloud environment, analytics, security policy và agent workflow thì chi phí chuyển đổi tăng đáng kể.

Điều này cũng là moat quan trọng: model nền có thể bị commoditize, còn workflow và organizational context thì khó sao chép tức thì.

---

## §3. Ba dự đoán hướng đi (6–12 tháng tới)

### Dự đoán 1 — loại: mở rộng tính năng

- **Dự đoán:** Cursor sẽ tiếp tục đẩy **Projects** thành lớp orchestration cấp cao hơn, nơi user giao objective thay vì task đơn lẻ; hệ thống tự chia việc cho nhiều agent, theo dõi dependency, CI, review và rollout trong thời gian dài.
- **Lập luận:** Timeline đã đi liên tục từ Composer → Agent → Background Agent → Cloud Agent → Projects. Mỗi bước giảm thêm một lớp micro-management của developer. Vì vậy hướng hợp lý tiếp theo không phải thêm một chatbot mới mà là để agent tự quản lý nhiều vòng đời công việc hơn.

### Dự đoán 2 — loại: mở rộng segment / enterprise

- **Dự đoán:** Cursor sẽ tăng mạnh các lựa chọn **self-hosted, policy control, audit, cost governance và role-based agent permissions** để đi sâu vào các doanh nghiệp regulated hoặc codebase nhạy cảm.
- **Lập luận:** Từ §1, Enterprise controls, sandbox, organization management và self-hosted cloud agent đều giải quyết cùng một rào cản: doanh nghiệp không thể trao quyền cho agent nếu không kiểm soát data và hành động của agent. Ở §2, đây chính là lực “anxiety” lớn nhất đang cản adoption ở enterprise.

### Dự đoán 3 — loại: đe dọa Big Tech / moat

- **Dự đoán:** Cursor sẽ tiếp tục giảm phụ thuộc vào việc chỉ “bọc” model bên ngoài bằng cách đầu tư mạnh hơn vào **model riêng, routing, harness, eval, agent runtime và SDK**, đồng thời bán chính hạ tầng agent này như một platform.
- **Lập luận:** Nếu lợi thế chỉ là gọi model frontier của OpenAI/Anthropic/Google trong một editor tốt hơn, các hãng model hoặc Microsoft/GitHub có thể tích hợp lại feature tương tự. Việc Cursor đã phát triển model riêng như Composer, SDK, cloud runtime và Projects cho thấy công ty đang cố chuyển moat từ “UI wrapper” sang **full-stack agent system**. Đây là cách phòng thủ hợp lý nhất trước nguy cơ feature bị Big Tech hấp thụ.

---

## §4. AI Log

> Ghi chú: Bảng dưới đây phản ánh việc AI được dùng mạnh cho research và soạn bản nháp. Trước khi nộp, người làm bài nên mở lại các link nguồn chính và chỉnh những nhận định không đúng với quan điểm cá nhân của mình.

| Việc | AI làm hay bạn làm? | Bạn kiểm chứng/phán đoán lại thế nào? |
|---|---|---|
| Tìm changelog/blog và gom các mốc lớn của Cursor | **AI hỗ trợ chính** | Mở lại các link nguồn gốc trong bảng, ưu tiên changelog/blog chính thức; loại các update nhỏ chỉ là bug fix hoặc cải thiện UX. |
| Rút danh sách còn 8 cột mốc | **AI đề xuất, bạn quyết định cuối** | Kiểm tra mỗi mốc có thật sự thay đổi vai trò của sản phẩm trong workflow hay chỉ là feature nhỏ. |
| Revert từng mốc về nguyên lý | **AI tạo bản nháp** | Đối chiếu với framework đã học: x10, wrapper/moat, vòng lặp feedback, tăng abstraction level và workflow moat; sửa các nhãn quá chung chung. |
| Xác định early adopters và tệp hiện tại | **AI tổng hợp từ nguồn công khai** | So sánh thảo luận Hacker News giai đoạn 2023 với định hướng enterprise/agent trong tài liệu 2025–2026; không coi số liệu marketing của công ty là bằng chứng tuyệt đối. |
| Viết JTBD và 4 forces | **AI hỗ trợ cấu trúc**, **phần kết luận cần phán đoán cá nhân** | Đọc lại để bảo đảm JTBD được viết theo “việc cần làm”, không phải tên feature. Kiểm tra 4 forces có đủ Push, Pull, Habit và Anxiety. |
| Đưa ra 3 dự đoán 6–12 tháng | **AI đề xuất hướng và lập luận** | Chỉ giữ dự đoán có thể dẫn ngược về ít nhất một mốc timeline và một thay đổi trong tệp user; bỏ các dự đoán kiểu “sẽ thêm nhiều tính năng AI”. |
| Biên tập memo cuối | **AI tạo bản nháp hoàn chỉnh** | Người làm bài cần đọc lại toàn bộ, đổi cách diễn đạt ở những chỗ không giống cách mình nói và xác nhận các link còn truy cập được trước khi nộp. |

---

## Nguồn chính đã sử dụng

1. Hacker News — Cursor launch, 03/2023: https://news.ycombinator.com/item?id=35285047  
2. Hacker News — thảo luận Cursor AI-first editor, 10/2023: https://news.ycombinator.com/item?id=37888477  
3. Cursor — Our Problems, 10/2023: https://www.cursor.com/blog/problems-2023  
4. Cursor Changelog — Composer beta, 07/2024: https://cursor.com/changelog/0-37-x  
5. Cursor Changelog — Agent in Composer, 11/2024: https://cursor.com/changelog/0-43-x  
6. Cursor Changelog — Agent Improvements / Yolo Mode, 12/2024: https://cursor.com/changelog/0-44-x  
7. Cursor Changelog — Background Agent, 05/2025: https://cursor.com/changelog/0-50  
8. Cursor — Enterprise, 10/2025: https://cursor.com/blog/enterprise  
9. Cursor Changelog — Cloud Agents with Computer Use, 02/2026: https://cursor.com/changelog/02-24-26  
10. Cursor Changelog — Cursor SDK, 04/2026: https://cursor.com/changelog/sdk-release  
11. Cursor — Projects, 09/2026: https://cursor.com/blog/projects  
12. Cursor — Enterprise AI Coding Agents / định hướng 2026: https://cursor.com/blog/cursor-leads-gartner-mq-2026  

