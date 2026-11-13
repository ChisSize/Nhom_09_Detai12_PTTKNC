# ĐỀ TÀI 12: Kiểm định và phân biệt chất lượng cảm quan và thành phần hóa lý nông sản theo vùng xuất xứ

## 1. Thông tin chung
- **Đề tài:** Đánh giá, kiểm định và phân biệt chất lượng cảm quan và thành phần hóa lý nông sản theo vùng xuất xứ (Origin).
- **Mục tiêu:** Khảo sát xem các mẫu nông sản (cụ thể là cà phê) từ các vùng trồng khác nhau có sự khác biệt có ý nghĩa thống kê về mặt chất lượng hay không.
- **Phương pháp chính:** MANOVA 1 nhân tố (Nhân tố: Vùng trồng - Origin), Tiêu chuẩn Wilks’ Λ (Wilks' Lambda), Phân tích thành phần chính (PCA), Phân tích phân biệt tuyến tính (LDA) và Vẽ Elip phân tán tin cậy (Confidence Ellipses).

## 2. Mô tả Dữ liệu
Dữ liệu đầu vào tập trung vào 6 biến phụ thuộc (Vector biến $p = 6$) phản ánh chất lượng cảm quan và hóa lý của mẫu nông sản:
1. `Fragrance` (Hương thơm)
2. `Flavor` (Mùi vị)
3.   `Aftertaste` (Hậu vị)
4. `Acidity` (Độ chua)
5. `Body` (Độ đậm đà)
6. `Moisture` (Độ ẩm)

Biến độc lập (Nhân tố): `Origin` (Vùng trồng: Mexico, Colombia, Guatemala, Brazil, v.v.)

## 3. Cấu trúc Thư mục (Project Structure)
```text
Nhom_09_Detai12/
├── data/
│   ├── raw/                  # Dữ liệu gốc: arabica_data_cleaned.csv
│   └── processed/            # clean_data.xlsx và các bảng kết quả
├── figures/
│   ├── boxplots/             # Boxplot từng biến theo Origin
│   ├── means/                # Biểu đồ trung bình từng biến và biểu đồ tổng hợp
│   ├── correlations/         # Heatmap tương quan
│   └── confidence_ellipses/  # Elip tin cậy PCA và LDA
├── notebooks/
│   └── main.ipynb            # Chạy toàn bộ pipeline
├── src/                      # Các notebook phân tích theo từng bước
│   ├── 01_data_cleaning.ipynb
│   ├── 02_assumption_checks.ipynb
│   ├── 03_manova.ipynb
│   ├── 04_visualization.ipynb
│   └── 05_confidence_ellipses.ipynb
├── requirements.txt
└── README.md
```

Ảnh được lưu theo loại trong các thư mục con của `figures/`; chạy lại notebook sẽ cập nhật ảnh tương ứng trong từng thư mục.

## 4. Hướng dẫn chạy code
Yêu cầu hệ thống: Đã cài đặt Python 3 và Jupyter Notebook.
Để đảm bảo code chạy không gặp lỗi thiếu thư viện, hãy cài đặt các gói cần thiết bằng lệnh:
```bash
pip install -r requirements.txt
```
*(Đảm bảo thư viện `openpyxl` đã được cài đặt để Pandas có thể đọc/ghi định dạng Excel).*

**Cách chạy khuyến nghị:** mở `notebooks/main.ipynb` và chạy toàn bộ notebook. Notebook này tự chạy các bước dưới đây theo đúng thứ tự.

**Thứ tự chạy thủ công các file trong thư mục `src/`:**
1. Mở và chạy `01_data_cleaning.ipynb` để làm sạch dữ liệu từ thư mục `raw` sang thư mục `processed`.
2. Mở và chạy `02_assumption_checks.ipynb` để kiểm tra các giả định tiền đề trước khi chạy MANOVA.
3. Mở và chạy `03_manova.ipynb` để tính toán các thống kê kiểm định (đặc biệt là Wilks' Lambda) và xuất kết quả ra file Excel.
4. Mở và chạy `04_visualization.ipynb` để vẽ các biểu đồ phân bố và so sánh trung bình cơ bản.
5. Mở và chạy `05_confidence_ellipses.ipynb` để phân tích (PCA & LDA) và vẽ biểu đồ Elip tin cậy 95%, từ đó kết luận sự tách biệt chất lượng giữa các vùng trồng.

## 5. Kết luận chính (Tóm tắt)
- **Kiểm định MANOVA:** Giá trị p-value của kiểm định Wilks' Lambda rất nhỏ ($p < 0.05$), nên ta bác bỏ giả thuyết $H_0$. Có thể kết luận vùng trồng (Origin) thực sự tạo ra sự khác biệt có ý nghĩa thống kê lên toàn bộ tập hợp 6 biến chất lượng.
- **Trực quan hóa:** Các biểu đồ Elip phân tán tin cậy (đặc biệt là trên không gian LDA) cho thấy sự phân tách khá rõ rệt giữa các cụm vùng trồng, củng cố thêm kết luận của MANOVA.
