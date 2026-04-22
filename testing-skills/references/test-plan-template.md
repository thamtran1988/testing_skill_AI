# Cấu Trúc Test Plan — 9 Mục Bắt Buộc (IEEE 829 / ISTQB)

> Skill 01 đọc file này để xác định cấu trúc và nội dung từng mục.
> Quy tắc bắt buộc:
> - Đủ 9 mục, không bỏ mục nào
> - Không còn placeholder trống trong nội dung chính
> - Estimate có lý do, KHÔNG chia đều giữa các ticket
> - Suspension Criteria phải có ngưỡng cụ thể (%, số giờ) — không được mơ hồ
> - Viết bằng tiếng Việt có dấu
>
> Luồng chuẩn với Skill 01.1:
> - Hoàn thành Mục 1-9 trong template này
> - Dùng Skill 01.1 để extract sang `qa-config.yaml` theo `references/qa-config-schema.yaml`
> - Từ sprint kế tiếp, ưu tiên `qa-config.yaml` làm nguồn cấu hình vận hành cho skills 02-15

---

## Mục 1 — Thông Tin Chung

Bao gồm:
- Tên dự án, version, tên sprint
- Mã tài liệu: `TP-{ProjectCode}-{SprintX}-{YYYYMMDD}`
- Người tạo, người phê duyệt, ngày tạo, trạng thái (Draft / Review / Approved)
- Danh sách ticket trong phạm vi (key, summary, độ phức tạp ước tính: Thấp / Trung bình / Cao)
- Tài liệu tham chiếu: Sprint board, BRD/PRD, môi trường

---

## Mục 2 — Phạm Vi Kiểm Thử (Scope)

Bao gồm:
- **Trong phạm vi (In Scope):** Liệt kê từng module/tính năng sẽ test, loại test áp dụng (Functional / Regression / Smoke / SIT / Performance / Security), lý do ưu tiên
- **Ngoài phạm vi (Out of Scope):** Liệt kê những gì KHÔNG test và lý do cụ thể (không thay đổi trong sprint / chưa sẵn sàng môi trường / phụ thuộc team khác)
- **Loại kiểm thử áp dụng:** Đánh dấu từng loại: Functional / Integration / Regression / Smoke / Performance / Security / Accessibility / UAT

---

## Mục 3 — Phương Pháp Kiểm Thử (Approach)

Bao gồm:
- **Domain và kiến trúc** (xác định từ Mục 1, ảnh hưởng đến risk và strategy): Fintech / Ecommerce / Logistics / SaaS / Healthcare và Monolith / Microservices / Mobile / API-only
- **Chiến lược:** Risk-based testing — ưu tiên theo mức độ thay đổi code + impact đến core flow
- **Kỹ thuật thiết kế TC:** EP, BVA, Decision Table, State Transition, Error Guessing, Exploratory — áp dụng từng kỹ thuật cho loại feature nào
- **Quy trình thực hiện:** Smoke → Functional → Integration → Regression → Retest → Report
- **Công cụ:** Tên cụ thể cho từng mục đích (quản lý TC, API test, automation, performance, security)

### 3a — Chiến lược thực thi nhanh (Execution Acceleration)

> Bắt buộc điền nếu số TC > 50 hoặc thời gian test ≤ 5 ngày làm việc.

- **Nhóm rule/TC theo pattern:** Liệt kê các nhóm TC có cùng cấu trúc input-output có thể chạy theo batch (ví dụ: toàn bộ "direct ref rules" dùng chung 1 template data, chỉ thay 1 trường)
- **Thứ tự ưu tiên thực thi:** Rule/TC nào phải chạy trước (S1 risk cao nhất), nhóm nào có thể defer nếu hết giờ
- **Mutation strategy:** Với multi-criteria rules, bắt đầu từ golden dataset (tất cả criteria OK) rồi mutate từng tiêu chí — tránh viết lại data từ đầu cho mỗi TC
- **Automation-first:** TC nào sẽ chạy tự động (script từ skill 06), TC nào manual — ghi rõ tỷ lệ mục tiêu
- **AI-assisted execution:** Nếu feature có AI/rule engine, mô tả cách dùng AI để gen input variant tự động và validate output hàng loạt (tham chiếu `references/ai-execution-spec.md`)
- **Parallel execution:** Nhóm TC nào có thể chạy song song (không phụ thuộc nhau về state/data)

### 3b — Chiến lược sinh test data

> Bắt buộc điền — không để trống hoặc ghi chung chung "chuẩn bị data trước khi test".

