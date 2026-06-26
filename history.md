# Nhật Ký Chạy Colab (Run History / Scratchpad)

*Ghi chú: File này dùng làm nháp để bạn lưu nhanh các kết quả, thông số và log quan trọng từ Google Colab trước khi tổng hợp vào bản báo cáo chính thức (`submission/REFLECTION.md`). Bất cứ khi nào chạy xong 1 cell có output quan trọng, hãy copy/paste thẳng vào đây hoặc đưa cho tôi để tôi note lại giúp bạn.*

---

## 1. Thông số môi trường (Setup Info)
- **Tên GPU được cấp**: Tesla T4 (15.6 GB)
- **Phiên bản CUDA/Driver**: *(Nếu có)*
- **Tier đang chạy**: T4

## 2. Kết quả SFT (Từ Notebook 01_sft_mini)
- **Training time (NB1)**: 7 phút 27 giây (125 steps)
- **VRAM Peak**: *(chưa đo cụ thể, chạy vừa vặn trên T4)*
- **Final loss (SFT)**: 1.1106

## 3. Kết quả DPO (Từ Notebook 03_dpo_train)
- **Training time (NB3)**: 
- **VRAM Peak**: 
- **Final loss (DPO)**: 
- **Reward Gap (chosen - rejected) ở step cuối**: 

## 4. Kết quả So sánh SFT vs DPO (Từ Notebook 04_compare_and_eval)
- **Tổng quan Win / Loss / Tie**: *(Ví dụ: DPO thắng 5, SFT thắng 2, Hòa 1)*
- **Mean output length (độ dài câu trả lời trung bình)**:
  - SFT: 
  - DPO: 

## 5. Kết quả Benchmark (Nếu chạy Notebook 06_benchmark)
- **IFEval**: 
- **GSM8K**: 
- **MMLU**: 
- **AlpacaEval-lite**: 

---

**📋 CÁCH LÀM VIỆC VỚI FILE NÀY:**
1. Trên Colab, cứ mỗi khi chạy xong một notebook và có số liệu quan trọng (ví dụ loss của SFT, hoặc số win/loss), bạn hãy copy đoạn text đó ném vào khung chat.
2. Tôi sẽ lấy thông tin bạn đưa, phân tích sơ bộ và tự động điền vào file `history.md` này giúp bạn.
3. Khi bạn chạy xong hết toàn bộ, chúng ta sẽ lấy toàn bộ dữ liệu từ file này để hoàn thiện file báo cáo cuối cùng `submission/REFLECTION.md` cực kỳ nhanh chóng và chính xác.
