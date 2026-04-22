name: 04-review-tc
version: 1.1.0
description: >
  Review và đánh giá chất lượng test case có sẵn: phân tích coverage 12 nhóm và toàn bộ rule inventory, phát hiện lỗ hổng, đề xuất TC bổ sung đúng format nguồn (Functional TSV hoặc SIT format chuyên biệt).
  Trigger: review testcase có sẵn, đánh giá TC từ sprint trước, TC viết thủ công, TC nhận từ team khác, coverage analysis file TC hiện có, gap analysis testcase đã có.
  Output: 3 bảng review (bản đồ bao phủ + gap analysis + TC bổ sung).

## Quy tắc lưu output
- Nếu skill cần ghi output ra file/thư mục, phải tự động tạo đầy đủ thư mục đích nếu chưa tồn tại trước khi lưu file.

## Quy tắc ngôn ngữ
- Nội dung mô tả, test artifact và output do skill tạo ra phải viết bằng tiếng Việt có dấu.
- Chỉ giữ nguyên không dấu cho mã kỹ thuật, ID, endpoint, field name, status code, keyword hệ thống và tên file.

## Đầu vào
| Trường | Bắt buộc? | Mô tả |
|---|---|---|
| Tài liệu yêu cầu | ✔ | US / AC / BR — mỗi yêu cầu có ID riêng |
| File test case | ✔ | Danh sách TC đã viết (TSV, Excel, hoặc Markdown; có thể là Functional hoặc SIT) |
Nếu thiếu một trong hai → ghi `[Cần bổ sung]`, liệt kê câu hỏi làm rõ, dừng lại.

### Bảng 1 — Bản đồ bao phủ (Coverage Map)
Mỗi dòng = 1 Rule ID/AC/BR từ tài liệu yêu cầu.
Header (chuẩn hóa 15 cột, đồng bộ references/tc-template.tsv):
```
STT	Summary	Test Level	Precondition	Test Data	Step summary	Expected result	Priority	Story Linkages	Test Type	Smoke	Auto	Phụ thuộc TC	Teardown	Trace
```

### Bảng 2 — Gap analysis (Per-rule)
Header:
```
Rule ID	Nhóm còn thiếu	Mô tả lỗ hổng	Đề xuất TC bổ sung	Priority	Câu hỏi làm rõ
```

### Bảng 3 — TC bổ sung nội bộ (Supplemental TC)
Header:
```
STT	Summary	Test Level	Precondition	Test Data	Step summary	Expected result	Priority	Story Linkages	Test Type	Smoke	Auto	Phụ thuộc TC	Teardown	Trace	Note
```

## Review gate coverage (bổ sung)
- Review phải kiểm tra đủ 3 thứ:
  1. Đủ rule inventory chưa (mỗi Rule ID phải có TC hoặc N/A)
  2. Trace đúng Rule ID chưa
  3. Gap đã biến thành TC bổ sung trong TSV chính chưa
- Nếu inventory còn rule chưa cover hoặc Trace không hợp lệ → BLOCKED, Kết quả gate: Not Approved
- Không được ghi Kết quả gate: Approved nếu chưa merge đủ gap bổ sung vào TSV chính.
