# Automation BrandMaster CTQT - Kiểm Thử AI-Driven (v1.0.0)

Chào mừng bạn đến với dự án kiểm thử được cấu trúc hóa theo mô hình trợ lý AI. Hệ thống này hoạt động như một "Engine rời" — có thể đứng độc lập hoặc **tích hợp trực tiếp vào trong bất kỳ dự án có sẵn nào**.

Mặc dù việc tích hợp sẽ thêm một số folder vào Root của dự án, nhưng bạn chỉ cần nắm được luồng dữ liệu 3 bước (Input -> Engine -> Output) là có thể sử dụng dễ dàng.

## ⚙️ Yêu cầu công cụ (Prerequisites)
Hệ thống này được thiết kế để một AI Assistant đọc và thực thi. Bạn có thể sử dụng bất kỳ công cụ giao tiếp AI nào hỗ trợ đọc project context, điển hình như:
- CLI/Chat: **Claude Code**, **Code x**, **Claude chat** (upload thư mục dự án vào tính năng Projects của Claude.ai).
- IDE Extensions & Agents: **Copilot**, **Cursor**, **Cline**, hoặc **Antigravity**.

---

## 📂 Bản Đồ Cấu Trúc (Cho cấu hình Tích Hợp/Embedded)

🚨 **LƯU Ý QUAN TRỌNG:** Bản Engine này **bắt buộc phải được đặt ở cấp Root (gốc) cao nhất** của dự án. Tuyệt đối không nhét vào các thư mục con (ví dụ: `qa-tools/QA-AI-Engine/`) vì điều này sẽ làm đứt gãy các đường dẫn tham chiếu tương đối (`../../references/`) đang được cấu hình ngầm trong bộ não.

Khi bộ QA Skills này được "nhúng" vào một project chuẩn của Dev/BA, cấu trúc lúc này vận hành làm 3 pha tự nhiên:

### Nhóm 1: Nguồn Dữ Liệu Của Team (INPUT)
AI sẽ đọc thẳng yêu cầu từ thư mục documentation đang có sẵn của team (ví dụ thư mục `docs/`).
- **Thư mục doc của dự án (VD: `docs/` hay `requirements/`):** Chứa tài liệu AC, BR, flow API thực tế.
- **`qa-config.yaml`:** Cấu hình chuẩn của AI (biến môi trường, tên module...), đặt ngay tại thư mục root chung. (Mẹo: Có thể copy file mẫu từ `references/qa-config-schema.yaml` làm điểm khởi đầu, hoặc yêu cầu `Skill 01.1` tự động tạo).

### Nhóm 2: “Bộ não” của Hệ Thống Kiểm Thử (ENGINE)
Đây là các folder đi theo bộ công cụ mà bạn đã sao chép vào. **Khuyến nghị KHÔNG CẦN CHỈNH SỬA các nội dung này.**
- **`skills/`**: Chứa thuật toán và hướng dẫn prompt. Mỗi folder (`01` đến `15`) là một kỹ năng AI tự đọc.
- **`references/`**: Các biểu mẫu (Mẫu testcase TSV, HTML, Json Schema) để AI định dạng file đầu ra tiêu chuẩn.
- **`SKILL.md` (nằm ở gốc)**: "Công tắc" nguồn bật/tắt kỹ năng. File chứa prompt mô tả để AI đọc và định tuyến vào đúng thư mục `skills/` theo yêu cầu user.

### Nhóm 3: Nơi thu hoạch kết quả (OUTPUT)
Nơi AI tự sinh ra file sau mỗi câu lệnh của bạn.
- **`testing-output/`** (hoặc bạn có thể override vào thẳng mục `tests/` của Dev thông qua file `qa-config.yaml`). Tại đây chia sẵn các nhánh cho: Test plan, Testcases, Test reports.

---

## 🚀 Hướng Dẫn Sử Dụng Nhanh (Cheat Sheet)

Bạn chỉ cần nhớ quy trình 3 bước:

### Bước 1: Chuẩn bị Dữ Liệu Input
Đảm bảo tài liệu tính năng (features, AC, API Specs...) đang hiện diện trong môi trường code của team. *Nếu dự án quá phức tạp, có thể tạo 1 file `index.md` nháp dẫn link lại các tài liệu cho gọn.*

