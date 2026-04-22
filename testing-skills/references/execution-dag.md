# Execution DAG

## Thứ tự thực hiện trong sprint

Alias quy trình (de doc theo STLC):
- 2.1 = 01.2 (HLTC optional)
- 2.2 = 02 (Functional TC chi tiet)
- 2.3 = 03 (SIT TC chi tiet)

```
Giai đoạn Planning
  01 (Test Plan) ──► 01.1 (Extract Config) ──► qa-config.yaml
                                     └──► 01.2 (High-Level Test Design, optional)

Giai đoạn Thiết kế & Chuẩn bị                      Artifact cần trước
  02 (Gen TC Functional) ──────────────────────────  Ticket / AC / BR (+ HLTC outline từ 01.2 nếu có)
  03 (Gen TC SIT) ─────────────────────────────────  API spec / sequence diagram
  04 (Review TC) ──────────────────────────────────  Output của 02 / 03, hoặc TC có sẵn
  05 (Gen Data Test) ──────────────────────────────  Output của 02 / 03
  06 (Gen Script Test) ────────────────────────────  Output của 02 / 03 + 05 (khuyến nghị)

Giai đoạn Thực thi
  07 (Check Result) ───────────────────────────────  Kết quả pass/fail từ test run
  07 ──► 07 ──► … (chạy lặp mỗi ngày, tích lũy Sprint Snapshot)

Giai đoạn Báo cáo
  08 (Sprint Report) ──────────────────────────────  Sprint Snapshot từ 07

Specialist testing — chạy song song khi cần, không phụ thuộc thứ tự lẫn nhau
  09 (Demo Prep) ──────────────────────────────────  Danh sách ticket DONE
  10 (Security) ───────────────────────────────────  URL staging + tài khoản test
  11 (Performance) ────────────────────────────────  URL staging + API list
  15 (Accessibility) ──────────────────────────────  URL staging + danh sách trang

Giai đoạn Release
  12 (UAT Support) ────────────────────────────────  TC + môi trường UAT
  13 (Go/No-Go) ───────────────────────────────────  Output 07/08 + kết quả 10/11/12/15 (nếu có)
  14 (Smoke Production) ───────────────────────────  Deploy xong + approval từ 13
```

## Bắt đầu giữa sprint (không có artifact từ bước trước)

- Có TC sẵn -> bỏ qua 01/01.1/02/03, vào thẳng 04 (review) hoặc 05 (data)
- Có function logic phức tạp nhưng chưa cần TC chi tiết -> chạy 2.1 (01.2) trước, sau đó mới vào 2.2 (02) và 2.3 (03)
- Không có `qa-config.yaml` -> skill vẫn chạy được; dùng thông tin user cung cấp trực tiếp (xem Nhóm B)
- Không có Sprint Snapshot -> 08 chấp nhận fallback: kết quả TC + danh sách bug tổng hợp thủ công
