---
name: 01.2-test-design-high-level
version: 1.0.1
preamble-tier: 2
description: >
  Thiết kế test design high-level dạng Markdown outline cho function có logic phức tạp,
  phục vụ review nhanh trước khi viết test case chi tiết. Bước này optional,
  chỉ chạy khi cần. Output bắt buộc: Markdown outline + checklist review gate.

---
**LƯU Ý QUAN TRỌNG:**
> **File HLTC phải lưu theo path output chuẩn `testing-output/test-cases/hltc/` và theo format: `hltc-{module}-{sprint}-v{semver}-{yyyy-mm-dd}_{HHmm}.md`.**

**Checklist trước khi kết thúc:**
- [ ] Đã đọc kỹ hướng dẫn lưu file HLTC
- [ ] Đã xác định đúng thư mục và format tên file
- [ ] Đã thực hiện bước chốt HLTC (review, Approved outline với team, xác nhận coverage)

**Lưu ý quy trình:**
- Nếu đã có HLTC, BẮT BUỘC phải dùng HLTC làm đầu vào cho test case chi tiết (functional TC, SIT TC, v.v).
- Chỉ chuyển sang viết test case chi tiết sau khi HLTC đã được review/chốt với team.
---
---



## Quy tắc lưu output

- Nếu skill cần ghi output ra file/thư mục, phải tự động tạo đầy đủ thư mục đích nếu chưa tồn tại trước khi lưu file.

## Quy tắc ngôn ngữ

- Nội dung HLTC và output do skill tạo ra phải viết bằng tiếng Việt có dấu.
- Chỉ giữ nguyên không dấu cho mã kỹ thuật, ID, endpoint, field name, status code, keyword hệ thống và tên file.

## Quy tắc phê duyệt và đồng bộ trạng thái

- HLTC chỉ được xem là đã chốt khi có xác nhận review và Approved từ user hoặc team trong context hiện tại.
- Khi có xác nhận Approved, phải cập nhật ngay chính file HLTC vừa tạo:
  - tick `[x] Team đã review và chốt "Approved" scope high-level`
  - đổi dòng `Kết quả gate` thành `Approved`
- Không được giữ trạng thái cuối file là `Not Approved` nếu context mới nhất đã xác nhận Approved.
- Nếu chưa có xác nhận Approved, giữ nguyên `Not Approved` và không được ngầm định là đã chốt.
- Mọi bước tiếp theo phụ thuộc HLTC phải dựa trên trạng thái mới nhất đã được ghi trong file, không dựa trên trao đổi miệng/nhận xét rồi bỏ qua việc cập nhật file.

## Mục tiêu

- Tạo bộ high-level testcase dạng Markdown outline để review nhanh với team.
- Chốt phạm vi test trước khi vào Skill 02 viết test case chi tiết.
- Giảm thiểu việc viết TC chi tiết rồi mới phát hiện thiếu nhóm case.

## Khi nào dùng

Dùng Skill 01.2 khi có một trong các dấu hiệu sau:
- Function có nhiều nhánh logic, nhiều rule, nhiều hệ thống phụ thuộc.
- Có nhiều mode xử lý (AI + Rule, fallback, override, precedence).
- Team cần review nhanh scope test trước khi chi tiết hóa.

Không bắt buộc cho mọi yêu cầu.

## Đầu vào

- AC/User Story/BR liên quan function (bắt buộc)
- Test Plan và/hoặc qa-config.yaml (nếu đã có)
- Danh sách function/module cần review (nếu user chỉ định)

Nếu thiếu AC/BR có ý nghĩa -> ghi [Cần bổ sung] và dừng.

## Đầu ra bắt buộc

1. Markdown outline (text-first) bắt buộc phải theo đúng định dạng Markmap/Markdown outline như template
2. Checklist review gate (Approved/Not Approved + action)
3. Mermaid/sơ đồ chỉ là tùy chọn nếu team cần review trực quan (không thay thế outline chính)

Chỉ sử dụng duy nhất định dạng Markdown outline/Markmap cho HLTC. Không dùng bảng chi tiết hoặc format khác.
Template bắt buộc:
- ../../references/test-design-mindmap-template.md

