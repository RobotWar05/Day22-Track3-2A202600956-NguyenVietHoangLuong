# Bảng Theo Dõi Công Việc (TODO List)

*Ghi chú: Bản checklist này được dùng để theo dõi tiến độ, ghi chú lỗi và tình trạng hoàn thành các đầu mục công việc của Lab 22. AI Assistant (tôi) sẽ sử dụng nó để đánh dấu và note lại theo yêu cầu của bạn, giúp việc quản lý hoặc chuyển giao context trở nên thuận lợi hơn.*

---

## Trạng thái Công Việc

- [x] **Khởi tạo và thiết lập môi trường**
  - Thực thi: Chạy lệnh `make setup` hoặc `bash setup-laptop.sh`.
  - Ghi chú/Lỗi: *(trống)*

- [x] **Hoàn thành Phần 1: SFT-mini checkpoint (`01_sft_mini.py`)**
  - Thực thi: Chạy script `make sft` hoặc NB `01_sft_mini.py`.
  - Kiểm tra lưu Adapter cấu hình LoRA r=16, alpha=32.
  - Chụp ảnh đồ thị loss: `01_sft_loss.png`.
  - Ghi chú/Lỗi: Lỗi PicklingError do tokenizer multiprocessing, đã fix bằng dataset_num_proc=1.

- [x] **Hoàn thành Phần 2: Chuẩn bị Preference Data (`02_preference_data.py`)**
  - Thực thi: Chạy script `make data` hoặc NB `02_preference_data.py`.
  - Kiểm tra sinh file data `train.parquet`.
  - Ghi chú/Lỗi: *(trống)*

- [ ] **Hoàn thành Phần 3: Huấn luyện DPO (`03_dpo_train.py`)**
  - Thực thi: Chạy script `make dpo` hoặc NB `03_dpo_train.py`.
  - Chụp ảnh đồ thị Reward gap: `03_dpo_reward_curves.png`.
  - Ghi chú/Lỗi: *(trống)*

- [ ] **Hoàn thành Phần 4: Đánh giá và So sánh (`04_compare_and_eval.py`)**
  - Thực thi: Chạy script `make eval` hoặc NB `04_compare_and_eval.py`.
  - Đánh giá bằng AI judge / Chấm tay 8 câu prompts.
  - Chụp ảnh bảng tổng hợp Win/Loss/Tie: `04_side_by_side_table.png`.
  - Ghi chú/Lỗi: *(trống)*

- [ ] **(Tùy chọn) Hoàn thành Phần 5: Merge và Deploy GGUF (`05_merge_deploy_gguf.py`)**
  - Thực thi: Chạy script `make deploy` hoặc NB `05_merge_deploy_gguf.py`.
  - Chụp ảnh log/kết quả văn bản từ llama.cpp: `06_gguf_smoke.png`.
  - Ghi chú/Lỗi: *(trống)*

- [ ] **(Tùy chọn) Hoàn thành Phần 6: Benchmark Định lượng (`06_benchmark.py`)**
  - Thực thi: Chạy script `make bench` hoặc NB `06_benchmark.py`.
  - Chụp ảnh biểu đồ 4 cột: `07-benchmark-comparison.png`.
  - Ghi chú/Lỗi: *(trống)*

- [ ] **Hoàn thiện Báo cáo (Reflection)**
  - Viết report file `submission/REFLECTION.md`.
  - Đảm bảo Section 3 & Section 6 trên 150 chữ.
  - Đảm bảo đủ số lượng ảnh trong `submission/screenshots/`.
  - Ghi chú/Lỗi: *(trống)*

- [ ] **Kiểm tra Gatekeeper và Nộp bài**
  - Chạy script `make verify` để kiểm tra độ trọn vẹn của repository.
  - Push mã nguồn lên Github ở chế độ public.
  - Paste link Github lên VinUni LMS.
  - Ghi chú/Lỗi: *(trống)*

---

### Nhật Ký Các Lỗi Quan Trọng (Issue Logs)
*(Mục này dùng để ghi lại những lỗi khó (như OOM, thiếu token, bug script) và cách đã xử lý thành công)*

- **Lỗi PicklingError ở SFTTrainer / DPOTrainer**: Do Tokenizer multiprocessing không pickle được. Xử lý: thêm `dataset_num_proc=1` vào cấu hình.
- **Lỗi NotImplementedError BMGHK ở DPOTrainer**: Card T4 không hỗ trợ backward pass của GQA trong xformers. Xử lý: Gọi hàm `PatchDPOTrainer()` của Unsloth để vá lỗi attention.
