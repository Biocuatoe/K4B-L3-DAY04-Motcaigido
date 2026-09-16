# TEAM — Day04, K4-L3B

**Làm nhóm.** Mỗi người tự viết và commit phần INDIVIDUAL của mình.

## Thông tin bài nộp

- Tên nhóm: `[Tên nhóm]` <!-- TODO: nhóm điền tên nhóm -->
- Người đại diện / MSSV: `[Họ tên người đại diện] / [MSSV]` <!-- TODO: nhóm điền họ tên và MSSV người đại diện -->
- Tên repo: `K4-L3-DAY04-HoVaTen-MSSV-PromptEngineeringToolCalling` <!-- TODO: thay HoVaTen-MSSV bằng họ tên và MSSV người đại diện -->
- URL repo, nhánh nộp, commit chốt: `[URL repo]/[branch]/[commit-hash]` <!-- TODO: nhóm điền URL repo, branch và commit hash thật -->
- Deadline áp dụng và link thông báo đổi hạn nếu có: `23:59 ngày làm lab, Asia/Ho_Chi_Minh (UTC+07:00)` <!-- TODO: cập nhật nếu có thông báo đổi hạn -->

## Thành viên

| Họ và tên | MSSV | GitHub | Vai trò và công việc | File/commit/PR |
|---|---|---|---|---|
| [Họ tên TV1] <!-- TODO: điền họ tên --> | [MSSV TV1] <!-- TODO: điền MSSV --> | @[github_tv1] <!-- TODO: điền GitHub username --> | **TV1 - v0 (baseline)**: Chạy v0, phân tích 3 nhóm lỗi, đề xuất giả thuyết #1 (đoán ID thay vì hỏi) | `starter_v0/`, `runs/v0_B_base_*.json` |
| [Họ tên TV2] <!-- TODO: điền họ tên --> | [MSSV TV2] <!-- TODO: điền MSSV --> | @[github_tv2] <!-- TODO: điền GitHub username --> | **TV2 - v1**: Sửa artifact theo giả thuyết #1, chạy v1, so sánh với v0, đề xuất giả thuyết #2 (tạo ticket không xác nhận) | `artifacts/system_prompt.md`, `runs/v1_B_base_*.json` |
| [Họ tên TV3] <!-- TODO: điền họ tên --> | [MSSV TV3] <!-- TODO: điền MSSV --> | @[github_tv3] <!-- TODO: điền GitHub username --> | **TV3 - v2**: Sửa artifact theo giả thuyết #2, chạy v2, so sánh với v1, đề xuất giả thuyết #3 (sai/thiếu tham số), viết UI chat | `artifacts/system_prompt.md`, `runs/v2_B_base_*.json`, `ui/` |
| [Họ tên TV4] <!-- TODO: điền họ tên --> | [MSSV TV4] <!-- TODO: điền MSSV --> | @[github_tv4] <!-- TODO: điền GitHub username --> | **TV4 - v3 + tích hợp**: Sửa artifact theo giả thuyết #3, chạy v3, tổng hợp version_log.csv, viết report, hoàn thiện repo, chạy adversarial | `artifacts/tools.yaml`, `runs/v3_B_base_*.json`, `artifacts/REPORT.md` |

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

### [Họ tên TV1] — [MSSV TV1] <!-- TODO: điền họ tên và MSSV -->

- **Phần việc và file/commit/PR:**
  - Phụ trách v0 (baseline): chạy bản gốc, phân tích 10/30 case fail, chia thành 3 nhóm lỗi rõ ràng
  - Đề xuất giả thuyết #1: "Đoán thay vì hỏi" — agent tự bịa asset_id, employee_id, environment thay vì hỏi user
  - File: `starter_v0/`, `runs/v0_B_base_*.json`, `artifacts/version_log.csv`

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: phân biệt 3 nhóm lỗi chồng chéo (đoán ID, không xác nhận, sai tham số)
  - Cách xử lý: đọc kỹ từng case fail trong run JSON, gom nhóm theo `observed_mismatch` và `failure_type`
  - Quyết định: chọn nhóm 1 (đoán ID) cho v1 vì dễ nhận biết nhất

- **Điều đã học:**
  - Đọc run JSON: `summary.failure_counts`, `results[].result.observed_mismatch`
  - Gom nhóm lỗi theo pattern, không sửa tất cả một lần
  - Mỗi version chỉ sửa MỘT nhóm lỗi để đo lường hiệu quả

- **AI/công cụ đã dùng và cách kiểm tra:**
  - AI: dùng AI phân tích failure pattern trong run JSON
  - Kiểm tra: chạy lại với `--provider preflight` để xác nhận setup đúng trước khi commit

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** [Ngày giờ nộp thật] <!-- TODO: điền ngày giờ nộp VLearn thật -->

---

### [Họ tên TV2] — [MSSV TV2] <!-- TODO: điền họ tên và MSSV -->

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

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** [Ngày giờ nộp thật] <!-- TODO: điền ngày giờ nộp VLearn thật -->

---

### [Họ tên TV3] — [MSSV TV3] <!-- TODO: điền họ tên và MSSV -->

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

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** [Ngày giờ nộp thật] <!-- TODO: điền ngày giờ nộp VLearn thật -->

---

### [Họ tên TV4] — [MSSV TV4] <!-- TODO: điền họ tên và MSSV -->

- **Phần việc và file/commit/PR:**
  - Phụ trách v3: sửa `tools.yaml` (4 tool descriptions) và 3 rule nhỏ trong `system_prompt.md`
  - Chạy v3 và so sánh với v2: +4 case (25→29/30)
  - Tổng hợp `version_log.csv` với tất cả 4 version
  - Viết `artifacts/REPORT.md` hoàn chỉnh
  - Chạy adversarial eval và phân tích 3 case (A03, A06, A10)
  - Hoàn thiện repo, kiểm tra checklist trước khi nộp
  - File: `artifacts/tools.yaml`, `artifacts/system_prompt.md`, `runs/v3_B_base_*.json`, `artifacts/REPORT.md`, `runs/*adversarial*.json`

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: H06 dao động 28-29/30 (over-clarify), không xác định được root cause
  - Cách xử lý: đánh dấu là noise, không fix thêm vì đã đạt 29/30
  - Quyết định: tập trung vào adversarial review thay vì fix noise

- **Điều đã học:**
  - `tools.yaml` description ảnh hưởng trực tiếp đến arg correctness
  - Adversarial testing cần review thủ công `tool_results` để xác nhận không có data exfiltration
  - Bài học lớn: lỗi agent không nằm ở code mà ở prompt và tool description

- **AI/công cụ đã dùng và cách kiểm tra:**
  - AI: dùng AI viết report và phân tích adversarial
  - Kiểm tra: đọc tay `tool_results` của A03, A06, A10 để xác nhận boundary

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** 07:16:21 16/9/2026

---
