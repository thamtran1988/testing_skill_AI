---
name: 06-gen-script-test
version: 1.1.0
preamble-tier: 2
description: >
  Tạo automation test script từ test case: Playwright (JS/TS), Pytest + Requests (Python),
  RestAssured (Java), Cypress, Robot Framework. Trigger: automation script, gen script,
  viết code test, Playwright, Pytest, RestAssured, Cypress, Robot Framework, tự động
  hoá kiểm thử.
  Output: code file chạy được ngay, CI-ready.
---

# Skill 06 — Gen Script Test

## Quy tắc ngôn ngữ

- Nội dung mô tả, test artifact và output do skill tạo ra phải viết bằng tiếng Việt có dấu.
- Chỉ giữ nguyên không dấu cho mã kỹ thuật, ID, endpoint, field name, status code, keyword hệ thống và tên file.

## Tài liệu bắt buộc phải đọc trước khi sinh script

1. `references/ai-execution-spec.md` (bắt buộc)
2. `references/automation-framework-policy.md` (tham chiếu khi cần)

Nếu chưa đọc `ai-execution-spec.md` thì không được bắt đầu sinh code.
Nếu thiếu file tham chiếu bắt buộc trong repo hiện tại thì trả `NEEDS_CONTEXT`, nêu rõ file thiếu và yêu cầu user cung cấp hoặc xác nhận dùng fallback.

## Đầu vào

| Thông tin | Bắt buộc |
|---|---|
| Danh sách TC cần automation (STT hoặc file) | ✅ |
| Tech stack / framework | ✅ nếu chưa có `qa-config.yaml` |
| Base URL / API endpoint | ✅ nếu chưa có `qa-config.yaml` |
| Auth mechanism (Bearer, API Key, Session...) | ✅ nếu chưa có `qa-config.yaml` |
| Data test (từ skill 05 hoặc cung cấp trực tiếp) | Khuyến nghị |

Nếu project có `qa-config.yaml` → đọc các field sau trước, không hỏi lại (ưu tiên hơn input người dùng):

| Field | Dùng cho |
|---|---|
| `tools.automation.ui` | Danh sách framework UI (playwright/cypress/selenium/robotframework) |
| `tools.automation.api` | Danh sách framework API (pytest/restassured/postman-newman/robotframework) |
| `environments.staging.url` | Base URL mặc định |
| `environments.staging.auth_required` | Có cần setup auth không |
| `test_accounts` | Accounts dùng trong beforeAll / fixture |
| `automation_rules.test_level_policy` | Rule mapping test level: component/integration/e2e |

Nếu chưa có config → hỏi user về tech stack và base URL trước khi gen.

Quy tắc fallback khi không có `qa-config.yaml`:
- Dùng thông tin user cung cấp trực tiếp làm nguồn chính.
- Nếu thiếu framework cụ thể, mặc định đề xuất Robot Framework và chờ user xác nhận trước khi sinh script.

Nếu thiếu một trong các mục sau thì trả **NEEDS_CONTEXT** và dừng:
1. Danh sách test case nguồn (Functional hoặc SIT)
2. Module/feature
3. Môi trường mục tiêu
4. Dữ liệu test hoặc quy tắc setup/cleanup
5. Test Level cho từng TC (hoặc policy đủ để suy ra level)

## Quy tắc chọn TC và xử lý ưu tiên

### Quy tắc 1 — Ưu tiên TC có đánh dấu automation

Nếu user không chỉ định cụ thể TC nào cần gen script:
- Ưu tiên nhặt các TC từ file Functional (`testing-output/test-cases/functional/`) **và** SIT (`testing-output/test-cases/sit/`) có cột `Auto=Y` (hoặc tương đương).
- Không tự nhặt TC có `Auto=N` trừ khi user yêu cầu rõ.
- Nếu không có TC nào có `Auto=Y` → báo user và hỏi có muốn gen cho toàn bộ TC không trước khi tiếp tục.

### Quy tắc 2 — Làm theo chỉ định của user