### Bước 2: Ra Lệnh Cho AI Theo Keyword
Bạn mở file **`SKILL.md` (ở Root)**, xem các "Keyword" trong Bảng định tuyến. Khi rảnh chat chứa Keyword đó, AI sẽ tự móc "Skill não bộ" ra thực thi.

**Một số ví dụ câu chat:**
- *"Tạo test plan cho tài liệu trong thư mục docs/Sprint-01 nhé"*
- *"Viết testcase chức năng cho requirement GhepRule.md nha"*
- *"Tạo data test cho chức năng thanh toán nha"*
- *"Gen automation script ra thư mục tests/ui "*

### Bước 3: Nhận Kết Quả
Chờ AI chạy xong và vào thu gom đánh giá tại `testing-output/` (hoặc thư mục đích bạn đã chọn).
**Ví dụ:** AI test xong tính năng GhepRule, file kết quả testcase sẽ lưu tại:  
👉 `testing-output/test-cases/functional/tc-gheprule-Sprint-01.tsv`

### Bước 4: Chuyển Giao Sang Sprint Mới
Khi hoàn tất một chặng và bước sang Sprint tiếp theo (VD: từ Sprint-01 lên Sprint-02):
1. **Mở file `qa-config.yaml`** và sửa thông số ở dòng `project.sprint` thành `Sprint-02`.
2. **Không cần dọn dẹp thư mục `testing-output/` của Sprint cũ!** Bởi vì mọi file xuất xưởng đều tự động đính kèm hậu tố tag `{sprint}` và `{timestamp}`. Việc sinh dữ liệu mới sẽ được xếp ngăn nắp, an toàn bên cạnh dữ liệu cũ mà không bao giờ rủi ro bị ghi đè.

---

## 🧠 Cơ Chế Học Hỏi & Cải Tiến (Feedback Loop)

Hệ thống hoạt động nối tiếp cực kì nhạy bén qua các Sprint nhờ 2 cơ chế đặc thù:
1. **Cấp độ Dự án (Tự động):** Hệ thống cung cấp một bản lưu trữ "Trí nhớ" tại `references/qa-improvement-log.md`. Cuối mỗi Sprint, khi bạn gọi lệnh làm Test Report (Skill 08), AI sẽ đúc rúc kinh nghiệm lỗi (VD: "Module A đang có nhiều bug lặp lại") và lưu vào log này. Ở Sprint sau, khi phân tích viết Testcase mới (Skill 02), AI sẽ đọc ngược lại file Log này để rào bắt các rủi ro từ trước.
2. **Cấp độ Kỹ năng AI (Nâng cấp chủ động):** Tuy AI có thể học hỏi và sửa lỗi ngay trong Context chat nếu bạn nhắc (VD: *"Bạn viết thiếu các test case đồng thời / test khối lượng lớn rồi"*), nhưng hệ thống được cấu hình **KHÔNG BAO GIỜ tự sửa đổi luật trong thư mục `skills/`** nhằm tránh rủi ro làm hỏng chuẩn Framework chung của cả Team. 
   - **Để một kinh nghiệm trở thành luật vĩnh viễn:** Bạn cần chủ động sửa trực tiếp vào file `.md` (VD: `gen-tc-functional.md`) hoặc ra lệnh dứt khoát cho trợ lý: *"Hãy cập nhật rule bắt buộc kiểm tra Test Đồng Thời vào thẳng file kỹ năng số 02 trong folder skills/"*. Sau khi AI cập nhật "Não bộ", hãy chốt version Git đẩy lên `v1.1` để chia sẻ trí khôn này cho mọi người!

---

## 💡 Lời Khuyên Dành Cho Người Mới
- Không cần phải sợ hãi hay cố xem thuật toán bên trong code file `skills/`.
- Hãy coi bộ công cụ này như "Trợ lý cầm tay": Chuẩn bị sẵn file tài liệu Dev đã viết -> Gọi lệnh ở Box chat -> Nhặt kết quả TestCase ở Output.
