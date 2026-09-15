# TEAM — Day04, K4-L3B

**Làm nhóm.** Mỗi người tự viết và commit phần INDIVIDUAL của mình.

## Thông tin bài nộp

- Tên nhóm: Motcaigido
- Người đại diện / MSSV: Lê Đức Hùng - 2A202602849
- Tên repo: `K4B-L3-DAY04-Motcaigido`
- URL repo, nhánh nộp, commit chốt:
  - URL: `https://github.com/Biocuatoe/K4B-L3-DAY04-Motcaigido`
  - Nhánh: `main`
  - Commit chốt: `[điền hash commit cuối cùng]`
- Deadline áp dụng và link thông báo đổi hạn nếu có: 12:00 2026-09-17 (theo SUBMISSION.md)

## Thành viên

| STT | Họ và tên | MSSV | GitHub | Vai trò & Phụ trách chính (Hợp tác v1/v2/v3) | File/commit chính |
|---|---|---|---|---|---|
| 1 | Lê Đức Hùng | 2A202602849 | `Biocuatoe` | **Lead & Prompt.** Tổng hợp, chốt repo, viết `TEAM.md`, `version_log`. Cùng nhóm iterate prompt qua v1/v2/v3 (tập trung fix `missing_info` & cấu trúc tổng thể). | `TEAM.md`, `version_log.csv`, `artifacts/system_prompt.md`, `artifacts/tools.yaml` |
| 2 | Nguyễn Huy Hoàng | 2A202602738 | `Hoang-H-Nguyen` | **Report & Prompt.** Viết `REPORT.md`, phân tích failure & safety review. Cùng nhóm iterate prompt qua v1/v2/v3 (tập trung fix `wrong_boundary` & argument accuracy). | `artifacts/REPORT.md`, `runs/` (analysis), `artifacts/system_prompt.md`, `artifacts/tools.yaml` |
| 3 | Nguyễn Hà Khuê | 2A202602938 | `khuengha` | **Eval & Prompt.** Viết 10 group cases, chạy adversarial. Cùng nhóm iterate prompt qua v1/v2/v3 (tập trung fix `routing`, `environment_arg` & parallel tools). | `data/eval_group.json`, `data/eval_adversarial.json/`, `artifacts/system_prompt.md`, `artifacts/tools.yaml` |
| 4 | Nguyễn Văn Thăng | 2A202602835 | `nguyenthang23092005` | **Prompt & Tooling.** Prompt logic (multi-turn & boundaries), phát triển UI/Runner để test prompt trực quan. | `artifacts/system_prompt.md`, `artifacts/tools.yaml` |


## Nhận xét chung

- **Kết quả và bằng chứng:**
  - `case_accuracy` tăng trưởng ổn định qua các phiên bản: **v0 (0.6667) → v1 (0.7667) → v2 (0.8333) → v3 (0.9667)**.
  - `multiturn_accuracy` đạt **1.0** từ v2; `tool_routing_accuracy` đạt **0.9667** ở v3.
  - Bằng chứng: Các file run JSON trong thư mục `runs/` (v0 đến v3) và `version_log.csv`.

- **Thay đổi hiệu quả nhất (Hợp tác nhóm):**
  - Cả nhóm cùng thảo luận và iterate prompt qua 3 vòng. 
  - **v1**: Tập trung xử lý `missing_info` (thêm rule bắt buộc `clarify` khi thiếu asset/employee ID).
  - **v2**: Tập trung xử lý `wrong_boundary` (thêm rule xác nhận `yes_no` trước mọi write action như `create_ticket`).
  - **v3**: Tập trung xử lý `wrong_arg_value` và `routing` (phân biệt rõ environment đã biết vs mơ hồ, cấm tự động `inspect_device` khi chỉ có `employee_id`, tối ưu parallel tools).

- **Giới hạn còn lại:**
  - Case **H06** ở v3 vẫn fail (over-clarify): user nói rõ "Email staging" nhưng agent vẫn gọi `clarify(choice)`. Nguyên nhân do prompt chưa có đủ few-shot examples cho trường hợp environment tường minh.

- **Cách phân công và tích hợp:**
  - Thay vì chia mỗi người một version, cả 4 thành viên cùng tham gia brainstorming và tinh chỉnh `system_prompt.md` qua các vòng v1, v2, v3. 
  - Hùng điều phối và tổng hợp version log; Hoàng tổng hợp kết quả vào REPORT.md; Khuê liên tục test prompt mới với bộ eval cases và adversarial; Thăng tích hợp vào UI và runner để demo trực quan.

## INDIVIDUAL

### Lê Đức Hùng — 2A202602849