Nếu user chỉ định rõ (danh sách TC theo STT, nhóm rule, module, file cụ thể, hoặc bất kỳ phạm vi nào):
- Làm đúng theo chỉ định đó — không áp đè bằng Quy tắc 1.
- Không tự thêm hoặc bỏ TC ngoài phạm vi user đã chỉ định.
- Nếu TC user chỉ định có `Auto=N`, vẫn gen nhưng ghi chú trong mapping matrix.

### Quy tắc 3 — Hỏi lại khi thiếu thông tin cấu hình

Khi thiếu bất kỳ thông tin bắt buộc nào dưới đây mà `qa-config.yaml` không cung cấp:
- **API endpoint / domain**: hỏi rõ URL base và path cụ thể của module cần test.
- **Tài khoản test**: hỏi username/role cần dùng (không hỏi password — yêu cầu user inject qua env var hoặc secrets).
- **Auth mechanism**: hỏi loại auth (Bearer token, API key, session cookie) và cách lấy token.
- **DB / environment**: hỏi môi trường đích (staging/UAT) nếu chưa rõ.

**Không được tự giả định giá trị cho các thông tin trên.** Liệt kê rõ từng mục còn thiếu và hỏi user trước khi sinh bất kỳ dòng code nào.

## Framework được hỗ trợ

> **Ưu tiên chính: Robot Framework** — dùng unified API + UI automation (keyword-driven, dễ maintain, phù hợp QA)

| Framework | Ngôn ngữ | Phù hợp với | Ghi chú |
|---|---|---|---|
| **Robot Framework** | Python | UI, API, E2E (unified) | ✅ **Ưu tiên — cả API + UI trong 1 framework** |
| Playwright | JS / TS | UI E2E, API | UI-focus, API kém hơn Robot |
| Pytest + Requests | Python | API, Backend | API-only |
| RestAssured | Java | API, Spring Boot | API-only, Java-specific |
| Cypress | JS | UI E2E, Component | UI-only, JavaScript |
| k6 | JS | Performance script độc lập | Skill 11 dùng JMeter mặc định; k6 script gen tự do nhưng không có template tích hợp trong skill 11 |

**Phạm vi mobile native (clarify):**
- Chưa hỗ trợ trực tiếp Appium / XCUITest / Espresso trong skill này.
- Khi yêu cầu automation cho mobile native app, trả `NEEDS_CONTEXT` và nêu rõ đang ngoài phạm vi hiện tại của Skill 06.
- Nếu là mobile web/PWA chạy trên trình duyệt, vẫn dùng Playwright/Cypress/Robot Framework bình thường.

---

## Nguyên tắc sinh code

- **Page Object Model** cho UI test — tách locator ra khỏi test logic
- **Assertion rõ ràng** — không dùng `expect(true).toBe(true)`
- **Data-driven** — đọc data từ file/fixture, không hardcode trong test
- **CI-ready** — chạy được với `npm test` / `pytest` / `mvn test` không cần config thêm
- **Cleanup** — mỗi test tự cleanup data sau khi chạy (`afterEach` / `teardown`)
- **Độc lập** — test không phụ thuộc thứ tự chạy với test khác
- **Mô tả rõ** — `describe` / `it` / `test name` phản ánh đúng kịch bản TC

## Chuẩn AI-first bắt buộc

Trước khi sinh code phải tạo **mapping matrix**.

Khi trả kết quả phải có đúng 3 phần:
1. Mapping matrix (TC nguồn -> script)
2. Danh sách file output theo path chuẩn
3. DoD pass/fail checklist

Không có mapping matrix thì không sinh script.

## Cấu trúc thư mục output

**Lưu vào:** `output_paths.automation` từ qa-config (default: `testing-output/automation/`)

```
testing-output/automation/
├── fixtures/          # test data files (JSON/CSV)
│   └── {feature}.json
├── pages/             # Page Objects (UI — Playwright/Cypress)
│   └── {PageName}.ts
├── specs/             # test files (Playwright/Cypress)
│   └── {feature}.spec.ts
├── robot/             # Robot Framework suites
│   ├── {feature}.robot
│   └── resources/{feature}-keywords.resource
├── tests/api/         # API tests (Pytest)
│   └── test_{feature}.py
└── helpers/           # utils, auth setup
    └── auth.ts
```

