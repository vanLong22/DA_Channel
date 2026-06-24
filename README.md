# Phân tích Hành vi Mua hàng Đa kênh (Omnichannel Retail Analytics)

## 1. Tổng quan Dự án
Dự án tập trung vào việc xử lý và phân tích dữ liệu kinh doanh đa kênh của **Công ty X** – một chuỗi bán lẻ thời trang, phong cách sống và công nghệ hàng đầu tại Việt Nam. Nhằm thích ứng với xu hướng hiện đại, công ty đã triển khai mô hình Bán hàng đa kênh (Omnichannel) qua 3 kênh chính: **Hệ thống cửa hàng vật lý (In-Store)**, **Ứng dụng di động (Mobile App)**, và **Trang thương mại điện tử (Online)**.

Sau 2 năm vận hành (2024–2025) với cùng một chính sách phân bổ thử nghiệm, dự án hiện bước vào giai đoạn **Tối ưu hóa chi phí và Chuyên môn hóa kênh bán**. Mục tiêu cốt lõi của nghiên cứu này là cung cấp các góc nhìn chuyên sâu dựa trên dữ liệu để giúp Ban giám đốc tái cấu trúc ngân sách marketing, định hình lại vai trò chiến lược của từng kênh nhằm chuẩn bị cho đà tăng trưởng tiếp theo.

## 2. Bài toán Kinh doanh (Business Problems)
Nghiên cứu này tiến hành phân tích sâu hành vi mua sắm trên tập dữ liệu gồm **120,000 giao dịch** để tìm câu trả lời thỏa đáng cho bốn bài toán kinh doanh lớn:
*   Kênh bán hàng nào đang mang lại hiệu quả cao nhất về doanh thu và giá trị trung bình trên một đơn hàng (AOV)?
*   Đặc trưng phân khúc khách hàng (*New, Loyal, Returning, VIP*) gắn liền với kênh mua sắm nào?
*   Ngành hàng/Danh mục sản phẩm nào tối ưu hiệu suất nhất khi phân phối trên từng kênh cụ thể?
*   Tác động thực tế của chính sách chiết khấu (Discount) lên giá trị đơn hàng như thế nào?