- **Golden dataset:** Mô tả 1 bộ data chuẩn (đủ điều kiện happy path) dùng làm gốc để mutate cho toàn bộ TC
- **Loại data cần sinh:** BVA boundary values, EP partitions, normalize variants (tên viết tắt / dấu câu / chữ hoa), threshold values (ví dụ: goods overlap 0.29/0.30/0.31)
- **Công cụ / cách gen:** Script JSON/TSV tự động, parameterized table, hay thủ công — nếu thủ công thì ghi rõ ai làm và mất bao lâu
- **Traceability:** Data file lưu tại `testing-output/test-data/`, có manifest kèm theo (xem skill 05)
- **Teardown:** Cách reset data sau mỗi lần chạy để TC độc lập nhau
- **Pointer:** Chạy **skill 05** để sinh master dataset đầy đủ sau khi có danh sách TC

---

## Mục 4 — Tiêu Chí Hoàn Thành (Exit Criteria)

Bao gồm hai phần:

**Tiêu chí PASS (điều kiện để release):**
- Pass rate: ≥ {X}% (cụ thể hóa theo dự án)
- S1 (Critical) còn open: = 0
- S2 (High) còn open: ≤ {N} (cụ thể hóa)
- TC đã thực hiện: ≥ {X}% tổng TC
- Regression pass rate: = 100%

**Tiêu chí FAIL (dấu hiệu phải dừng / không release):**
- Pass rate: < {X}%
- S1 còn open: ≥ 1
- Môi trường không ổn định: > {X} giờ trong ngày

---

## Mục 5 — Tiêu Chí Tạm Dừng & Tiếp Tục (Suspension Criteria)

> ⚠️ Quy tắc bắt buộc: MỌI điều kiện tạm dừng phải có ngưỡng số cụ thể — không được viết mơ hồ như "khi có nhiều bug" hay "môi trường không tốt".

Bao gồm:

**Điều kiện tạm dừng** — mỗi điều kiện phải ghi rõ ngưỡng:

| Điều kiện | Ngưỡng cụ thể | Hành động ngay |
|---|---|---|
| Tỷ lệ TC bị Blocked | > {30}% tổng TC cần chạy | Dừng test, báo QC Lead, họp triage |
| Môi trường không khả dụng | > {4} giờ liên tục trong 1 ngày | Dừng, escalate Dev/Ops, ghi log |
| Bug S1 liên quan đến môi trường (không phải app bug) | Xuất hiện ≥ 1 | Dừng luồng liên quan, xác nhận root cause |
| Dữ liệu test bị corrupt / sai | Phát hiện > {10}% data sai | Dừng, rebuild data, xác nhận lại |
| Pass rate sụt giảm đột ngột | < {60}% trong 1 ngày sau khi đã đạt {80}% | Dừng, phân tích nguyên nhân, họp khẩn |

**Điều kiện tiếp tục:**
- Môi trường ổn định và được Dev/Ops xác nhận
- Bug S1 blocking đã fix và deploy lên staging
- Test data đã rebuild và verify
- QC Lead cho phép tiếp tục bằng văn bản (Slack / email)

---

## Mục 6 — Kế Hoạch & Lịch Trình (Testing Tasks & Schedule)

> ⚠️ Quy tắc bắt buộc: KHÔNG chia đều giờ giữa các ticket. Phải ước tính dựa trên độ phức tạp thực tế.

**Bảng estimate theo ticket:**

Hướng dẫn estimate:
- Ticket **Thấp** (CRUD đơn giản, 1 luồng): 2–4h viết TC + 2–3h test
- Ticket **Trung bình** (logic nghiệp vụ, nhiều trường hợp): 4–8h viết TC + 4–6h test
- Ticket **Cao** (tích hợp, trạng thái phức tạp, security/payment): 8–16h viết TC + 8–12h test

| # | Nhiệm vụ | Ticket liên quan | Độ phức tạp | Người thực hiện | Thời gian (ngày) | Effort (giờ) | Ghi chú |
|---|---|---|---|---|---|---:|---|
| 1 | Viết TC Functional + SIT | {Ticket A, B} | Cao | {QC} | {Ngày N–M} | {N}h | {Lý do estimate cao: có integration + payment flow} |
| 2 | Viết TC Functional | {Ticket C} | Thấp | {QC} | {Ngày N} | {N}h | {CRUD đơn giản} |
| 3 | Chuẩn bị Test Data | Tất cả | Trung bình | {QC} | {Ngày N} | {N}h | |
| 4 | Thực hiện Smoke Test | Tất cả | — | {QC} | {Ngày N} | {N}h | |
| 5 | Thực hiện Functional Test | {Ticket A, B, C} | — | {QC} | {Ngày N–M} | {N}h | |
| 6 | Regression Test | Core flows | — | {QC} | {Ngày N} | {N}h | |
| 7 | Retest sau fix | Theo bug report | — | {QC} | {Ngày N–M} | {N}h | Linh hoạt theo fix cycle |
| 8 | Tổng hợp Sprint Report | — | — | QC Lead | {Ngày N} | {N}h | |
| **Tổng** | | | | | | **{N}h** | |

