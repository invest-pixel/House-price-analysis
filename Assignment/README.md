# Dự đoán giá nhà ở California (Regression)

Bài tập Machine Learning phân tích và dự đoán giá nhà ở California sử dụng 3 mô hình: Linear Regression, Random Forest Regressor, và Support Vector Regressor (SVR).

## Cấu trúc thư mục

- `notebook.ipynb`: Chứa toàn bộ quá trình EDA, tiền xử lý, code huấn luyện và phân tích kết quả.
- `requirements.txt`: Danh sách các thư viện cần cài đặt.
- `data/`: Nơi lưu trữ bộ dữ liệu `california_housing.csv` (được tải tự động từ Google Drive).
- `outputs/figures/`: Lưu các biểu đồ được sinh ra (Phân phối, Heatmap tương quan, Feature Importance).
- `outputs/results.csv`: File lưu bảng tổng hợp đánh giá các mô hình.

## Hướng dẫn cài đặt và thực thi

1. Cài đặt các thư viện phụ thuộc:
   ```bash
   pip install -r requirements.txt
   ```

2. Mở file `notebook.ipynb` bằng VS Code (cài extension Jupyter) hoặc chạy lệnh `jupyter notebook`.

3. Chọn "Run All" hoặc chạy từng cell từ trên xuống dưới để xem chi tiết quá trình. Dữ liệu sẽ tự động được tải về trong lần chạy đầu tiên.
