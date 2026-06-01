# 🛍️ Phân Tích Hành Vi Mua Hàng Đa Kênh

> **So sánh hiệu quả kinh doanh giữa kênh In-Store, Mobile App và Online**

---

## 📌 Tóm Tắt Dự Án

Công ty X là doanh nghiệp bán lẻ hoạt động đồng thời trên ba kênh: cửa hàng truyền thống (**In-Store**), ứng dụng di động (**Mobile App**) và website thương mại điện tử (**Online**). Ban lãnh đạo muốn hiểu rõ sự khác biệt về hiệu quả giữa các kênh để đưa ra quyết định phân bổ nguồn lực và xây dựng chiến lược bán hàng phù hợp.

Dự án phân tích **120,000 giao dịch bán lẻ** trong giai đoạn **2024–2025**, nhằm trả lời bốn câu hỏi kinh doanh:

| # | Câu hỏi |
|---|---------|
| 1 | Kênh nào tạo ra doanh thu và giá trị đơn hàng (AOV) cao nhất? |
| 2 | Kênh nào phù hợp với từng phân khúc khách hàng (New, Loyal, Returning, VIP)? |
| 3 | Chính sách discount có thực sự làm tăng giá trị đơn hàng không? |
| 4 | Danh mục sản phẩm nào nên bán trên kênh nào? |

---

## 📂 Dataset