- **Phần việc và file/commit/PR:**
  - Viết `TEAM.md`, `version_log.csv` và điều phối nhóm.
  - Tham gia iterate prompt qua v1, v2, v3 (tập trung vào cấu trúc prompt và xử lý `missing_info`).

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: Làm sao để model không tự đoán `asset_id` khi user nói "laptop của tôi". 
  - Cách xử lý: Đề xuất thêm section "Missing Information Handling" trong prompt, yêu cầu model gọi `clarify` thay vì tự suy diễn.

- **Điều đã học:**
  - Prompt engineering là một quá trình lặp (iterative), cần có version log rõ ràng để track sự thay đổi của metric.

- **AI/công cụ đã dùng và cách kiểm tra:**
  - Sử dụng ChatGPT/Gemini và OpenAI/OpenRouter API làm provider cho các phiên chạy v0–v3; kiểm tra bằng `python run_eval.py --provider openai` / `--provider openrouter` và đối chiếu `case_accuracy` trong `runs/` và `version_log.csv`.

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** `11:55 2026-09-17`

---

### Nguyễn Huy Hoàng — 2A202602738

- **Phần việc và file/commit/PR:**
  - Viết `REPORT.md`, phân tích failure analysis và safety review.
  - Tham gia iterate prompt qua v1, v2, v3 (tập trung vào `wrong_boundary` và argument accuracy).

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: Model thường bỏ qua bước xác nhận và gọi thẳng `create_ticket`.
  - Cách xử lý: Đề xuất thêm rule "Write Actions Boundary" trong prompt, liệt kê rõ `create_ticket` là hành động ghi dữ liệu, bắt buộc phải qua `clarify(yes_no)`.

- **Điều đã học:**
  - Automatic score không nói lên tất cả; cần đọc kỹ transcript và kiểm tra filesystem để đảm bảo không có dữ liệu nhạy cảm bị ghi ra ngoài.

- **AI/công cụ đã dùng và cách kiểm tra:**
  - Sử dụng ChatGPT/Gemini và OpenAI/OpenRouter API để chạy các phiên test; đọc JSON trong `runs/` và `tickets/` để xác minh không có dữ liệu nhạy cảm hay write action không được xác nhận.

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** `11:55 2026-09-17`

---

### Nguyễn Hà Khuê — 2A202602938

- **Phần việc và file/commit/PR:**
  - Chịu trách nhiệm chính về 10 group eval cases (`data/eval_group.json`) và adversarial testing.
  - Tham gia iterate prompt qua v1, v2, v3 (tập trung vào `routing`, `environment_arg` và parallel tools).

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: Model hay map nhầm "demo" sang "staging" hoặc tự động gọi `inspect_device` khi chỉ có `employee_id`.
  - Cách xử lý: Viết thêm các cases adversarial (H19, H04) để phát hiện lỗi, sau đó cùng nhóm thêm rule phân biệt "known environment" vs "ambiguous environment" trong prompt.

- **Điều đã học:**
  - Eval cases chất lượng cao (đặc biệt là multi-turn và adversarial) quan trọng hơn số lượng. Chúng giúp phát hiện các lỗi ngấm ngầm mà single-turn không thấy được.

- **AI/công cụ đã dùng và cách kiểm tra:**
  - Sử dụng ChatGPT/Gemini và OpenAI/OpenRouter API cho các lần chạy eval; kiểm tra bằng JSON schema validator và `python run_eval.py --provider openai --version v3 --suite group` để xác minh đúng 5 single-turn + 5 multi-turn cases.

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** `11:55 2026-09-17`

---

### Nguyễn Văn Thăng — 2A202602835

- **Phần việc và file/commit/PR:**
  - Code UI, tham gia iterate prompt qua v1, v2, v3 (tập trung vào `format_boundary` và multi-turn intent replacement).

- **Quyết định, khó khăn và cách xử lý:**
  - Khó khăn: Model đôi khi gọi lại các tool kiểm tra dữ liệu thiết bị như inspect_device khi người dùng chỉ yêu cầu format báo cáo từ các findings đã có sẵn.
  - Cách xử lý: Đề xuất thêm rule "Format Only Boundary" trong prompt, nhấn mạnh việc tôn trọng yêu cầu "không kiểm tra lại" của user.

- **Điều đã học:**
  - Hiểu rõ hơn cách thiết lập boundary cho tool calling và cách xử lý intent mới trong multi-turn conversation. Việc kết hợp UI/runner với transcript giúp phát hiện lỗi routing và iterate prompt nhanh hơn so với chỉ kiểm tra kết quả JSON.

- **AI/công cụ đã dùng và cách kiểm tra:**
  - Sử dụng ChatGPT/Gemini và OpenAI/OpenRouter API để chạy phiên chat demo; kiểm tra UI/transcript bằng cách đọc file JSON trong `transcripts/` và đối chiếu `tool_calls`, input, output và phiên bản run.

- **Thời điểm đã tự nộp URL repo chung trên VLearn:** `11:55 2026-09-17`