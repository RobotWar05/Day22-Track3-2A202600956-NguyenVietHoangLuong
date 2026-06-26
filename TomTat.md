# Tóm Tắt Dự Án Day 22 - DPO/ORPO Alignment Lab (Track 3)

## 1. Keywords (Từ khóa cần chú ý)
- **DPO (Direct Preference Optimization)** / **ORPO**
- **Alignment** / **Preference Learning**
- **SFT (Supervised Fine-Tuning)**
- **TRL (Transformer Reinforcement Learning)**, **PEFT**, **LoRA**
- **Unsloth**, **bitsandbytes** (Qwen2.5-3B-bnb-4bit / Qwen2.5-7B-bnb-4bit)
- **UltraFeedback**, **VN Alpaca**
- **GGUF**, **llama-cpp-python**
- **Likelihood displacement**, **Alignment-tax**

## 2. Các yêu cầu đề bài & Roadmap thực hiện

**Mục tiêu chính**: Từ SFT-mini checkpoint → train DPO adapter → compare SFT-only vs SFT+DPO → merge + GGUF + serve.
Đầu ra là một DPO-aligned VN model open-source đầu tiên. Có 2 tier để thực hiện: **T4 (default)** và **BigGPU (full)**.

### Roadmap Thực Hiện

| Notebook / Tool | Tên Bước | Mục tiêu | Yêu cầu qua bài (Pass criteria) |
|---|---|---|---|
| `01_sft_mini` | SFT Checkpoint | Train SFT (Unsloth + LoRA r=16, 1k VN Alpaca) | Lưu được adapter; Loss giảm dần (monotonic decrease). |
| `02_preference_data`| Xử lý dữ liệu | Load `ultrafeedback-binarized`, format chuẩn DPO | Ghi file Parquet; `chosen` ≠ `rejected`; In 3 ví dụ kiểm tra. |
| `03_dpo_train` | DPO Training | Train `DPOTrainer(beta=0.1, lr=5e-7)` trên base SFT| Lưu adapter dpo; Reward gap > 0; Vẽ biểu đồ reward curves. |
| `04_compare_and_eval`| Đánh giá (Eval)| So sánh 8 prompt giữa SFT-only và SFT+DPO | Render bảng so sánh; Báo cáo số lượng win/loss/tie. |
| `05_merge_deploy` | Triển khai (Bonus)| Merge GGUF Q4_K_M và test bằng llama-cpp | File < 5GB; Lệnh chạy sinh được tiếng Việt có nghĩa. |
| `06_benchmark` | Benchmark (Bonus)| Đo lường IFEval, GSM8K, MMLU | Biểu đồ 4-bar chart; Thảo luận về alignment-tax trong Reflection. |

## 3. Các bước cần làm từng phần & Chi tiết triển khai

### Phần 1: SFT Mini (Notebook `01_sft_mini.py`)
- **Cần làm**: Xây dựng lại bản SFT từ bài Lab 21 để làm base checkpoint.
- **Triển khai**:
  - Dùng thư viện Unsloth với cấu hình LoRA r=16, alpha=32.
  - Chạy tập dữ liệu 1k VN Alpaca trong 1 epoch.
  - **Output mong muốn**: Lưu adapter vào `adapters/sft-mini/`, xuất biểu đồ loss (cần chụp lại `01_sft_loss.png`).

### Phần 2: Chuẩn bị Preference Data (Notebook `02_preference_data.py`)
- **Cần làm**: Chuẩn bị dataset để train DPO.
- **Triển khai**:
  - Tải tập dữ liệu `argilla/ultrafeedback-binarized-preferences-cleaned`.
  - Transform/Định dạng dữ liệu thành các cột: `prompt`, `chosen`, `rejected`.
  - **Output mong muốn**: File `data/pref/train.parquet`. In ra màn hình 3 ví dụ để đảm bảo `chosen` luôn khác `rejected`.

### Phần 3: Huấn luyện DPO (Notebook `03_dpo_train.py`)
- **Cần làm**: Chạy thuật toán DPO để align model.
- **Triển khai**:
  - Sử dụng TRL `DPOTrainer` với cấu hình: `beta=0.1`, learning rate `5e-7`.
  - Model huấn luyện là model SFT vừa tạo, còn reference model được giữ đóng băng (frozen).
  - **Output mong muốn**: Lưu adapter vào `adapters/dpo/`. Vẽ đồ thị reward curves (`03_dpo_reward_curves.png`) cho cả chosen và rejected. Khoảng cách (reward gap) phải lớn hơn 0.

### Phần 4: So sánh và Đánh giá (Notebook `04_compare_and_eval.py`)
- **Cần làm**: Thẩm định xem model DPO có thật sự tốt hơn hay không.
- **Triển khai**:
  - Dùng 8 prompts cố định đưa cho cả 2 model (SFT-only và SFT+DPO).
  - So sánh kết quả bằng judge AI (như GPT-4o/Claude) hoặc tự chấm bằng tay.
  - **Output mong muốn**: Bảng tổng kết (`04_side_by_side_table.png`) bao gồm số lượng win/loss/tie (thắng/thua/hòa).

### Phần 5: Merge và Deploy (Tùy chọn - Notebook `05_merge_deploy_gguf.py`)
- **Cần làm**: Đóng gói model để có thể chạy offline hoặc deploy thật.
- **Triển khai**:
  - Unload LoRA và merge weights lại vào base model.
  - Chuyển đổi định dạng sang GGUF chuẩn Q4_K_M.
  - Chạy thử nghiệm smoke test qua thư viện `llama-cpp-python`.
  - **Output mong muốn**: Chụp ảnh `06_gguf_smoke.png` thể hiện văn bản tiếng Việt xuất ra tốt.

### Phần 6: Benchmark (Tùy chọn - Notebook `06_benchmark.py`)
- **Cần làm**: Đánh giá định lượng model bằng các chuẩn Benchmark.
- **Triển khai**:
  - Chạy điểm qua các bài test MMLU, GSM8K, IFEval, AlpacaEval-lite.
  - Chạy cho cả 2 model SFT và SFT+DPO, sau đó biểu diễn thành biểu đồ 4 cột (`07-benchmark-comparison.png`).
  - Viết giải thích về "alignment-tax" trong bản Reflection.

### Phần 7: Submission (Nộp bài)
- Cập nhật thư mục `submission/screenshots` đủ 6 ảnh bắt buộc.
- Hoàn thiện `submission/REFLECTION.md` với tối thiểu 150 chữ cho Section 3 và 6.
- Chạy lệnh `make verify` kiểm tra đầu ra hợp lệ.
- Push lên public Github Repo và nộp link qua VinUni LMS. (KHÔNG tạo PR, KHÔNG để repo private).
