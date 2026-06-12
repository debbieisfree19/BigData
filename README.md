# 📦 BigData Nhóm 3 — Phân tích Pakistan Largest E-Commerce Dataset
 
Dự án môn **Big Data** — phân tích bộ dữ liệu thương mại điện tử lớn nhất Pakistan bằng **Apache Hadoop HDFS** và **Apache Spark** (PySpark + Spark SQL), kết hợp mô hình **Machine Learning (LinearSVC)** dự đoán đơn hàng bị hủy, chạy trên môi trường Windows (Jupyter Notebook / VS Code).
 
---
 
## 👥 Thành viên nhóm
 
| STT | Họ và tên | MSSV |
|-----|-----------|------|
| 1 | Nguyễn Quỳnh Trang | 31231027655 |
| 2 | Trần Thảo Trang | 31231022291 |
| 3 | Nguyễn Hoàng Phước | 31231021201 |
 
---
 
## 📊 Dataset
 
| Thông tin | Chi tiết |
|-----------|---------|
| **Tên** | Pakistan Largest E-Commerce Dataset |
| **Nguồn** | [Kaggle](https://www.kaggle.com/datasets/zusmani/pakistans-largest-ecommerce-dataset) |
| **Quy mô** | 905,208 dòng × 26 cột |
| **Thời gian** | 2016 – 2018 |
| **Định dạng** | CSV (`Pakistan_Largest_Ecommerce_Dataset.csv`) |
 
**Các cột chính được sử dụng:**
 
| Cột | Kiểu | Mô tả |
|-----|------|-------|
| `increment_id` | string | Mã đơn hàng (unique per order) |
| `item_id` | string | Mã dòng sản phẩm (một đơn có thể có nhiều item) |
| `customer_id` | string | Mã khách hàng |
| `category_name_1` | string | Danh mục sản phẩm cấp 1 |
| `sku` | string | Mã SKU sản phẩm |
| `price` | double | Đơn giá sản phẩm |
| `qty_ordered` | double | Số lượng đặt hàng |
| `grand_total` | double | Tổng tiền thực thu (sau giảm giá) |
| `discount_amount` | double | Số tiền giảm giá; `grand_total + discount_amount` = giá gốc |
| `payment_method` | string | Phương thức thanh toán (`cod`, `easypay`, ...) |
| `status` | string | Trạng thái đơn hàng (`complete`, `canceled`, `order_refunded`, ...) |
| `order_year` | integer | Năm đặt hàng (có thể NULL) |
| `order_month` | integer | Tháng đặt hàng (có thể NULL) |
 
> **Lưu ý:** Chuỗi `\N` trong dataset được xử lý thành `null`. Các cột `Working Date`, `BI Status`, `MV`, `Year`, `Month`, `Customer Since`, `M-Y`, `FY` bị loại bỏ trong bước tiền xử lý. `payment_method` null được fill thành `"unknown"`.
 
---
 
## 🛠️ Công nghệ sử dụng
 
- **Apache Hadoop 3.4.3** — lưu trữ phân tán (HDFS)
- **Apache Spark / PySpark** — xử lý dữ liệu lớn
- **Spark SQL** — truy vấn phân tích
- **Spark MLlib** — mô hình phân loại LinearSVC
- **Python 3.9**
- **Jupyter Notebook** (chạy trong VS Code)
- **Java 8** (JDK 1.8)
- **Windows 10/11**
---
 
## 📁 Cấu trúc repo
 
```
BigData_Nhom3/
├── README.md
├── preprocessing.ipynb           # Tiền xử lý dữ liệu, xuất file sạch lên HDFS
├── sql_queries.ipynb             # 10 câu truy vấn Spark SQL phân tích bán hàng
└── LinearSVC_canceledorder.ipynb # Mô hình ML dự đoán đơn hàng bị hủy (LinearSVC)
```
 
---
 
## ⚙️ Yêu cầu môi trường
 
### Cài đặt cần có
 
- Java JDK 8 (`JAVA_HOME` đã cấu hình)
- Hadoop 3.4.3 (`HADOOP_HOME` đã cấu hình)
- Python 3.9
- PySpark (`pip install pyspark`)
- Jupyter (`pip install jupyter` hoặc dùng VS Code + Jupyter extension)
- matplotlib + seaborn (`pip install matplotlib seaborn`) — dùng cho Confusion Matrix
### Biến môi trường Windows (System Environment Variables)
 
```
JAVA_HOME   = C:\Program Files\Java\jdk1.8.0_xxx
HADOOP_HOME = D:\BigData\Java\hadoop-3.4.3
PATH        += %JAVA_HOME%\bin
PATH        += %HADOOP_HOME%\bin
PATH        += %HADOOP_HOME%\sbin
```
 
---
 
## 🚀 Hướng dẫn chạy
 
### Bước 1 — Khởi động Hadoop
 
Mở **Command Prompt với quyền Administrator**, chạy lần lượt:
 
```cmd
start-dfs.cmd
start-yarn.cmd
```
 
Kiểm tra HDFS đang chạy tại: [http://localhost:9870](http://localhost:9870)  
Kiểm tra YARN đang chạy tại: [http://localhost:8088](http://localhost:8088)
 
> ⚠️ Nếu `start-yarn.cmd` báo lỗi **"Access is denied"**, chạy lệnh sau rồi thử lại:
> ```cmd
> mkdir C:\tmp
> icacls C:\tmp /grant Everyone:(OI)(CI)F /T
> ```
 
### Bước 2 — Upload dataset lên HDFS
 
```cmd
hdfs dfs -mkdir -p /ecom
hdfs dfs -put D:\đường\dẫn\Pakistan_Largest_Ecommerce_Dataset.csv /ecom/
```
 
Kiểm tra file đã lên chưa:
 
```cmd
hdfs dfs -ls /ecom/
```
 
### Bước 3 — Chạy các notebook theo thứ tự
 
**1. `preprocessing.ipynb` — Tiền xử lý:**
- Đọc CSV gốc từ `hdfs://localhost:9000/ecom/Pakistan_Largest_Ecommerce_Dataset.csv`
- Làm sạch dữ liệu, ép kiểu, xử lý null, chuẩn hóa cột
- Xuất file sạch lên `hdfs://localhost:9000/ecom/ecom_clean_final`
**2. `sql_queries.ipynb` — Phân tích Spark SQL:**
- Đọc dữ liệu sạch từ `hdfs://localhost:9000/ecom/ecom_clean_final`
- Tạo Temp View `sales` với 13 cột đã chọn
- Thực hiện 10 câu truy vấn phân tích bán hàng
**3. `LinearSVC_canceledorder.ipynb` — Mô hình ML:**
- Đọc dữ liệu sạch từ `hdfs://localhost:9000/ecom/ecom_clean_final` (CSV, `multiLine=True`)
- Tạo cột `label`: `canceled` → `1.0`, còn lại → `0.0`
- Làm sạch lại cột số, huấn luyện mô hình LinearSVC
- Đánh giá mô hình và vẽ Confusion Matrix
---
 
## 🔄 Pipeline xử lý dữ liệu
 
```
HDFS: /ecom/Pakistan_Largest_Ecommerce_Dataset.csv
            │
            ▼
    [preprocessing.ipynb]
    1.  Đọc CSV (inferSchema=False)
    2.  Xóa cột rác (_c21–_c25) + dòng trắng
    3.  Xóa cột không cần thiết (Working Date, BI Status, MV, Year, Month, Customer Since, M-Y, FY)
    4.  Đổi tên "Customer ID" → customer_id
    5.  Chuyển "\N" → null
    6.  Xóa dòng null ở cột quan trọng (category_name_1, customer_id, grand_total, created_at, status)
    7.  Fill null cột phụ: payment_method → "unknown"
    8.  Xóa dòng trùng lặp
    9.  Ép kiểu số (price, qty_ordered, grand_total, discount_amount → double, xóa dấu phẩy)
    10. Lọc giá trị vô lý (price≥0, qty>0, grand_total≥0, discount≥0)
    11. Parse created_at → created_date (DateType), tạo order_year, order_month
    12. Xử lý ngoại lai bằng IQR (qty_ordered, grand_total)
            │
            ▼
    HDFS: /ecom/ecom_clean_final  (CSV)
            │
            ├──────────────────────────────────────┐
            ▼                                      ▼
    [sql_queries.ipynb]              [LinearSVC_canceledorder.ipynb]
    Tạo Temp View "sales"            Tạo label (canceled=1 / other=0)
    → 10 câu Spark SQL               Mã hóa category, payment_method
                                     → VectorAssembler → LinearSVC
                                     → Đánh giá: Accuracy, F1, AUC
```
 
---
 
## 📋 Danh sách 10 câu truy vấn Spark SQL
 
| # | Tên | Kỹ thuật SQL |
|---|-----|-------------|
| 1 | Doanh thu và sản lượng theo danh mục | `GROUP BY` + `COUNT DISTINCT`, `SUM`, `AVG` + `ORDER BY` |
| 2 | Top 10 SKU bán chạy nhất theo doanh thu | `GROUP BY` + `HAVING` + `LIMIT` |
| 3 | Doanh thu theo tháng (năm × tháng) | `GROUP BY` đa chiều (year + month) + `ORDER BY` |
| 4 | Tỷ lệ trạng thái đơn hàng | `CASE WHEN` + Aggregation + Window `SUM() OVER ()` |
| 5 | Đơn hàng giá trị cao bị hủy / hoàn trả | `WHERE` + Subquery (ngưỡng `AVG × 2`) + `CASE WHEN` |
| 6 | Top 10 khách hàng chi tiêu nhiều nhất | `WHERE` + `GROUP BY` + `ORDER BY` + `LIMIT` |
| 7 | Hiệu quả khuyến mãi theo danh mục | `CASE WHEN` phân nhóm % giảm giá + `GROUP BY` đa chiều + `revenue_per_discount` |
| 8 | Tăng trưởng doanh thu MoM | CTE + Window `LAG()` + tính % tăng trưởng |
| 9 | Top 3 danh mục theo doanh thu mỗi năm | Window `RANK() OVER (PARTITION BY year)` + Subquery |
| 10 | Hiệu quả phương thức thanh toán | `CASE WHEN` + Aggregation đa chiều + tỷ lệ hủy % |
 
---
 
## 🗂️ CATALOG — Temp View `sales`
 
| Cột | Kiểu | Ý nghĩa |
|-----|------|---------|
| `increment_id` | string | Mã đơn hàng — dùng `COUNT(DISTINCT increment_id)` để đếm đơn |
| `item_id` | string | Mã dòng sản phẩm — một đơn có thể có nhiều `item_id` |
| `customer_id` | string | Mã khách hàng |
| `category_name_1` | string | Danh mục sản phẩm cấp 1 |
| `sku` | string | Mã SKU sản phẩm |
| `price` | double | Đơn giá sản phẩm |
| `qty_ordered` | double | Số lượng đặt hàng |
| `grand_total` | double | Tổng tiền thực thu (sau giảm giá) |
| `discount_amount` | double | Số tiền giảm giá — `grand_total + discount_amount` = giá gốc |
| `payment_method` | string | Phương thức thanh toán (`cod`, `easypay`, ...) |
| `status` | string | Trạng thái đơn hàng (`complete`, `canceled`, `order_refunded`, ...) |
| `order_year` | integer | Năm đặt hàng — parse từ `created_at`, đã lọc null trong preprocessing |
| `order_month` | integer | Tháng đặt hàng — parse từ `created_at`, đã lọc null trong preprocessing |
 
---
 
## 🤖 Mô hình ML — LinearSVC dự đoán đơn hàng bị hủy
 
**Mục tiêu:** Dự đoán liệu một đơn hàng có bị hủy hay không dựa trên thông tin tại thời điểm đặt hàng.
 
**Nhãn (label):**
- `1` — đơn bị hủy (`status` chứa `"canceled"`)
- `0` — đơn bình thường (các trạng thái còn lại)
**Features sử dụng:**
 
| Feature | Loại | Xử lý |
|---------|------|-------|
| `category_name_1` | Categorical | `StringIndexer` → `category_index` |
| `payment_method` | Categorical | `StringIndexer` → `payment_index` |
| `price` | Numerical | Làm sạch, cast double |
| `qty_ordered` | Numerical | Làm sạch, cast double |
| `grand_total` | Numerical | Làm sạch, cast double |
| `discount_amount` | Numerical | Làm sạch, cast double |
| `order_year` | Numerical | Dùng trực tiếp |
| `order_month` | Numerical | Dùng trực tiếp |
 
**Cấu hình mô hình:** `LinearSVC(maxIter=50, regParam=0.1)` — train/test split 80/20, `seed=42`
 
**Đánh giá:** Accuracy, F1-Score, Precision, Recall, ROC-AUC + Confusion Matrix
 
---
 
## 🐛 Lỗi thường gặp
 
| Lỗi | Nguyên nhân | Cách fix |
|-----|-------------|----------|
| `RPC response has invalid length` | NameNode ở Safe Mode hoặc YARN crash | `hdfs dfsadmin -safemode leave` |
| `Access is denied` khi start-yarn | Windows chặn ghi vào `C:\tmp` | `mkdir C:\tmp` + `icacls C:\tmp /grant Everyone:(OI)(CI)F /T` |
| `No such file or directory` trên HDFS | Chưa upload file CSV | Chạy lệnh `hdfs dfs -put` ở Bước 2 |
| `Connection refused port 9000` | NameNode chưa chạy | Chạy lại `start-dfs.cmd` với quyền Admin |
| `InvalidFileException` khi đọc dữ liệu sạch | Đọc trực tiếp CSV gốc thay vì file đã xử lý | Đảm bảo chạy `preprocessing.ipynb` trước để tạo `ecom_clean_final` |
 
---
 
## 📌 Ghi chú
 
- Toàn bộ phân tích được thực hiện bằng **Spark SQL** và **PySpark API** trên một Temp View duy nhất `sales`
- Câu 7 dùng ngưỡng giảm giá theo **tỷ lệ %** (`discount_amount / (grand_total + discount_amount)`) thay vì giá trị tuyệt đối để chuẩn hóa công bằng giữa các danh mục có mức giá khác nhau
- Preprocessing loại bỏ ngoại lai bằng **IQR** cho `qty_ordered` và `grand_total` (không áp dụng cho `price`)
- `ecom_clean_final` được lưu dưới dạng **CSV** (không phải Parquet)
- HDFS path dùng trong project: `hdfs://localhost:9000/ecom/`
