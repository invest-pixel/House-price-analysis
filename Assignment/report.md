# BÁO CÁO BÀI TẬP: DỰ ĐOÁN GIÁ NHÀ Ở CALIFORNIA (REGRESSION)

## 1. Giới thiệu bài toán
Bài toán đặt ra là xây dựng một mô hình học máy (Machine Learning) có khả năng dự đoán giá trị trung bình của các ngôi nhà (median_house_value) tại bang California dựa trên các đặc trưng về vị trí địa lý, số lượng phòng và thông tin nhân khẩu học. Đây là một bài toán **Regression** vì biến mục tiêu là một giá trị số liên tục.

## 2. Mô tả dataset
Bộ dữ liệu được sử dụng là **California Housing Dataset**. Bộ dữ liệu này chứa thông tin thống kê của các khu dân cư ở California từ cuộc điều tra dân số năm 1990. 
- Mỗi dòng đại diện cho một nhóm các căn nhà.
- Biến mục tiêu: `median_house_value`.
- Các biến độc lập: Gồm 9 biến như `longitude`, `latitude`, `housing_median_age`, `total_rooms`, `total_bedrooms`, `population`, `households`, `median_income`, và `ocean_proximity`.

## 3. Khám phá dữ liệu (EDA)
- **Kiểm tra thông tin:** Dữ liệu gồm 20.640 dòng. Hầu hết các cột đều là dữ liệu số (float64), ngoại trừ cột `ocean_proximity` là dữ liệu phân loại (object/chuỗi).
- **Phân phối biến mục tiêu:** Biểu đồ histogram của `median_house_value` cho thấy phân phối hơi lệch phải (right-skewed) và có một mức trần (cap) tại giá trị 500.000 USD.
- **Tương quan (Correlation):** Ma trận tương quan (Heatmap) chỉ ra rằng biến `median_income` (thu nhập) có độ tương quan thuận cao nhất đối với giá nhà (0.69).

## 4. Tiền xử lý dữ liệu
Quy trình làm sạch và chuẩn bị dữ liệu bao gồm 4 bước chính:
- **Mã hóa (Encoding):** Sử dụng One-Hot Encoding (`pd.get_dummies` với `drop_first=True`) để chuyển đổi cột phân loại `ocean_proximity` thành các biến số nhị phân.
- **Xử lý missing values:** Có 207 dòng bị thiếu dữ liệu ở cột `total_bedrooms`. Giá trị Trung vị (Median) được sử dụng để điền vào các ô trống này nhằm hạn chế tác động của nhiễu (outliers).
- **Chia tập dữ liệu:** Sử dụng `train_test_split` để chia dữ liệu thành 80% tập Train (huấn luyện) và 20% tập Test (kiểm thử). Đặt `random_state=42` để cố định kết quả chia.
- **Chuẩn hóa dữ liệu (Scaling):** Các biến số có đơn vị khác nhau (thu nhập vs độ tuổi nhà), do đó `StandardScaler` được sử dụng để đưa các đặc trưng về cùng một thang đo. Lưu ý quan trọng: Chỉ `fit` scaler trên tập Train và `transform` trên tập Test để tránh rò rỉ dữ liệu (data leakage).

## 5. Mô hình sử dụng
3 mô hình Hồi quy được lựa chọn đại diện cho 3 trường phái khác nhau:
1. **Linear Regression:** Mô hình hồi quy tuyến tính cổ điển, được dùng làm baseline.
2. **Random Forest Regressor:** Mô hình học máy tập hợp (Ensemble) dựa trên nhiều Cây quyết định (Decision Trees), có khả năng giải quyết tốt các dữ liệu phi tuyến tính phức tạp. Được cài đặt với số cây `n_estimators=100`.
3. **Support Vector Regressor (SVR):** Mô hình hồi quy véc-tơ hỗ trợ sử dụng hạt nhân (kernel) `rbf`, tối ưu hóa dựa trên giới hạn lề (margin).

## 6. Kết quả đánh giá
 Kết quả như sau:

| Mô hình | MAE | RMSE | R2 Score | Thời gian huấn luyện |
| :--- | :--- | :--- | :--- | :--- |
| **Linear Regression** | $50,670 | $70,060 | 0.625 | ~0.01s |
| **Random Forest** | $31,631 | $49,008 | **0.816** | ~1.38s |
| **SVR** | $86,961 | $116,858 | -0.042 | ~10.58s |

## 7. So sánh mô hình
- **Mô hình tốt nhất:** Random Forest Regressor là mô hình hoạt động vượt trội nhất. R2 Score đạt 0.816 (mô hình giải thích được 81.6% sự thay đổi của giá nhà) và sai số RMSE là thấp nhất ($49,008). 
- **Giải thích:** Dữ liệu bất động sản có mối liên hệ phi tuyến tính rất phức tạp giữa các đặc trưng (VD: kết hợp giữa vĩ độ, kinh độ và thu nhập để tạo nên giá trị). Random Forest là một thuật toán dựa trên sự phân nhánh của nhiều cây quyết định nên rất nhạy bén với cấu trúc dữ liệu này, vượt trội hoàn toàn so với mô hình tuyến tính đơn giản là Linear Regression.
- **Tính trạng Fit:** 
  - SVR đang bị *Underfitting* trầm trọng (R2 âm) do không thích ứng được với sự chênh lệch đơn vị của biến mục tiêu (target scale).
  - Linear Regression hơi *Underfitting* vì mô hình quá đơn giản.
  - Random Forest tổng quát hóa khá tốt do điểm dự đoán trên tập Test (chưa từng thấy) vẫn đạt ngưỡng cao.

## 8. Kết luận và hướng cải thiện
**Khó khăn gặp phải:**
Việc sử dụng thuật toán SVR với tham số mặc định gây ra kết quả cực kỳ sai lệch (R2 âm). Nguyên nhân do SVR rất nhạy cảm với đơn vị đo lường của biến mục tiêu. Do biến `median_house_value` có phạm vi rất lớn (hàng trăm ngàn đô la) mà chưa được scale, SVR không thể điều chỉnh được lề (margin) phù hợp.

**Hướng cải thiện trong tương lai:**
- Áp dụng `GridSearchCV` hoặc `RandomizedSearchCV` để dò tìm các siêu tham số (Hyperparameter tuning) tối ưu nhất cho SVR (điều chỉnh tham số `C`) và Random Forest (điều chỉnh `max_depth`).
- Chuẩn hóa (Scale) cả biến mục tiêu `y` nếu muốn SVR hoạt động hiệu quả.
- Sử dụng thêm các mô hình Ensemble mạnh mẽ khác như XGBoost hay LightGBM.
- Khai thác đặc trưng (Feature Engineering): Tạo thêm các tỷ lệ như số phòng ngủ/số phòng tổng cộng, dân số/số hộ gia đình.
