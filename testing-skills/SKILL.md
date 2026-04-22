---
name: testing-skill-suite
version: 1.0.0
preamble-tier: 1
description: >
  Bộ skill kiểm thử phần mềm toàn diện — hỗ trợ toàn bộ vòng đời testing từ
  lập kế hoạch đến báo cáo sau deploy. Dùng skill này khi người dùng nhắc đến
  bất kỳ hoạt động QA/QC nào: test plan, test case, data test, script test,
  review, security test, performance test, UAT, go/no-go, smoke test, test report.
  Đọc yêu cầu để xác định sub-skill phù hợp và chuyển tiếp ngay.
---

# Testing Skill Suite — Router

## Kiến trúc project-agnostic

Bộ skill này được thiết kế để dùng cho **mọi dự án** — không hardcode thông tin project.

Thông tin project-specific (domain, team, tools, environments, SLA...) được lưu trong **`qa-config.yaml`** riêng cho từng project (xem cấu trúc ở `references/qa-config-schema.yaml` hoặc copy file điền sẵn ở `references/qa-config-sample.yaml`).

**Luồng khởi động project mới:**
1. Chạy skill `01-test-plan` → tạo Test Plan
2. Chạy skill `01.1-extract-qa-config` → sinh `qa-config.yaml` từ Test Plan
3. (Optional) Chạy skill `01.2-test-design-high-level` cho chức năng có logic phức tạp → tạo HLTC dạng Markdown outline để review nhanh trước khi viết TC chi tiết
4. Lưu `qa-config.yaml` vào thư mục project — các skill tiếp theo đọc config này theo nhu cầu thực tế của từng skill

**Phân loại đọc qa-config.yaml:**
- **Nhóm A — Ưu tiên đọc, thiếu cần nguồn thay thế:** 07, 08, 11, 12, 13, 14, 15 (dùng exit_criteria, suspension_criteria, definition_of_done)
- **Nhóm B — Tuỳ chọn, có thì tốt hơn, không có vẫn chạy được:** 01.2, 02, 03, 04, 05, 06, 09, 10 (dùng scope, URL, domain rule)
- **Nhóm C — Tạo/chuyển đổi config:** 01.1
- **Nhóm D — Đọc nếu đã tồn tại, không phụ thuộc:** 01

---

## Thứ tự thực hiện trong sprint (Execution DAG)

Để giảm tải context của router, sơ đồ đầy đủ đã tách sang:
- `references/execution-dag.md`

Tóm tắt nhanh khi route:
- Planning: `01 -> 01.1 -> 01.2 (optional)`
- Thiết kế/chuẩn bị: `2.1 HLTC (optional) -> 2.2 Functional -> 2.3 SIT`
- Mapping kỹ thuật: `2.1 = 01.2`, `2.2 = 02`, `2.3 = 03`
- Sau đó: `04 -> 05 -> 06`
- Thực thi/báo cáo: `07 -> 08`
- Release: `12 -> 13 -> 14`
- Specialist song song khi cần: `09, 10, 11, 15`

---

## Quy tắc Precedence cho các cặp skill chồng trigger

Khi user dùng keyword ambiguous, áp dụng rule dưới đây theo thứ tự:

### Skill 02 (TC Functional) vs Skill 03 (TC SIT)

**Priority 1 — Scope rõ ràng từ input data:**
- Input là AC / BR / User Story → **Skill 02**
- Input là API spec / sequence diagram / service dependency → **Skill 03**

**Priority 2 — Từ khóa explicit từ user:**
- User nói "SIT", "integration test", "tích hợp" → **Skill 03**
- User nói "chức năng", "functional", "AC-based" → **Skill 02**

**Priority 3 — Suggest clarification:**
- User chỉ nói "gen TC" mà không clear scope → hỏi: "Bạn cần gen TC **chức năng** dựa AC/BR, hay **tích hợp** giữa các service?"

### Skill 02 (TC Manual) vs Skill 06 (Automation Script)

