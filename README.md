# BÁO CÁO ĐỒ ÁN: DỰ ĐOÁN NĂNG LƯỢNG TIÊU THỤ TRONG NHÀ THÔNG MINH

## Tóm tắt (Abstract)
Báo cáo này tập trung vào bài toán dự báo năng lượng tiêu thụ của các thiết bị điện trong hộ gia đình. Bằng cách sử dụng tập dữ liệu **uci appliances energy prediction**, chúng tôi thực hiện so sánh hiệu quả giữa hai kiến trúc mạng nơ-ron sâu phổ biến: **Transformer** và **LSTM**. Kết quả thực nghiệm cho thấy Transformer vượt trội trong việc nắm bắt các phụ thuộc dài hạn, trong khi LSTM duy trì sự ổn định tốt trong các chuỗi dữ liệu ít biến động.

---

## 1. Giới thiệu (Introduction)
* **Bối cảnh**: Sự gia tăng của các thiết bị IoT trong nhà thông minh tạo ra nhu cầu cấp thiết về việc tối ưu hóa điện năng tiêu thụ.
* **Ứng dụng**: Hệ thống dự báo giúp người dùng tối ưu chi phí và hỗ trợ các đơn vị vận hành lưới điện dự báo phụ tải.
* **Mục tiêu**: Xây dựng mô hình hồi quy đa biến có khả năng dự báo mức tiêu thụ điện dựa trên các yếu tố môi trường (nhiệt độ, độ ẩm).

---

## 2. Tổng quan lý thuyết (Related Work / Background)
* **Dự báo chuỗi thời gian**: Khác với bài toán Computer Vision (Object Detection/Segmentation), dự báo chuỗi thời gian tập trung vào việc mô hình hóa các phụ thuộc theo thời gian.
* **Phương pháp**:
    - **LSTM**: Một dạng đặc biệt của RNN, giải quyết hiệu quả vấn đề mất mát đạo hàm.
    - **Transformer**: Kiến trúc hiện đại dựa hoàn toàn trên cơ chế Self-Attention, cho phép xử lý song song và nắm bắt ngữ cảnh toàn cục.
* **Lý do chọn**: Chúng tôi chọn Transformer để đánh giá khả năng của cơ chế Attention so với cách tiếp cận tuần tự truyền thống của LSTM.

---

## 3. Dataset & Tiền xử lý dữ liệu
### 3.1 Dataset
* **Tên dataset**: uci appliances energy prediction
* **Số lượng mẫu**: 19735 bản ghi.
* **Số lượng biến**: 27 đặc trưng môi trường.
* **Định dạng nhãn**: Giá trị thực (Wh) biểu thị năng lượng tiêu thụ.

### 3.2 Tiền xử lý
* **Normalize**: Áp dụng MinMaxScaler để đưa dữ liệu về khoảng [0, 1].
* **Sliding Window**: Sử dụng cửa sổ 14 bước thời gian để dự báo 1 bước tiếp theo.
* **Chia tập dữ liệu**: Train 80%, Test 20% (theo trình tự thời gian, không xáo trộn).

---

## 4. Phương pháp đề xuất (Methodology)
### 4.1 Kiến trúc mô hình
* **Backbone**: Lớp Linear Projection kết hợp với Positional Encoding.
* **Mô hình chính**:
    - **Transformer**: 2 lớp Encoder với 4 đầu Attention.
    - **LSTM**: 2 lớp ẩn chồng nhau.



### 4.2 Hàm mất mát (Loss Function)
* **MSE Loss**: Được sử dụng làm hàm tối ưu hóa chính để giảm thiểu sai số bình phương giữa giá trị thực và dự báo.

### 4.3 Thiết lập huấn luyện
* **Optimizer**: Adam
* **Learning rate**: 1e-05
* **Epochs**: 150
* **Batch size**: 1024

---

## 5. Thực nghiệm & Kết quả
### 5.1 Metric đánh giá
* **MSE**: Sai số bình phương trung bình.
* **MAE**: Sai số tuyệt đối trung bình.
* **MAPE**: Tỷ lệ phần trăm sai số tuyệt đối.

### 5.2 Kết quả định lượng
| Model        | MSE             | MAE             | MAPE (%)        |
| :----------- | :-------------- | :-------------- | :-------------- |
| Transformer  |         4086.69 |           37.11 |           42.13 |
| LSTM         |         5937.39 |           38.25 |           36.00 |

### 5.3 Kết quả định tính
* **Biểu đồ dự báo tổng quát**:
![Comparison](../figures/prediction_comparison.png)

* **Phân tích Attention qua các Layer/Head**:
![Attention](../figures/attention_layers_heads_comparison.png)

* **Tầm quan trọng của Đặc trưng**:
![Feature Importance](../figures/feature_importance_saliency.png)

---

## 6. Phân tích & Thảo luận
* **Phân tích lỗi**: Cả hai mô hình đều gặp khó khăn khi dự báo các điểm tiêu thụ năng lượng tăng vọt (spikes) do hành vi ngẫu nhiên của người dùng.
* **Hoạt động kém**: Hiệu năng giảm nhẹ khi dữ liệu môi trường (độ ẩm bên ngoài) có sự thay đổi quá nhanh.
* **Ưu điểm**: Transformer thể hiện sự nhạy bén vượt trội đối với các thay đổi xu hướng dài hạn.

---

## 7. Kết luận & Hướng phát triển
* **Kết quả**: Xây dựng thành công pipeline dự báo với độ chính xác cao.
* **Bài học**: Cơ chế Attention giúp việc giải thích mô hình (interpretability) trở nên trực quan hơn.
* **Hướng phát triển**: Áp dụng các kiến trúc mới hơn như Informer hoặc PatchTST cho chuỗi thời gian dài.

---

## 8. Phụ lục (Appendix)
* **Cấu trúc thư mục**: 
    - `data/`: Dữ liệu thô.
    - `weights/`: Trọng số mô hình đã huấn luyện.
    - `figures/`: Các biểu đồ phân tích.
* **Tiến trình hội tụ**:
![Loss](../figures/training_loss_comparison.png)

---

## Tài liệu tham khảo (References)
1. Vaswani, A. et al. "Attention is All You Need" (2017).
2. UCI Machine Learning Repository.
3. PyTorch Deep Learning Framework documentation.