## Quy tắc thiết kế

1. Outline HLTC phải theo đúng 1 format duy nhất (Markdown outline/Markmap) như template:
  - Main topic/Main flow
  - Function/Sub flow
  - UI (Web/App/API)
  - Validation
  - Impact
  - Abnormal case
  (Xem ví dụ chi tiết trong references/test-design-mindmap-template.md)

2. Không sử dụng bảng liệt kê chi tiết, không dùng bảng HLTC cho outline high-level. Nếu cần mapping chi tiết, để riêng ở cuối file.

3. Ưu tiên high-level, không đi quá chi tiết từng test step.

4. Bắt buộc có nội dung tối thiểu trong các nhánh:
- Function/Sub flow: liệt kê AC/Sub flow chính
- UI: nếu có đa kênh thì phải có Web/App/API
- Validation: authorization + business validation quan trọng
- Impact: current functions + data impact/cut-off data
- Abnormal case: timeout + network/system error

5. Nếu có quy tắc ưu tiên rule:
- Mô tả precedence rõ ràng trong outline.
- Nếu có nhiều rule support, ghi rõ tiêu chí chọn rule ưu tiên.

## Review Gate (bắt buộc)

Đánh giá theo checklist sau (bắt buộc dùng chính xác các mục này, không tự thêm/bớt):

- [ ] Đã cover đủ các nhánh Function/Sub flow chính
- [ ] Đã cover UI đúng kênh cần áp dụng (Web/App/API)
- [ ] Đã cover Validation: authorization + business rule quan trọng
- [ ] Đã cover Impact: current functions và data impact
- [ ] Đã cover Abnormal case: timeout và network/system error
- [ ] Đã cover ít nhất 1 negative branch mỗi rule quan trọng
- [ ] Đã xác định rõ branch cần vào Smoke
- [ ] Không có mâu thuẫn rõ ràng với AC/BR
- [ ] Team đã review và chốt "Approved" scope high-level

Kết quả gate: Approved / Not Approved

Quyết định gate:
- Approved: Được chuyển sang Skill 02 để viết TC chi tiết
- Not Approved: Cập nhật lại HLTC outline, review lại trước khi vào Skill 02

Luồng tiếp theo nếu Approved:
- 2.2 (Skill 02): Viết Functional TC chi tiết
- 2.3 (Skill 03): Viết SIT TC chi tiết nếu có tích hợp

Nếu nhận được xác nhận Approved sau khi file đã tạo xong:
- Mở lại file HLTC hiện tại và cập nhật checklist/gate trước
- Lưu lại file HLTC
- Chỉ sau đó mới được thực hiện Skill 02/03

## Lưu file vào

`output_paths.test_cases.hltc` từ qa-config (default: `testing-output/test-cases/hltc/`)
-> `hltc-{module}-{sprint}-v{semver}-{yyyy-mm-dd}_{HHmm}.md`

## Definition of Done

- Có Markdown outline hợp lệ, đọc được, dễ review
- Có checklist review gate (9 mục chuẩn) và dòng kết quả: `Kết quả gate: Approved / Not Approved`
- File lưu đúng thư mục `test-cases/hltc/`, có version và timestamp trong tên file
- Nếu đã có xác nhận Approved thì file phải phản ánh đúng trạng thái cuối cùng: checklist Approved được tick và gate = Approved
- Nếu Not Approved thì không được kết luận sang bước viết TC chi tiết

## Checklist xác nhận lưu file đúng chuẩn (bắt buộc)

- [ ] Đã lưu file vào đúng thư mục `testing-output/test-cases/hltc/` hoặc path override từ qa-config
- [ ] Tên file đúng pattern: `hltc-{module}-{sprint}-v{semver}-{yyyy-mm-dd}_{HHmm}.md`
- [ ] Nội dung file gồm: Markdown outline, checklist review gate 9 mục chuẩn, dòng kết quả Approved/Not Approved
- [ ] Đã kiểm tra lại version, timestamp, module, sprint trong tên file