**Priority 1 — Explicit automation request:**
- User: "viết automation script" / "gen code test" / "hướng dẫn viết test" → **Skill 06** (Robot Framework mặc định cho cả API + UI)
- User: "gen manual test case" / "viết TC" → **Skill 02**

**Priority 1.1 — Functional/SIT to Script:**
- User yêu cầu "chuyển TC Functional/SIT thành script" → **Skill 06**
- Skill 06 bắt buộc đọc `references/ai-execution-spec.md` trước khi sinh code

**Priority 2 — Framework mention context:**
- Framework mention trong context planning / design → **Skill 02** (ignore framework, focus TC)
- Framework mention trong context code generation → **Skill 06**
- Ví dụ: "Dùng Robot Framework để test; gen TC như nào?" → **Skill 02**; "Viết Robot test script" → **Skill 06**

**Priority 3 — Input artifact:**
- Input là TC file (TSV/Excel) + request "gen automation script" → **Skill 06** (Robot Framework ưu tiên, nếu user không specify)
- Input là AC/ticket + request "gen TC" → **Skill 02**

**Priority 4 — Default framework:**
- Khi user yêu cầu "gen automation script" mà không specify framework → **suggest Robot Framework** (cả API + UI automation)
- Nếu user yêu cầu framework khác (Playwright/Pytest/Cypress...) → sinh theo đó

**Priority 5 — Suggest clarification:**
- User mention automation mà không clear tool → hỏi: "Bạn muốn gen automation script dùng **Robot Framework** (API + UI unified) hay framework khác?"

---

## Nguyên tắc định tuyến

Đọc yêu cầu người dùng, xác định ý định chính, chọn **đúng 1 sub-skill** bên dưới.
Nếu yêu cầu liên quan nhiều skill, hỏi người dùng muốn bắt đầu từ bước nào.

> **Lưu ý — Skill 06:** Chỉ route vào skill 06 khi người dùng **chủ động yêu cầu viết automation script** cho một TC cụ thể. Nếu người dùng chỉ nhắc đến tên framework (Playwright, Pytest, Cypress...) trong ngữ cảnh hỏi về QA artifact (test plan, test case, report) → **không route vào skill 06**, tiếp tục với skill QA phù hợp.

> **Lưu ý bổ sung — Skill 06:** Khi đã route vào skill 06, output phải theo chuẩn AI-first:
> 1) Mapping matrix, 2) danh sách file output path chuẩn, 3) DoD pass/fail checklist.
>
> **Phạm vi test level trong suite này:**
> - Áp dụng cho: `component`, `integration`, `e2e`
> - `unit test` do dev quản lý ngoài QA suite, chỉ ghi nhận metadata khi cần.

## Nguyên tắc định tuyến và tuân thủ skill (bắt buộc cho mọi từ khóa)

1. Khi nhận yêu cầu, AI PHẢI tra cứu bảng định tuyến/từ khóa trong SKILL.md để xác định đúng sub-skill.
2. PHẢI đọc kỹ file SKILL.md của sub-skill được định tuyến trước khi sinh output.
3. Output PHẢI đúng format, quy trình, artifact, checklist… như sub-skill quy định (ví dụ: Markdown outline, TSV, report, checklist, v.v.).
4. KHÔNG được trả lời theo thói quen hoặc dạng tóm tắt nếu skill yêu cầu format cụ thể.
5. Nếu có nhiều skill liên quan, phải hỏi user muốn bắt đầu từ bước nào hoặc route theo precedence.

**Checklist tự kiểm tra trước khi trả lời:**
- [ ] Đã xác định đúng sub-skill theo bảng định tuyến/từ khóa?
- [ ] Đã đọc kỹ file SKILL.md/sub-skill liên quan?
- [ ] Output đúng format, quy trình, artifact như skill quy định?
- [ ] Không bỏ sót bước review, checklist, hoặc artifact bắt buộc?

