# CSC4005 – Lab 2 Report

## 1. Thông tin chung
- Họ và tên:Đỗ Ngọc Trung
- Lớp:KHMT 16-01
- Repo:https://github.com/FIT-DNU-CS-16-01/csc4005-lab2-cnn-neu-Blackbin03.git
- W&B project:csc4005-lab2-neu-cnn

## 2. Bài toán
Mô tả ngắn bài toán phân loại 6 lớp trên NEU-CLS.

## 3. Mô hình và cấu hình
### 3.1. MLP baseline từ Lab 1
### 3.2. CNN from scratch
### 3.3. Transfer learning
## 4. Bảng kết quả
| Model | Train mode | Best Val Acc | Test Acc | Epoch time (s) | Trainable Params | Nhận xét |
|---|---|---:|---:|---:|---:|---|
| MLP | scratch |  |  |  |  |  |
| CNN-small | scratch (cnn_small_baseline) | 1.0000 | 1.0000 | 3.5433 | 32,098 | Perfect scores in this saved run — likely overfitting or data split issue |
| CNN-small | scratch (test_run) | 0.4699 | 0.4578 | 3.7477 | 32,098 | More realistic run — model predicts one class for most samples (see confusion matrix) |
| ResNet18 | transfer/finetune |  |  |  |  | outputs/resnet18_transfer is empty — no saved run |

> Ghi chú: Có hai kết quả cho `cnn_small` trong `outputs/`: một run đạt 100% (cần kiểm tra tính hợp lệ), một run (`test_run`) có độ chính xác thấp.

## 5. Phân tích learning curves

- `cnn_small_baseline` (perfect run): loss và accuracy ở mức hoàn hảo — thường là dấu hiệu của overfitting hoặc lỗi trong phân chia tập dữ liệu (ví dụ: test/val trùng với train hoặc seed/split không cố định).
- `test_run`: val acc ~0.47, test acc ~0.46. Xem `outputs/test_run/curves.png` và `outputs/test_run/history.csv` để hiểu tiến trình (divergence, plateau, overfitting...).

## 6. Confusion matrix và lỗi dự đoán sai

- `outputs/cnn_small_baseline/confusion_matrix.png` — tất cả dự đoán đúng (ma trận dạng đường chéo hoàn chỉnh).
- `outputs/test_run/confusion_matrix.png` — theo `metrics.json`:
	- Crazing: 0 đúng / 45 sai (tất cả bị dự đoán nhầm)
	- Inclusion: 38 đúng / 0 sai
	- Kết luận: mô hình trong `test_run` dự đoán hầu hết mẫu là "Inclusion" — nguyên nhân thường thấy: class imbalance, lỗi tiền xử lý (ví dụ: nhãn, chỉ dùng 2 lớp), hoặc vấn đề với transform/normalization.

## 7. Kết luận

- CNN có cải thiện so với MLP không?
	- Chưa có đủ thông tin. Cần chạy MLP baseline (hoặc thêm kết quả) để so sánh.
- Transfer learning có tốt hơn không?
	- Không rõ — `outputs/resnet18_transfer` trống. Nên chạy thử ResNet18 với `--train_mode transfer`.
- Khi nào nên chọn transfer learning?
	- Dataset nhỏ hoặc khi đặc trưng ảnh tương đồng với ImageNet; transfer giúp tránh overfitting và giảm thời gian huấn luyện.

## 8. Cách tái tạo (reproduce)

1) Giải nén dữ liệu (nếu cần):

```powershell
# Windows PowerShell (cmd dùng powershell -Command ... nếu cần)
powershell -Command "Expand-Archive -Path data\\NEU-CLS.zip -DestinationPath data\\NEU-CLS"
```

2) Ví dụ chạy training (cnn_small, scratch):

```powershell
python -m src.train --data_dir data/NEU-CLS --run_name cnn_small_baseline --model_name cnn_small --train_mode scratch --epochs 20 --batch_size 32
```

3) Ví dụ chạy transfer learning (ResNet18):

```powershell
python -m src.train --data_dir data/NEU-CLS --run_name resnet18_transfer --model_name resnet18 --train_mode transfer --epochs 20 --batch_size 32
```

4) Tệp đầu ra: sau khi chạy, `outputs/<run_name>/` sẽ chứa `best_model.pt`, `confusion_matrix.png`, `curves.png`, `history.csv`, và `metrics.json`.

## 9. Kiểm tra nhanh và gợi ý sửa lỗi

- Kiểm tra số lượng và phân bố lớp trong `data/NEU-CLS`.
- Mở `outputs/*/history.csv` để xem lịch sử loss/acc per-epoch.
- Kiểm tra `src/dataset.py` (data split, transforms, num_channels, normalization) để loại trừ lỗi preprocessing.

## 10. Tệp tham khảo trong `outputs/`

- `outputs/cnn_small_baseline/metrics.json`
- `outputs/cnn_small_baseline/confusion_matrix.png`
- `outputs/cnn_small_baseline/curves.png`
- `outputs/test_run/metrics.json`
- `outputs/test_run/confusion_matrix.png`
- `outputs/test_run/curves.png`

---

*Nếu bạn muốn, tôi có thể tự động nhúng các hình PNG vào báo cáo (nhúng base64 hoặc tạo đường dẫn), hoặc trích xuất chi tiết per-class từ `metrics.json` để thêm vào báo cáo.*