---

## Mục 7 — Nhân Lực & Môi Trường (Resources & Environment)

Bao gồm:

**Nhân lực:**

| Vai trò | Tên | Trách nhiệm chính | % Thời gian |
|---|---|---|---:|
| QC Lead | {Tên} | Review TC, báo cáo, escalate blocker, quyết định suspend | {N}% |
| QC Engineer | {Tên} | Viết TC, thực hiện test, log bug | {N}% |

**Môi trường:**

| Môi trường | URL | Trạng thái | Auth | Ghi chú |
|---|---|---|---|---|
| Staging | {URL} | {Sẵn sàng / Chờ deploy} | {Basic auth / OAuth} | Deploy mới nhất: {ngày} |
| UAT | {URL} | {Sẵn sàng / Chưa có} | | |

**Tài khoản test cần chuẩn bị:**

| Loại tài khoản | Username | Role / Quyền |
|---|---|---|
| Admin | {email} | Toàn quyền |
| User thường | {email} | Quyền cơ bản |
| User không có quyền X | {email} | Kiểm tra negative case |

---

## Mục 8 — Rủi Ro & Phương Án Dự Phòng (Risks & Contingencies)

Liệt kê tối thiểu 4 rủi ro thực tế, không để chung chung:

| # | Rủi ro | Khả năng | Tác động | Phương án dự phòng |
|---|---|---|---|---|
| R1 | Môi trường staging không ổn định cuối sprint | Cao | Chặn {N}% TC chưa chạy | Backup môi trường / coordinate với Ops từ ngày {N} |
| R2 | Requirement thay đổi giữa sprint (scope creep) | Trung bình | Phải viết lại {N}% TC | Review ticket daily, checkpoint TC review ngày {N} |
| R3 | Thiếu test data do môi trường mới | Trung bình | Block toàn bộ functional test | Chuẩn bị data script từ ngày {1} sprint |
| R4 | Bug S1 phát sinh cuối sprint sát deadline | Trung bình | Risk release | Escalation path rõ ràng, exit criteria đã định nghĩa |
| R5 | QC member nghỉ đột xuất | Thấp | Delay {N} ngày | Cross-training, TC documentation đầy đủ để người khác pick up |

---

## Mục 9 — Tài Liệu Đầu Ra (Deliverables)

| Tài liệu | Skill sinh | Định dạng | Người tạo | Thời hạn | Nơi lưu |
|---|---|---|---|---|---|
| Test Plan | skill 01 | `Test_Plan_{code}_{sprint}_v{ver}_{date}.md` | QC Lead | Ngày {N} sprint | `testing-output/test-plan/` |
| High-Level Test Design | skill 01.2 | `hltc-{module}-{sprint}-v{ver}-{date}.md` | QC Lead | Ngày {N} sprint | `testing-output/test-cases/hltc/` |
| Test Case Functional | skill 02 | `tc-functional-{module}-{sprint}-v{ver}-{date}.tsv` | QC | Ngày {N} sprint | `testing-output/test-cases/functional/` |
| Test Case SIT | skill 03 | `tc-sit-{module}-{sprint}-v{ver}-{date}.tsv` | QC | Ngày {N} sprint | `testing-output/test-cases/sit/` |
| Test Data (master dataset + teardown) | **skill 05** | `master-dataset-{sprint}-v{ver}-{date}.tsv` + `.sql` | QC | Trước ngày test | `testing-output/test-data/` |
| Automation Script | **skill 06** | Robot Framework `.robot` | QC | Ngày {N} sprint | `testing-output/automation/` |
| Daily Test Report | skill 07/08 | Markdown + HTML | QC | Mỗi ngày test | `testing-output/reports/daily/` |
| Sprint Summary Report | skill 08 | `.md` + `.docx` | QC Lead | Ngày cuối sprint | `testing-output/reports/sprint/` |
| Bug Report | — | Jira ticket | QC | Trong ngày phát hiện | Jira project {X} |

> **Lưu ý bắt buộc:**
> - Sau khi có danh sách TC → chạy **skill 05** ngay để sinh test data, không để sát ngày test mới làm
> - TC nào có thể automation → chạy **skill 06** để gen script, giảm effort manual
> - Mọi file output phải có `v{semver}` + `{yyyy-mm-dd}_{HHmm}` trong tên file (xem `references/project-folder-convention.md`)
