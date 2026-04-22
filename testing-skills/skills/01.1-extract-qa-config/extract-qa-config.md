---
name: 01.1-extract-qa-config
version: 1.0.0
preamble-tier: 2
description: >
  Trích xuất cấu hình QA từ Test Plan (docx/markdown) thành file machine-readable
  qa-config.yaml để dùng cho automation và các skill downstream.
  Trigger: extract config, qa config, test config, parse test plan.
  Output: qa-config.yaml
---



## Quy tắc lưu output

- Nếu skill cần ghi output ra file/thư mục, phải tự động tạo đầy đủ thư mục đích nếu chưa tồn tại trước khi lưu file.

## Quy tắc ngôn ngữ

- Nội dung mô tả, test artifact và output do skill tạo ra phải viết bằng tiếng Việt có dấu.
- Chỉ giữ nguyên không dấu cho mã kỹ thuật, ID, endpoint, field name, status code, keyword hệ thống và tên file.

## Đầu vào

| Thông tin | Bắt buộc |
|---|---|
| Test Plan (docx hoặc markdown) | ✅ |

## Quy tắc

- Không suy đoán nếu thiếu dữ liệu → trả NEEDS_CONTEXT, liệt kê rõ field nào cần bổ sung
- Chỉ extract thông tin có trong test plan — không tự thêm giá trị không có căn cứ
- Chuẩn hóa về YAML structure theo schema tại `../../references/qa-config-schema.yaml`
- Field tùy chọn (optional): để `~` nếu không có trong test plan

## Output — qa-config.yaml

Schema đầy đủ đặt tại: `../../references/qa-config-schema.yaml`

Khi sinh output:
1. Bám đúng key order và naming theo schema reference.
2. Không thêm key ngoài schema nếu Test Plan không đề cập.
3. Field optional để `~`.
4. Không để placeholder `<...>` trong output cuối.

## Field bắt buộc tối thiểu

Nếu thiếu các nhóm sau thì trả `NEEDS_CONTEXT`:
- `project` (name, code, sprint, domain, architecture)
- `environments.staging` (url, auth_required)
- `tools` (ít nhất: test_management, bug_tracker, automation.ui/api)
- `scope` (type, modules)
- `exit_criteria` (pass_rate, health_score_baseline, max_s1_open, max_s2_open, tc_executed_rate)

## Field khuyến nghị nên extract nếu Test Plan có mô tả

- `output_paths` (để thống nhất nơi lưu artifact)
- `artifact_traceability` (để enforce version/timestamp/result manifest cho từng lần tạo/chạy)

## Trình tự thực hiện gợi ý

1. Đọc Test Plan và map thông tin vào schema key tương ứng.
2. Đánh dấu các field optional chưa có dữ liệu bằng `~`.
3. Validate nhanh: không thiếu field bắt buộc, không còn placeholder.
4. Xuất file `qa-config.yaml`.

## Completion Status

- **DONE** — qa-config.yaml đầy đủ tất cả field bắt buộc, không còn placeholder `<...>`
- **DONE_WITH_CONCERNS** — Hoàn thành nhưng: {Một số field optional để `~` do Test Plan không đề cập / Assumptions đã ghi chú trong file config}
- **BLOCKED** — Không thể extract do: {Test Plan chưa có / File không đọc được / Thiếu thông tin cốt lõi về project}
- **NEEDS_CONTEXT** — Cần bổ sung từ Test Plan hoặc hỏi người dùng: {Liệt kê field còn thiếu}