## 3. Dữ liệu & Quy trình Xử lý (Data & Pipeline)
*   **Dữ liệu đầu vào:** Tập dữ liệu gốc gồm 120,000 bản ghi chứa thông tin chi tiết về mã giao dịch, ngày mua, phân khúc khách hàng, danh mục sản phẩm, số lượng, giá bán, tỷ lệ giảm giá, doanh thu, và kênh phân phối.
*   **Nguồn:** [Kaggle](https://www.kaggle.com/datasets/noopurbhatt/retail-sales-dataset)
*   **Quy trình Xử lý dữ liệu (`data_cleaning.ipynb`):**
    *   *Chuẩn hóa định dạng:* Đồng bộ hóa cột ngày tháng từ hai định dạng hỗn hợp (`YYYY-MM-DD` và `DD/MM/YYYY`) về kiểu `datetime64` đồng nhất; chuyển đổi dữ liệu dạng chữ sang kiểu dữ liệu danh mục (`category`).
    *   *Xử lý giá trị thiếu:* Phát hiện dữ liệu thiếu hoàn toàn ngẫu nhiên (MCAR) ở các trường thông tin phụ và tiến hành điền giá trị `Unknown` hoặc giá trị xuất hiện phổ biến nhất (`mode`). Riêng cột doanh thu (`sales_amount`) thiếu được tính toán lại chính xác dựa trên công thức nghiệp vụ.
    *   *Kiểm tra logic nghiệp vụ:* Phát hiện và xử lý các bản ghi lỗi hệ thống (ví dụ: tỷ lệ giảm giá > 100%, đơn giá hoặc số lượng âm, doanh thu thực tế bị phóng đại 100 lần do lỗi dấu thập phân). Tổng cộng 40 bản ghi lỗi nghiêm trọng đã được gán flag và loại bỏ trước khi đưa vào phân tích, giữ lại 119,960 bản ghi sạch.
    *   *Xử lý giá trị ngoại lai (Outlier):* Sử dụng phương pháp khoảng liên phân vị (IQR) để gắn cờ theo dõi các đơn hàng có giá trị cao hoặc số lượng lớn đột biến mà không xóa bỏ, đảm bảo tính toàn vẹn của dữ liệu.

## 4. Khám phá Chính & Kiểm định Thống kê (Key Insights)
Sau khi áp dụng thống kê mô tả phối hợp với kỹ thuật đào sâu nguyên nhân (Drill-down why-why), dự án phát hiện các insight chiến lược sau:

### Hiệu suất Doanh thu & Đơn hàng theo kênh
*   **Online** đạt doanh thu cao nhất (**$16,655,740**) nhờ sở hữu số lượng giao dịch vượt trội (**43,777 đơn**), mặc dù giá trị trung bình đơn hàng ($380.47) thấp hơn In-Store.
*   **In-Store** đứng thứ hai về doanh thu ($15,933,950) nhưng dẫn đầu về giá trị trung bình trên một đơn hàng (AOV đạt **$381.62**).
*   **Mobile App** cho thấy hiệu suất kém nhất ở cả tổng doanh thu ($12,757,340) lẫn AOV ($370.53).

### Đặc trưng Phân khúc Khách hàng & Ngành hàng
*   **Kênh Online & Mobile App** đóng vai trò là phễu thu hút khách hàng mới (*New Customers*), chiếm lần lượt 41.97% và 40.29% cơ cấu doanh thu của kênh. Online bán chạy nhất đối với ngành hàng **Đồ gia dụng (Home)** và **Thiết bị điện tử (Electronics)**.
*   **Kênh In-Store** đóng vai trò giữ chân và duy trì lòng trung thành của khách hàng (*Loyal & Returning*) khi nhóm này đóng góp tới 65.9% lượng đơn hàng. Ngành hàng chủ lực thúc đẩy hành vi mua lặp lại tại cửa hàng là **Thực phẩm/Hàng tiêu dùng (Groceries)** và **Sách (Books)**.

### Các kiểm định thống kê sử dụng (`EDA.ipynb`)
*   **Kiểm định Kruskal-Wallis H & Mann-Whitney U:** Xác nhận sự khác biệt về số lượng đơn hàng theo tháng của Mobile App thấp hơn Online và In-Store một cách rõ rệt, có ý nghĩa thống kê.
*   **Kiểm định Chi-square (kèm Cramér's V):** Xác nhận mối quan hệ phụ thuộc chặt chẽ giữa phân khúc khách hàng và kênh lựa chọn mua sắm.

## 5. Khuyến nghị Hành động & Tác động Kỳ vọng
*   **Khuyến nghị 1 (Tối ưu Mobile App):** Triển khai chương trình cross-selling (gợi ý mua thêm) ngay tại bước thanh toán cho nhóm khách hàng mới khi mua các sản phẩm Thời trang, Đồ chơi, Thiết bị điện tử. 
    *   *Mục tiêu:* Thúc đẩy tăng trưởng 15% - 20% số lượng đơn hàng hằng tháng và kéo AOV của Mobile App tăng 10% - 15%.
*   **Khuyến nghị 2 (Mở rộng Kênh Online):** Duy trì thế mạnh thu hút khách hàng mới của các ngành hàng Thiết bị điện tử, Đồ gia dụng, kết hợp bổ sung chuỗi chiến dịch chăm sóc tự động sau mua (Email marketing, nhắc lịch mua lại).
    *   *Mục tiêu:* Gia tăng tỷ lệ giữ chân và chuyển đổi khách hàng mới thành khách hàng quay lại tăng từ 5% - 10% sau 6 tháng.
*   **Khuyến nghị 3 (Duy trì vị thế In-Store):** Ưu tiên diện tích trưng bày và tối ưu nguồn hàng tồn kho cho hai nhóm mặt hàng có nhu cầu mua lặp lại cao là Thực phẩm và Sách, áp dụng các chính sách tích điểm đặc quyền dành riêng cho khách thành viên trực tiếp.
    *   *Mục tiêu:* Giữ vững tỷ trọng đơn hàng của nhóm khách hàng trung thành ở mức tối thiểu 65.9% và tăng tần suất mua lặp lại của họ.
      
## 6. DashBoard 
*    **[Xem Dashboard trực tuyến](https://app.powerbi.com/links/Tnx_b9SZHw?ctid=e94fbe89-41e0-4857-b292-cfd8b9e613f0&pbi_source=linkShare&bookmarkGuid=4f3f38cd-7441-4f8b-ab4f-65595b86cec2)** 

## 7. Cấu trúc Thư mục Dự án
```text
├── data/
│   ├── raw/
│   │   └── retail_sales_dataset.csv        # Dữ liệu gốc chưa làm sạch (120,000 dòng)
│   ├── processed/
│   │   ├── retail_dataset_cleaned.csv      # Dữ liệu sau xử lý bao gồm các bản ghi có flag
│   │   └── retail_dataset_eda.csv          # Dữ liệu làm sạch, không có các bản ghi có flag, dùng cho EDA & Power BI
├── notebooks/
│   ├── data_cleaning.ipynb          # Notebook thực hiện toàn bộ quy trình làm sạch dữ liệu
│   └── EDA.ipynb                    # Notebook phân tích khám phá, tìm nguyên nhân, kiểm định thống kê
├── BaoCao.docx
├── BaoCao.pbix  
└── README.md                        