- **Nguồn:** [Kaggle](https://www.kaggle.com/datasets/noopurbhatt/retail-sales-dataset)
- **Quy mô:** 120,000 bản ghi · 17 cột
- **Thời gian:** 01/01/2024 – 30/12/2025
- **Kênh bán hàng:** In-Store · Mobile App · Online
- **Phân khúc khách hàng:** New · Loyal · Returning · VIP
- **Danh mục sản phẩm:** Beauty · Books · Clothing · Electronics · Groceries · Home · Sports · Toys

**Các cột chính được dùng trong phân tích:**

| Cột | Ý nghĩa |
|-----|---------|
| `transaction_id` | Mã giao dịch duy nhất |
| `transaction_date` | Ngày thực hiện giao dịch |
| `sales_channel` | Kênh bán hàng |
| `customer_segment` | Phân khúc khách hàng |
| `category` | Danh mục sản phẩm |
| `quantity` | Số lượng sản phẩm |
| `unit_price` | Đơn giá (USD) |
| `discount_pct` | Tỷ lệ giảm giá (%) |
| `sales_amount` | Doanh thu giao dịch (USD) |

---

## 🔧 Các Bước Thực Hiện (Methodology)

### 1. Data Cleaning

**Mục tiêu:** Chuẩn hóa dữ liệu thô trước khi phân tích.

**Các bước xử lý:**

- **Chuẩn hóa định dạng:** Phát hiện và xử lý hai định dạng ngày hỗn lẫn (`YYYY-MM-DD` và `DD/MM/YYYY`), chuẩn hóa về `datetime64`. Hợp nhất hai giá trị trùng nghĩa `Gift Card` / `Giftcard`.
- **Xử lý Missing Values:** Áp dụng kiểm định chi-square và so sánh trung bình để xác định loại missing (MCAR). Xử lý 6 cột có giá trị thiếu bằng các phương pháp phù hợp (điền `Unknown`, điền mode, tính lại theo công thức).
- **Kiểm tra Logic Nghiệp Vụ:** Phát hiện 40 bản ghi vi phạm (discount > 100%, quantity âm, sales_amount bất thường…). Gán flag thay vì xóa để phục vụ kiểm toán.
- **Xử lý Outliers:** Áp dụng IQR ở cả cấp độ global và context (theo nhóm `sales_channel × customer_segment`). Quyết định giữ nguyên outlier và tạo cột flag để theo dõi.

**Kết quả sau cleaning:**

| Chỉ số | Trước | Sau |
|--------|-------|-----|
| Tổng bản ghi phân tích | 120,000 | 119,960 |
| Missing values | 6 cột | 0 |
| Duplicate | 0 | 0 |
| Kiểu `transaction_date` | object | datetime64 |

**Output:** `retail_dataset_cleaned.csv` (có flag) · `retail_dataset_powerbi.csv` (đã lọc flag)

---

### 2. Exploratory Data Analysis (EDA)

**Công cụ:** Python · Pandas · Matplotlib · Seaborn

**Các phân tích thực hiện:**

- Tổng doanh thu & AOV theo kênh bán hàng
- Xu hướng doanh thu theo tháng (2024–2025)
- Heatmap doanh thu theo phân khúc khách hàng và kênh
- AOV có discount và không discount theo kênh
- Tỷ trọng doanh thu từng danh mục sản phẩm theo kênh

**Insights nổi bật từ EDA:**

- **Online dẫn đầu doanh thu** ($16.66M), nhưng **In-Store có AOV cao nhất** ($381.62) → mỗi kênh có thế mạnh riêng.
- **In-Store là kênh giữ chân:** 36% giao dịch từ nhóm Loyal. **Online & Mobile App là kênh thu hút mới:** ~40–41% giao dịch từ nhóm New.
- **Discount không làm tăng AOV** – đơn có discount thấp hơn đơn không discount ~$32 ở cả ba kênh.
- Mỗi danh mục sản phẩm có kênh bán phù hợp riêng: *Groceries/Books/Sports* → In-Store; *Clothing/Toys* → Mobile App; *Electronics/Home* → Online.

---

## 📊 Kết Quả & Insight Kinh Doanh

### Hiệu suất tổng thể các kênh

| Kênh | Doanh thu | Số đơn | AOV | Discount Rate |
|------|-----------|--------|-----|---------------|
| **Online** | $16,655,740 | 43,777 | $380.47 | 39.94% |
| **In-Store** | $15,933,950 | 41,753 | $381.62 | 40.35% |
| **Mobile App** | $12,757,340 | 34,430 | $370.53 | 39.75% |

### Phân khúc khách hàng theo kênh

| Phân khúc | In-Store | Mobile App | Online |
|-----------|----------|------------|--------|
| **New** | 14.08% | 40.29% | 41.97% |
| **Loyal** | 36.32% | 13.05% | 14.09% |
| **Returning** | 29.71% | 22.75% | 21.02% |
| **VIP** | 19.89% | 23.91% | 22.92% |

### Thế mạnh danh mục sản phẩm theo kênh

| Kênh | Danh mục mạnh nhất |
|------|--------------------|
| In-Store | Groceries (56.57%) · Books (49%) · Sports (42.90%) |
| Mobile App | Clothing (50.82%) · Toys (45.61%) |
| Online | Home (52.70%) · Electronics (43.93%) |

### Khuyến nghị hành động chính

1. **Giảm tỷ lệ đơn có discount từ ~40% xuống ~25%** — discount đang làm giảm AOV ~$32/đơn mà không tạo thêm giá trị.
2. **Ưu tiên đầu tư vào kênh Online** — dẫn đầu doanh thu và thu hút khách hàng mới hiệu quả nhất.
3. **Phân bổ ngân sách marketing đúng vai trò từng kênh** — không quảng cáo kéo khách mới cho In-Store.
4. **Phân bổ tồn kho theo thế mạnh danh mục** — mỗi kênh tập trung vào nhóm sản phẩm phù hợp.
5. **Chuẩn bị sớm cho mùa cao điểm Quý 4** — doanh thu tăng mạnh vào tháng 9–12 cả hai năm.

---

## 📈 Dashboard

Dashboard được xây dựng bằng **Power BI** với 3 trang báo cáo:

| Trang | Nội dung |
|-------|---------|
| **Tổng quan hiệu suất** | Doanh thu, AOV, số đơn, discount theo kênh và theo thời gian |
| **Phân khúc khách hàng** | Heatmap doanh thu, tỷ lệ giao dịch và AOV theo phân khúc × kênh |
| **Khuyến mãi & Danh mục** | AOV có/không discount; tỷ trọng doanh thu theo sản phẩm và kênh |

🔗 **[Xem Dashboard trực tuyến](https://app.powerbi.com/groups/me/reports/23f05379-91b1-44eb-a548-51f47e92a56e/0f07b47e6a0235f13e70?experience=power-bi)** 

---

## 🗂️ Cấu Trúc File

```
├── data/
│   ├── raw/
│   │        ├── retail_sales_dataset.csv         # Dữ liệu thô gốc (120,000 bản ghi)
│   ├── processed
│   │        ├── retail_dataset_cleaned.csv       # Dữ liệu đã làm sạch (có flag)
│   │        └── retail_dataset_powerbi.csv       # Dữ liệu dùng cho Power BI (đã lọc flag)
├── notebooks/
│   ├── data_cleaning.ipynb              # Toàn bộ quy trình làm sạch dữ liệu
│   └── EDA.ipynb                        # Tìm insight 
├── BaoCao.pbix                      
├── BaoCao.docx                      
└── README.md
```

---

---

## 👤 Liên hệ

Linkedin: [Le Van Long](linkedin.com/in/lê-văn-long-57231b325)