Trong project dùng kiến trúc chuẩn Robot Framework, ưu tiên output path sau:

1. `testing-output/automation/Projects/<NhomChucNang>/<Feature>_<Kenh>.robot`
2. `testing-output/automation/KeywordLibraries/<Module>/HighLevelKeywords/<Module>_High.resource`
3. `testing-output/automation/KeywordLibraries/<Module>/LowLevelKeywords/<Module>_Low.resource`
4. `testing-output/automation/KeywordLibraries/<Module>/VerificationKeywords/<Module>_Verification.resource`
5. `testing-output/automation/DataTest/<Module>/<Feature>_data.<csv|json|yaml>`

Quy tắc phân nhóm:
- Dùng thư mục Projects theo nhóm chức năng/nghiệp vụ (Auth, Payment, Order, Search...).
- Phân loại test theo loại (api/ui/sit/smoke/regression) bằng Tags, không dùng thư mục Projects để phân loại.

## Checklist trước khi xuất

- [ ] Code chạy được với lệnh mặc định của framework
- [ ] Có `beforeAll` / `afterEach` setup và teardown
- [ ] Assertion đủ cụ thể (status code, response body, UI element)
- [ ] Không hardcode credential — dùng `process.env` hoặc fixture
- [ ] Comment giải thích bước phức tạp
- [ ] Ghi rõ version dependency trong comment đầu file

## Output format cố định

### 1) Mapping Matrix

| TC nguồn | Loại | Test Level | Tiền điều kiện | Bước nghiệp vụ | Kết quả mong đợi | File test suite | High-level keyword | Low-level keyword | Dữ liệu dùng | Môi trường |
|---|---|---|---|---|---|---|---|---|---|---|

### 2) File Output Plan

- `<path 1>`
- `<path 2>`
- `<path 3>`

### 3) DoD Pass/Fail

- [ ] Đã map đầy đủ TC nguồn sang script
- [ ] Mỗi dòng mapping có Test Level (component/integration/e2e)
- [ ] Đúng output path chuẩn
- [ ] Không hardcode dữ liệu nhạy cảm
- [ ] Test case không gọi trực tiếp low-level/libraries
- [ ] Low-level không assert nghiệp vụ
- [ ] Có tags smoke/regression/module
- [ ] UI không dùng sleep cố định
- [ ] Có data file tách riêng
- [ ] Dùng env config thay URL cố định
- [ ] Smoke path chạy được trên ít nhất 1 môi trường

Trạng thái: `PASS` hoặc `FAIL`

## CI/CD integration guidance (mức cơ bản)

Mục tiêu của output là CI-ready. Nếu project yêu cầu tích hợp cụ thể, thêm 1 file mẫu theo hệ thống CI hiện có:

- **GitHub Actions**: `.github/workflows/test-automation.yml`
- **GitLab CI**: `.gitlab-ci.yml`
- **Jenkins**: `Jenkinsfile`

Checklist tối thiểu cho CI:
1. Cài dependency + cache (npm/pip/maven)
2. Inject biến môi trường (`BASE_URL`, token, secret) qua secret store của CI
3. Chạy test command mặc định theo framework
4. Xuất artifact report (junit/html/log) để truy vết

Nếu user chưa yêu cầu pipeline cụ thể, chỉ ghi mục này như guidance; không tự tạo file CI chi tiết.

## Completion Status

- **DONE** — Script chạy được với lệnh mặc định, có setup/teardown, assertion đủ cụ thể, không hardcode credential
- **DONE_WITH_CONCERNS** — Hoàn thành nhưng: {Một số TC phức tạp chưa automation được / Thiếu data fixture / CI config cần điều chỉnh thêm}
- **BLOCKED** — Không thể gen do: {Thiếu tech stack xác nhận / Không có base URL / Auth mechanism chưa rõ}
- **NEEDS_CONTEXT** — Cần bổ sung: {Danh sách TC cần automation / Framework sử dụng / Base URL và auth mechanism}
