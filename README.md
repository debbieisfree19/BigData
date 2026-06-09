# 📦 BigData Nhóm 3 — Phân tích Pakistan Largest E-Commerce Dataset

Dự án môn **Big Data** — phân tích bộ dữ liệu thương mại điện tử lớn nhất Pakistan bằng **Apache Hadoop HDFS** và **Apache Spark** (PySpark + Spark SQL) trên môi trường Windows (Jupyter Notebook / VS Code).

---

## 👥 Thành viên nhóm

| STT | Họ và tên | MSSV |
|-----|-----------|------|
| 1 | | |
| 2 | | |
| 3 | | |

---

## 📊 Dataset

| Thông tin | Chi tiết |
|-----------|---------|
| **Tên** | Pakistan Largest E-Commerce Dataset |
| **Nguồn** | [Kaggle](https://www.kaggle.com/datasets/zusmani/pakistans-largest-ecommerce-dataset) |
| **Quy mô** | 905,208 dòng × 26 cột |
| **Thời gian** | 2016 – 2018 |
| **Định dạng** | CSV (`Pakistan_Ecommerce.csv`) |

**Các cột chính:**

| Cột | Mô tả |
|-----|-------|
| `item_id` | ID sản phẩm |
| `increment_id` | ID đơn hàng |
| `Customer ID` | ID khách hàng |
| `created_at` | Ngày đặt hàng |
| `status` | Trạng thái đơn hàng |
| `price` | Giá sản phẩm |
| `qty_ordered` | Số lượng đặt |
| `grand_total` | Tổng tiền đơn hàng |
| `discount_amount` | Số tiền giảm giá |
| `category_name_1` | Danh mục sản phẩm |
| `payment_method` | Phương thức thanh toán |
| `MV` | Merchandise value |
| `Customer Since` | Ngày đăng ký tài khoản |

---

## 🛠️ Công nghệ sử dụng

- **Apache Hadoop 3.4.3** — lưu trữ phân tán (HDFS)
- **Apache Spark / PySpark** — xử lý dữ liệu lớn
- **Spark SQL** — truy vấn phân tích
- **Python 3.9**
- **Jupyter Notebook** (chạy trong VS Code)
- **Java 8** (JDK 1.8)
- **Windows 10/11**

---

## 📁 Cấu trúc repo

```
BigData_Nhom3/
├── README.md
├── preprocessing.ipynb       # Tiền xử lý dữ liệu, lưu kết quả lên HDFS
└── spark_analysis.ipynb      # Tiền xử lý + 10 câu truy vấn Spark SQL
```

---

## ⚙️ Yêu cầu môi trường

### Cài đặt cần có

- Java JDK 8 (`JAVA_HOME` đã cấu hình)
- Hadoop 3.4.3 (`HADOOP_HOME` đã cấu hình)
- Python 3.9
- PySpark (`pip install pyspark`)
- Jupyter (`pip install jupyter` hoặc dùng VS Code + Jupyter extension)

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
hdfs dfs -mkdir -p /user/hadoop/ecommerce
hdfs dfs -put D:\đường\dẫn\Pakistan_Ecommerce.csv /user/hadoop/ecommerce/
```

Kiểm tra file đã lên chưa:

```cmd
hdfs dfs -ls /user/hadoop/ecommerce/
```

### Bước 3 — Chạy notebook

Mở VS Code → mở file notebook → chạy từng cell theo thứ tự từ trên xuống.

**Nếu chạy `preprocessing.ipynb` trước:**
- File sẽ đọc CSV gốc, làm sạch, và lưu kết quả về `hdfs://localhost:9000/ecom/ecom_clean_csv`

**Nếu chạy `spark_analysis.ipynb`:**
- Notebook này tự xử lý dữ liệu và thực hiện 10 câu phân tích Spark SQL trong một file duy nhất

---

## 🔄 Pipeline xử lý dữ liệu (`spark_analysis.ipynb`)

```
HDFS: /user/hadoop/ecommerce/Pakistan_Ecommerce.csv
            │
            ▼
    1. Đọc CSV (nullValue="#N/A")
            │
            ▼
    2. Tiền xử lý
       ├── Chuẩn hóa tên cột (bỏ khoảng trắng)
       ├── Xóa cột Unnamed / _c21..._c25
       ├── Lọc dòng lỗi (item_id null, category lệch cột)
       ├── Ép kiểu số (price, qty_ordered, grand_total, discount_amount)
       ├── Parse ngày → order_date (DateType)
       ├── Tạo status_clean, is_successful, quarter
       └── Chuẩn hóa merchandise_value (xóa dấu phẩy, cast double)
            │
            ▼
    3. Tạo 4 Temp View
       ├── orders        (thông tin đơn hàng)
       ├── order_items   (chi tiết sản phẩm)
       ├── customers     (khách hàng, đã dedup)
       └── categories    (tổng hợp theo danh mục)
            │
            ▼
    4. 10 câu Spark SQL
```

---

## 📋 Danh sách 10 câu truy vấn Spark SQL

| # | Tên | Kỹ thuật | Ý nghĩa |
|---|-----|----------|---------|
| 1 | Thống kê doanh thu theo danh mục | GROUP BY + COUNT DISTINCT, SUM, AVG, MIN, MAX | Danh mục đóng góp doanh thu cao nhất và tỷ lệ giảm giá |
| 2 | Đơn hàng giá trị cao bị hủy/hoàn | WHERE + Subquery (ngưỡng AVG×2) + JOIN | Phát hiện đơn lớn thất thoát để CSKH xử lý |
| 3 | Xu hướng doanh thu theo tháng/quý | GROUP BY + SUM OVER (lũy kế YTD) | Tháng doanh thu đỉnh điểm, kế hoạch theo mùa vụ |
| 4 | Hành vi khách hàng mới vs cũ | JOIN 3 bảng + GROUP BY + CASE WHEN | So sánh chi tiêu và tỷ lệ hoàn thành theo nhóm |
| 5 | Top 5 danh mục từng năm + tăng trưởng YoY | RANK() OVER (PARTITION BY year) + LAG() | Nhận diện danh mục tăng trưởng hay suy giảm |
| 6 | Khách hàng VIP vượt chi tiêu trung bình | Nested Subquery 2 tầng + JOIN 3 bảng | Phân khúc khách hàng cao giá trị |
| 7 | Tỷ lệ hoàn thành theo phương thức thanh toán | GROUP BY + CASE WHEN + tỷ lệ % | Tối ưu phương thức thanh toán, giảm hủy đơn |
| 8 | Tăng trưởng doanh thu MoM | LAG() + SUM() OVER (ROWS BETWEEN) | Phát hiện tháng đột biến, hỗ trợ dự báo |
| 9 | Top 3 SKU bán chạy nhất từng danh mục | RANK() OVER (PARTITION BY category) + % đóng góp | Quản lý tồn kho, ưu tiên sản phẩm chủ lực |
| 10 | Hiệu suất danh mục so với trung bình toàn sàn | JOIN 3 bảng + AVG() OVER + CASE WHEN | Đánh giá danh mục vượt trội hay tụt hậu |

---

## 🗂️ 4 Temp View schema

**`orders`**
```
order_id, customer_id, order_date, year, month, quarter,
fiscal_year, status, is_successful, payment_method, bi_status
```

**`order_items`**
```
order_id, sku, category, price, qty_ordered,
grand_total, discount_amount, merchandise_value
```

**`customers`**
```
customer_id, customer_since
```

**`categories`**
```
category, total_items, avg_price, total_revenue
```

---

## 🐛 Lỗi thường gặp

| Lỗi | Nguyên nhân | Cách fix |
|-----|-------------|----------|
| `RPC response has invalid length` | NameNode ở Safe Mode hoặc YARN crash | Chạy `hdfs dfsadmin -safemode leave` |
| `Access is denied` khi start-yarn | Windows chặn ghi vào `C:\tmp` | `mkdir C:\tmp` + cấp quyền `icacls` |
| `No such file or directory` trên HDFS | Chưa upload file CSV | Chạy lệnh `hdfs dfs -put` ở Bước 2 |
| `Connection refused port 9000` | NameNode chưa chạy | Chạy lại `start-dfs.cmd` với quyền Admin |

---

## 📌 Ghi chú

- Toàn bộ code được viết bằng **PySpark API** và **Spark SQL**
- Dataset chứa một số dòng lỗi lệch cột (category bị ghi nhầm thành payment_method) — đã được lọc trong bước tiền xử lý
- Chuỗi `#N/A` trong dataset được xử lý thành `null` ngay khi đọc file
