---
name: 01-test-plan
version: 1.0.0
preamble-tier: 2
description: >
  Tạo Test Plan kiểm thử phần mềm đầy đủ, chuyên nghiệp theo chuẩn IEEE 829 / ISTQB
  từ thông tin sprint. Trigger: test plan, kế hoạch kiểm thử, QA planning, sprint test.
  Output: file .docx hoàn chỉnh 9 mục.
---



## Quy tắc lưu output

- Nếu skill cần ghi output ra file/thư mục, phải tự động tạo đầy đủ thư mục đích nếu chưa tồn tại trước khi lưu file.

## Quy tắc ngôn ngữ

- Nội dung mô tả, test artifact và output do skill tạo ra phải viết bằng tiếng Việt có dấu.
- Chỉ giữ nguyên không dấu cho mã kỹ thuật, ID, endpoint, field name, status code, keyword hệ thống và tên file.

## Đầu vào bắt buộc

| Thông tin | Bắt buộc |
|---|---|
| Danh sách ticket (key, summary, status) | ✅ |
| Thời gian sprint (ngày bắt đầu – kết thúc) | ✅ |
| Danh sách nhân lực QC | ✅ |
| Môi trường và công cụ | ✅ |
| Tên dự án, version, sprint | ✅ |

Thiếu thông tin → ghi `[Cần bổ sung]`, hỏi lại. Không đoán mò.

## Bước 0 — Đọc qa-config.yaml (nếu có)

Nếu project đã có `qa-config.yaml` → đọc và dùng trực tiếp các giá trị sau, không hỏi lại:

| Field trong config | Dùng cho mục Test Plan |
|---|---|
| `project.name`, `project.sprint` | Mục 1 — Thông tin chung |
| `project.domain`, `project.architecture` | Mục 3 — Phương pháp & Risk |
| `team.*` | Mục 7 — Nhân lực |
| `environments.*` | Mục 7 — Môi trường |
| `tools.*` | Mục 3 — Công cụ, Mục 9 — Deliverables |
| `scope.modules` | Mục 2 — Phạm vi |
| `exit_criteria.*` | Mục 4 — Exit Criteria |
| `suspension_criteria.*` | Mục 5 — Suspension Criteria |
| `uat.required`, `uat.stakeholders` | Mục 2 — Scope, Mục 9 |
| `accessibility.required` | Mục 2 — Loại kiểm thử áp dụng |

Nếu chưa có `qa-config.yaml` → tiến hành Bước 1 như bình thường, sau đó gợi ý người dùng chạy skill 01.1 để tạo config.

## Bước 0.5 — Đọc toàn bộ file trong thư mục dự án chứa file tính năng (VD: docs/features/) (bắt buộc)

Trước khi sinh nội dung, **đọc tất cả file `.md` trong `thư mục dự án chứa file tính năng (VD: docs/features/)`** (bỏ qua `feature-template.md`).

Từ mỗi feature file, trích xuất và ánh xạ vào Test Plan như sau:

| Trường trong feature file | Dùng cho mục Test Plan |
|---|---|
| `Module`, `Feature`, `Ticket IDs` | Mục 1 — Danh sách ticket, tài liệu tham chiếu |
| `Business goal` | Mục 1 — Bối cảnh, Mục 3 — Chiến lược |
| `In scope` | Mục 2 — In Scope (thay thế giá trị placeholder từ config) |
| `Out of scope` | Mục 2 — Out of Scope |
| `Acceptance criteria` | Mục 2 — Tiêu chí kiểm tra, Mục 4 — Exit Criteria bổ sung |
| `Business rules` | Mục 2 — Scope, Mục 3 — Kỹ thuật test (Decision Table) |
| `Main flow`, `Alternate flows`, `Negative flows` | Mục 3 — Approach, Mục 6 — Estimate (cơ sở độ phức tạp) |
| `Dependencies` | Mục 2 — Out of Scope (nếu external), Mục 8 — Rủi ro |
| `Preconditions` | Mục 7 — Tài khoản & dữ liệu test cần chuẩn bị |
| `Open questions` | Mục 8 — Rủi ro (ghi là "Chưa rõ requirement") |

