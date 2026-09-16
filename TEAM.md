# TEAM — Day04, K4-L3B

**Làm nhóm.** Mỗi người tự viết và commit phần INDIVIDUAL của mình.

## Thông tin bài nộp

- Tên nhóm: Một cái gì đó
- Người đại diện / MSSV: Lê Đức Hùng — 2A202602849
- Tên repo: K4B-L3-DAY04-Motcaigido
- URL repo, nhánh nộp, commit chốt: [URL repo](https://github.com/Biocuatoe/K4B-L3-DAY04-Motcaigido)

## Thành viên

| Họ và tên | MSSV | GitHub | Vai trò và công việc | File/commit/PR |
|---|---|---|---|---|
| Nguyễn Huy Hoàng | 2A202602738 | [@Hoang-H-Nguyen](https://github.com/Hoang-H-Nguyen) | **TV1 - v0 (baseline)**: Chạy v0, phân tích 3 nhóm lỗi, đề xuất giả thuyết #1 (đoán ID thay vì hỏi) | `starter_v0/`, `runs/v0_B_base_*.json` |
| Nguyễn Văn Thăng | 2A202602835 | [@nguyenthang23092005](https://github.com/nguyenthang23092005) | **TV2 - v1**: Sửa artifact theo giả thuyết #1, chạy v1, so sánh với v0, đề xuất giả thuyết #2 (tạo ticket không xác nhận) | `artifacts/system_prompt.md`, `runs/v1_B_base_*.json` |
| Lê Đức Hùng | 2A202602849 | [@Biocuatoe](https://github.com/Biocuatoe) | **TV3 - v2**: Sửa artifact theo giả thuyết #2, chạy v2, so sánh với v1, đề xuất giả thuyết #3 (sai/thiếu tham số), viết UI chat | `artifacts/system_prompt.md`, `runs/v2_B_base_*.json`, `ui/` |
| Nguyễn Hà Khuê | 2A202602938 | [@khuengha](https://github.com/khuengha) | **TV4 - v3 + tích hợp**: Sửa artifact theo giả thuyết #3, chạy v3, chạy lại toàn bộ v0→v3 để tổng hợp version_log.csv, viết report, xây webapp + UI (`webapp.py`, `webapp/`), hoàn thiện repo, chạy adversarial | `artifacts/tools.yaml`, `runs/v3_B_base_*.json`, `artifacts/REPORT.md`, `artifacts/version_log.csv`, `webapp.py`, `webapp/` |

## Nhận xét chung

- **Kết quả và bằng chứng:**
  - v0 (baseline): **20/30** (case_accuracy = 0.6667) — điểm khởi đầu
  - v1: **23/30** (case_accuracy = 0.7667) — cải thiện +3 case sau khi thêm quy tắc "hỏi trước khi đoán ID"
  - v2: **25/30** (case_accuracy = 0.8333) — cải thiện +2 case sau khi thêm ranh giới xác nhận cho write action
  - v3: **29/30** (case_accuracy = 0.9667) — cải thiện +4 case sau khi làm rõ mô tả tool và tham số
  - **Tổng cộng: cải thiện 9/30 case (30%)** từ v0 → v3

- **Thay đổi hiệu quả nhất:**
  - Nhóm 3 (sai/thiếu tham số): sửa 4 tool description trong `tools.yaml` giúp cải thiện nhiều nhất (+4 case)
  - Nhóm 1 (đoán ID thay vì hỏi): thêm rule "Never guess" trong `system_prompt.md` giúp 3 case `missing_info` đều pass

- **Giới hạn còn lại:**
  - H06 fail do over-clarify (dao động 28-29/30)
  - Một số adversarial case cần review thủ công để xác nhận không có data exfiltration

- **Cách phân công và tích hợp:**
  - TV1 phân tích v0, xác định 3 nhóm lỗi rõ ràng
  - TV2-Tv3-Tv4 mỗi người phụ trách 1 nhóm lỗi và 1 version
  - TV4 tích hợp tất cả và viết report cuối cùng
  - Quy trình: phân tích → hypothesis → sửa artifact → chạy → so sánh → repeat

## INDIVIDUAL

Sao chép mục này cho từng thành viên.


---

### Lê Đức Hùng — 2A202602849

- **Phần việc và file/commit/PR:**
  - Phụ trách v2: sửa `system_prompt.md` theo giả thuyết #2, thêm mục Write actions and confirmation
  - Chạy v2 và so sánh với v1: +2 case (23→25/30), `multiturn_accuracy` lên 1.0
  - Đề xuất giả thuyết #3: "Sai/thiếu tham số" — tool gọi đúng nhưng tham số không đầy đủ
  - Phụ trách UI chat: tạo giao diện hiện tool call, input, kết quả/lỗi, version
  - File: `artifacts/system_prompt.md`, `runs/v2_B_base_*.json`, `ui/`

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: H12 vẫn fail (agent hỏi bổ sung summary thay vì hỏi yes/no)
  - Cách xử lý: chuyển H12 sang giả thuyết #3 cho v3 xử lý
  - Quyết định: tập trung vào confirmation boundary, không sửa H12 ngay

- **Điều đã học:**
  - Write action (create_ticket) cần confirmation riêng cho từng payload
  - Confirmation cũ mất hiệu lực khi payload thay đổi
  - UI cần hiện đủ: tool name, args, result/error, version để debug

- **AI/công cụ đã dùng và cách kiểm tra:**
  - AI: dùng AI viết UI với streaming response
  - Kiểm tra: chạy thử với nhiều loại query (bình thường, thiếu thông tin, multi-turn)

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** 07:16:21 16/9/2026

###  Nguyễn Huy Hoàng — 2A202602738

- **Phần việc và file/commit/PR:**
  - Chạy baseline v0 trên eval_base.json, phân tích toàn bộ 10 case fail và phân nhóm thành 3 loại lỗi (wrong_tool, missing_info, wrong_boundary); đề xuất giả thuyết cho nhóm missing_info.
- **Quyết định, khó khăn và cách xử lý:**
  - Khó nhất là phân biệt ranh giới giữa wrong_tool (H04, H13, H17 — tool đúng nhưng args sai) và missing_info (H10, H11, H19 — tool sai do thiếu thông tin gốc). Xử lý bằng cách xét gốc rễ: nếu người dùng đã cung cấp đủ thông tin nhưng model trích xuất sai → wrong_tool; nếu người dùng chưa cung cấp thông tin hợp lệ mà model vẫn hành động → missing_info.
- **Điều đã học:**
  - Baseline không fail ngẫu nhiên — 3 case missing_info có cùng một khuôn mẫu hành vi (đoán giá trị thay vì hỏi), cho thấy đây là lỗ hổng rule trong system prompt chứ không phải nhiễu của model. Việc phân nhóm lỗi rõ ràng trước khi sửa giúp định hướng đúng rule cần bổ sung, tránh sửa lan man.
- **AI/công cụ đã dùng và cách kiểm tra:**
  - OpenAI/OpenRouter API, dùng Claude để hỗ trợ đọc và đối chiếu chi tiết của từng case fail trong file kết quả v0: Kiểm tra chéo bằng summary.failure_counts (wrong_tool: 4, missing_info: 3, wrong_boundary: 3), case_failure_type/observed_mismatch xem có khớp không để đảm bảo không bỏ sót case nào.
- **Thời điểm đã tự nộp URL repo chung trên VLearn:** 10:00AM 16th September 2026


### Nguyễn Văn Thăng — 2A202602835

- **Phần việc và file/commit/PR:**
  - Phụ trách v1: sửa `system_prompt.md` theo giả thuyết #1, thêm quy tắc "Never guess" và mục Missing information
  - Chạy v1 và so sánh với v0: +3 case (20→23/30)
  - Đề xuất giả thuyết #2: "Tạo ticket không xác nhận" — agent gọi `create_ticket` luôn mà không hỏi xác nhận
  - File: `artifacts/system_prompt.md`, `runs/v1_B_base_*.json`

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: thêm rule mà không làm agent quá cứng nhắc, vẫn trả lời được câu hỏi thường gặp
  - Cách xử lý: thêm ví dụ cụ thể về khi nào phải clarify
  - Quyết định: dùng `clarify` kiểu `choice` cho environment (demo/QA/dev → production/staging)

- **Điều đã học:**
  - Sửa `system_prompt.md` thay đổi hành vi agent ngay mà không cần sửa code
  - Mỗi rule mới cần cân bằng giữa ràng buộc và khả năng trả lời linh hoạt
  - Version log giúp track từng thay đổi và so sánh công bằng

- **AI/công cụ đã dùng và cách kiểm tra:**
  - AI: dùng AI để draft rule mới, sau đó tự review lại
  - Kiểm tra: chạy lại bộ 30 case, kiểm tra `case_accuracy` tăng

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** 11:15AM 16th September 2026 

---

### Nguyễn Hà Khuê — 2A202602938

- **Phần việc và file/commit/PR:**
  - Phụ trách v3: sửa description 4 tool trong `tools.yaml` theo giả thuyết #3 (inspect_device bắt buộc set check, search_kb bắt buộc set category, lookup_user lấy asset ID từ kết quả tra cứu, create_ticket giữ ranh giới xác nhận) và bổ sung 3 rule nhỏ trong `system_prompt.md`
  - Chạy v3 và so sánh với v2: +4 case (25→29/30), case_accuracy 0.9667
  - Xây webapp + UI: `webapp.py` (server HTTP chạy tool loop, chọn provider openrouter/openai/anthropic/gemini, chọn version v0–v3, lưu transcript) và giao diện trong `webapp/`
  - Chạy lại toàn bộ v0, v1, v2, v3 trên cùng bộ base_30 để viết `version_log.csv` (prompt_hash/tools_hash, lý do thay đổi, metric trước/sau, run file) và đối chiếu số liệu khi viết `REPORT.md`
  - File: `artifacts/tools.yaml`, `artifacts/system_prompt.md`, `artifacts/version_log.csv`, `artifacts/REPORT.md`, `runs/v3_B_base_*.json`, `webapp.py`, `webapp/`

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: H06 vẫn fail do agent over-clarify, kết quả dao động 28-29/30 giữa các lần chạy
  - Cách xử lý: thêm rule "request đã đủ thông tin thì không hỏi lại summary" vào v3, không cộng thêm rule nữa để tránh over-clarify nặng hơn
  - Quyết định: v3 sửa `tools.yaml` (đổi tools_hash) vì 4 case wrong_arg_value đều do mô tả tham số mơ hồ chứ không chỉ do prompt

- **Điều đã học:**
  - Phải chạy lại toàn bộ các version trên cùng một bộ case thì so sánh giữa các version mới công bằng
  - prompt_hash/tools_hash trong version log là bằng chứng cho thấy artifact nào thực sự đổi ở từng version
  - Webapp/UI cho chọn version và provider giúp demo và so sánh hành vi agent trực tiếp giữa các version

- **AI/công cụ đã dùng và cách kiểm tra:**
  - AI: dùng AI hỗ trợ viết webapp (HTTP server + giao diện) và rà soát report
  - Kiểm tra: chạy lại eval cả 4 version, đối chiếu run file trong `runs/` với số liệu ghi trong `version_log.csv` và `REPORT.md`; chạy thử webapp với từng version v0–v3, xem transcript lưu trong `transcripts/`

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** 11:00AM 16th September 2026

---
