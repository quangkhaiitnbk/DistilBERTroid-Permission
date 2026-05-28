# DistilBERTroid-Permission: Phát hiện mã độc Android dựa trên Permission/API

> Project thực nghiệm cho tiểu luận môn **An toàn trên thiết bị di động**.  
> Đề tài: **Phát hiện mã độc trên nền tảng Android: Tiếp cận từ biểu diễn nhúng nơ-ron đến kiểm chứng thực tế bằng BERTroid**.

## 1. Giới thiệu

Project này triển khai mô hình **DistilBERTroid-Permission** cho bài toán phát hiện mã độc Android bằng phân tích tĩnh dựa trên **permission** và **API-related features**.

Ý tưởng chính của project là chuyển tập quyền truy cập/API của mỗi ứng dụng Android thành một chuỗi văn bản, sau đó sử dụng mô hình Transformer, cụ thể là **DistilBERT**, để học biểu diễn ngữ cảnh và phân loại ứng dụng thành hai lớp:

- `0`: Benign - ứng dụng lành tính
- `1`: Malware - ứng dụng mã độc

Project được xây dựng dựa trên hướng tiếp cận của bài báo **Detecting Android Malware: From Neural Embeddings to Hands-On Validation with BERTroid**, nhưng có điều chỉnh để phù hợp với điều kiện thực nghiệm học thuật trên Google Colab.

## 2. Điểm thay đổi so với BERTroid gốc

Trong bài báo gốc, BERTroid sử dụng BERT để học biểu diễn từ chuỗi permission của ứng dụng Android. Trong project này, mô hình được điều chỉnh thành **DistilBERTroid-Permission** với các thay đổi chính:

| Nội dung | BERTroid gốc | Project này |
|---|---|---|
| Encoder | BERT-base | DistilBERT-base-uncased |
| Đầu vào | Chuỗi permission | Chuỗi permission/API sau chuẩn hóa |
| Mục tiêu | Phát hiện mã độc Android | Phát hiện mã độc Android trong điều kiện thực nghiệm Colab |
| Lý do thay đổi | Mô hình mạnh nhưng nặng hơn | Nhẹ hơn, huấn luyện nhanh hơn, phù hợp tài nguyên hạn chế |

## 3. Bộ dữ liệu sử dụng

Project gồm hai notebook thực nghiệm:

### 3.1. Android Permission Dataset - Kaggle

Notebook:

```text
B9D54_10_Tran_Van_Quang_Khai_ATMobile_tren_bo_du_lieu_Android_Permission_Dataset_Kaggle.ipynb
```

Vai trò:

- Kiểm chứng pipeline cơ bản.
- Đọc dữ liệu CSV.
- Chuẩn hóa nhãn từ cột `Class`.
- Loại bỏ metadata.
- Lọc các cột permission nhị phân.
- Chuyển vector permission 0/1 thành chuỗi token.
- Huấn luyện và đánh giá mô hình DistilBERTroid-Permission.

### 3.2. Mendeley Android Permissions Dataset

Notebook:

```text
B9D54_10_Tran_Van_Quang_Khai_ATMobile_tren_bo_du_lieu_Mendeley.ipynb
```

Vai trò:

- Thực nghiệm mở rộng trên dữ liệu lớn hơn.
- Đọc dữ liệu Excel nhiều sheet.
- Gán nhãn theo sheet:
  - `Google play store`: benign
  - `Third party`: benign
  - `Malware Applications`: malware
- Trích chọn permission lúc cài đặt, runtime permission và API calls.
- So sánh với baseline truyền thống:
  - TF-IDF + Logistic Regression
  - TF-IDF + Linear SVM
  - DistilBERTroid-Permission

## 4. Môi trường thực nghiệm

Khuyến nghị chạy trên **Google Colab** với GPU.

Các thư viện chính:

```text
python>=3.10
pandas
numpy
matplotlib
scikit-learn
torch
transformers
accelerate
openpyxl
kaggle
```

## 5. Hướng dẫn chạy trên Google Colab

### Bước 1. Mở notebook

Với notebook Kaggle, truy cập link:

```text
[B9D54_10_Tran_Van_Quang_Khai_ATMobile_tren_bo_du_lieu_Android_Permission_Dataset_Kaggle.ipynb]
(https://colab.research.google.com/drive/1-1RJDSqgHlPcbHX-G67YRg-v2Nab5RwB?usp=sharing)
```

Với notebook Mendeley, truy cập link:

```text
[B9D54_10_Tran_Van_Quang_Khai_ATMobile_tren_bo_du_lieu_Mendeley.ipynb]
(https://colab.research.google.com/drive/1U0YYutbtTXvI9G9mpsqq8TTtPYsTDr2S?usp=sharing)
```

### Bước 2. Bật GPU

Trong Google Colab:

```text
Runtime → Change runtime type → Hardware accelerator → GPU → Save
```

### Bước 3. Chạy các cell cài đặt thư viện

Trong notebook đã có cell cài đặt thư viện, ví dụ:

```python
!pip install -q transformers accelerate kaggle openpyxl
```

### Bước 4. Chuẩn bị dữ liệu

Với notebook Kaggle:

1. Tạo API token trên Kaggle.
2. Tải file `kaggle.json`.
3. Upload `kaggle.json` lên Colab khi notebook yêu cầu.
4. Notebook sẽ tải và giải nén Android Permission Dataset.

Với notebook Mendeley:

1. Tải dataset Mendeley Android Permissions Dataset.
2. Upload file dataset lần đầu lên Colab hoặc lưu vào Google Drive.
3. Notebook sẽ đọc file Excel và gán nhãn theo từng sheet.

### Bước 5. Chạy tiền xử lý dữ liệu

Các bước tiền xử lý chính:

- Đọc dữ liệu gốc.
- Chuẩn hóa nhãn.
- Loại bỏ metadata.
- Giữ lại các cột permission/API dạng nhị phân.
- Chuyển các feature có giá trị `1` thành chuỗi token.
- Chia dữ liệu thành train, validation và test.

Ví dụ biểu diễn đầu vào sau chuyển đổi:

```text
internet access_network_state read_phone_state write_external_storage
```

### Bước 6. Huấn luyện mô hình

Mô hình sử dụng:

```text
DistilBERT encoder
→ hidden representation
→ dropout
→ fully connected layer
→ logits
→ softmax
→ benign/malware
```

Các kỹ thuật hỗ trợ huấn luyện:

- AdamW optimizer
- Cross-Entropy Loss
- Linear scheduler with warmup
- Mixed precision nếu có GPU
- Early stopping
- Tối ưu ngưỡng dự đoán trên tập validation

### Bước 7. Đánh giá mô hình

Các độ đo đánh giá:

- Accuracy
- Precision
- Recall
- F1-score
- MCC
- ROC-AUC
- PR-AUC
- Confusion matrix


## 6. Kết quả thực nghiệm tóm tắt

### 6.1. Kaggle

Thực nghiệm Kaggle phù hợp để kiểm chứng pipeline tổng thể, bao gồm đọc dữ liệu, tiền xử lý permission, chuyển đổi thành chuỗi văn bản, huấn luyện mô hình và đánh giá kết quả.

### 6.2. Mendeley

Thực nghiệm Mendeley là thực nghiệm mở rộng với dữ liệu phong phú hơn, bao gồm permission lúc cài đặt, runtime permission và API calls. Kết quả trong báo cáo cho thấy mô hình DistilBERTroid-Permission đạt hiệu quả tốt hơn trên bộ dữ liệu này so với thực nghiệm Kaggle.

Kết quả test trên Mendeley trong báo cáo:

| Metric | Giá trị |
|---|---:|
| Accuracy | 0.9622 |
| Precision | 0.9712 |
| Recall | 0.9527 |
| F1-score | 0.9619 |
| MCC | 0.9246 |
| ROC-AUC | 0.9908 |
| PR-AUC | 0.9923 |


## 7. Tài liệu tham khảo chính

1. Chaieb, M., Ghorab, M. A., & Saied, M. A. (2024). *Detecting Android Malware: From Neural Embeddings to Hands-On Validation with BERTroid*.
2. Android Developers. *Permissions on Android*.
3. Hugging Face Transformers Documentation. *DistilBERT*.
4. Kaggle. *Android Permission Dataset*.
5. Mendeley Data. *Android Permissions Dataset*.