**Lưu ý:**
- Mỗi dòng bảng định tuyến đều yêu cầu: “Output phải theo đúng hướng dẫn trong file SKILL.md của sub-skill này.”
- Nếu không rõ, PHẢI đọc lại file SKILL.md của sub-skill trước khi trả lời.
---

## Quy ước thư mục dự án (Project Folder Convention)

Quy ước chi tiết đã tách sang:
- `references/project-folder-convention.md`

Lưu ý ngắn:
- Ưu tiên path trong `output_paths` nếu có trong `qa-config.yaml`.
- Skill 07/08 bắt buộc dùng naming report có timestamp.

---

## Bảng định tuyến

| Người dùng nhắc đến | Sub-skill |
|---|---|
| test plan, kế hoạch kiểm thử, QA planning, sprint test plan | `skills/01-test-plan/test-plan.md` |
| extract qa config, parse test plan thành config, tạo qa-config.yaml, sinh config từ test plan | `skills/01.1-extract-qa-config/extract-qa-config.md` |
| test design, high level testcase, mindmap test, hltc, thiết kế test high-level, review testcase trước khi viết chi tiết | `skills/01.2-test-design-high-level/test-design-high-level.md` |
| viết testcase, gen TC, tạo test case, test case chức năng | `skills/02-gen-tc-functional/gen-tc-functional.md` |
| SIT, integration test, kiểm thử tích hợp, API contract test | `skills/03-gen-tc-sit/gen-tc-sit.md` |
| review testcase có sẵn, đánh giá TC từ sprint trước, TC viết thủ công, TC nhận từ team khác, coverage analysis file TC hiện có | `skills/04-review-tc/review-tc.md` |
| data test, dữ liệu kiểm thử, tạo data, test data | `skills/05-gen-data-test/gen-data-test.md` |
| automation script, gen script, viết automation test, hướng dẫn viết Playwright script, hướng dẫn viết Pytest script | `skills/06-gen-script-test/gen-script-test.md` |
| kết quả test, pass/fail, bug triage, daily report, retest | `skills/07-check-result/check-result.md` |
| test report, sprint report, test summary, báo cáo kiểm thử | `skills/08-test-report/test-report.md` |
| demo, chuẩn bị demo, kịch bản demo, demo sprint | `skills/09-demo-preparation/demo-preparation.md` |
| security test, OWASP, pentest, bảo mật, vulnerability | `skills/10-security-testing/security-testing.md` |
| performance test, load test, stress test, k6, JMeter, tải | `skills/11-performance-testing/performance-testing.md` |
| UAT, user acceptance, nghiệm thu, end user test | `skills/12-uat-support/uat-support.md` |
| go/no-go, deploy decision, thẩm định, xác nhận deploy, release gate | `skills/13-go-no-go/go-no-go.md` |
| smoke test production, smoke sau deploy, kiểm tra production | `skills/14-smoke-production/smoke-production.md` |
| accessibility test, a11y, WCAG, trợ năng, screen reader, keyboard navigation, contrast | `skills/15-accessibility-testing/accessibility-testing.md` |

---

## Tài liệu tham chiếu dùng chung

| File | Dùng cho skill |
|---|---|
| `references/tc-template.tsv` | 02, 04, 05 |
| `references/sit-template.tsv` | 03, 04 |
| `references/bug-severity.md` | 04, 07, 08, 13 |
| `references/report-template.md` | 07, 08, 13, 14 |
| `references/test-plan-template.md` | 01 |
| `references/test-design-mindmap-template.md` | 01.2 |
| `references/execution-dag.md` | Router |
| `references/project-folder-convention.md` | Router, 07, 08 |
| `references/qa-config-schema.yaml` | 01.1 |
| `references/qa-improvement-log.md` | 07, 08 |
| `references/owasp-checklist.md` | 10 |
| `references/performance-baseline.md` | 11 |
| `references/accessibility-checklist.md` | 15 |
| `references/exploratory-checklist.md` | 15 |
| `references/automation-framework-policy.md` | 06, 04, 08 |
| `references/ai-execution-spec.md` | 06 |