**Quy tắc:**
- Nếu `scope.modules` trong `qa-config.yaml` vẫn còn placeholder (`<module 1>`, `<module 2>`...) → **bỏ qua giá trị config**, dùng `Module` và `Feature` từ feature file thay thế.
- Không bịa thêm nội dung ngoài những gì có trong feature file và config.
- Nếu thư mục `features/` không tồn tại hoặc rỗng → ghi chú `[Chưa có feature requirement]` tại Mục 2 và tiếp tục.

## Bước 1 — Xác định domain và kiến trúc

Đọc ticket (hoặc `project.domain` / `project.architecture` từ config) để xác định domain
(fintech / ecommerce / logistics / SaaS / healthcare...) và kiến trúc (monolith / microservices / mobile / API-only).
Áp dụng vào risk và strategy.

## Bước 2 — Sinh nội dung 9 mục

Đọc `../../references/test-plan-template.md` để lấy cấu trúc chi tiết.

Nguyên tắc bắt buộc:
- Không chia đều estimate — dựa trên độ phức tạp thực tế từng ticket
- Suspension Criteria phải có ngưỡng cụ thể (%, số giờ)
- Nội dung viết bằng tiếng Việt có dấu

## Bước 3 — Checklist trước khi xuất

- [ ] Đủ 9 mục, không bỏ mục nào (bao gồm 3a và 3b)
- [ ] Không còn placeholder trống trong nội dung chính
- [ ] Estimate có lý do, không chia đều
- [ ] Suspension Criteria có ngưỡng cụ thể
- [ ] Mục 3a (Execution Acceleration) đã điền — không để trống nếu TC > 50 hoặc ≤ 5 ngày
- [ ] Mục 3b (Test Data Strategy) đã điền — không ghi chung chung
- [ ] Mục 9 có pointer rõ sang skill 05 và skill 06
- [ ] Tên file đúng format: `Test_Plan_{project.code}_{project.sprint}_v{semver}_{yyyy-mm-dd}_{HHmm}.md`
- [ ] File lưu đúng thư mục: `testing-output/test-plan/` (hoặc `output_paths.test_plan` trong qa-config)

## Bước 4 — Xuất file

Nếu có tool xuất .docx: áp dụng định dạng sau.
Nếu không: xuất Markdown đầy đủ, người dùng tự convert.

Yêu cầu định dạng:
- Khổ A4, margin 1 inch, font Arial 12pt
- Heading 1 cho mục chính, Heading 2 cho mục phụ
- Bảng có header tô màu `D5E8F0`

**Naming convention bắt buộc** (theo `references/project-folder-convention.md`):
- Markdown: `Test_Plan_{project.code}_{project.sprint}_v{semver}_{yyyy-mm-dd}_{HHmm}.md`
- Docx (nếu có): `Test_Plan_{project.code}_{project.sprint}_v{semver}_{yyyy-mm-dd}_{HHmm}.docx`
- Ví dụ: `Test_Plan_BM_Sprint-01_v1.0.0_2026-04-09_1430.md`

**Lưu file vào:** `output_paths.test_plan` từ qa-config (default: `testing-output/test-plan/`)
→ `testing-output/test-plan/Test_Plan_{project.code}_{project.sprint}_v{semver}_{yyyy-mm-dd}_{HHmm}.md`

**Không ghi đè file cũ.** Mỗi lần tạo/sửa phải tạo file mới với timestamp mới.

## Completion Status

- **DONE** — Test Plan đủ 9 mục, không còn placeholder, estimate có căn cứ, suspension criteria có ngưỡng cụ thể
- **DONE_WITH_CONCERNS** — Test Plan đã xuất nhưng: {Một số mục ghi [Cần bổ sung] do thiếu thông tin / Estimate chưa đủ chi tiết / Chưa có qa-config.yaml — gợi ý chạy skill 01.1}
- **BLOCKED** — Không thể tạo do: {Thiếu danh sách ticket / Thiếu thông tin sprint / Thiếu nhân lực QC}
- **NEEDS_CONTEXT** — Cần bổ sung: {Danh sách ticket (key, summary, status) / Thời gian sprint / Danh sách nhân lực QC / Môi trường và công cụ}

