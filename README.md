# Kiểm định và phân biệt chất lượng cảm quan và thành phần hóa lý nông sản theo vùng xuất xứ

Đề tài 12 — Học phần Phân tích Thống kê Nhiều Chiều (0101102556), HUIT.
Nhóm 09 — Khoa Khoa học Dữ liệu (Khóa 16DH).

Dự án dùng MANOVA 1 nhân tố để kiểm tra xem vùng trồng (quốc gia xuất xứ) của cà phê Arabica có tạo ra khác biệt có ý nghĩa thống kê về tổ hợp 6 chỉ tiêu chất lượng cảm quan và hóa lý hay không.

---

## 1. Câu hỏi nghiên cứu

Vector trung bình 6 chỉ tiêu chất lượng có khác nhau giữa bốn vùng trồng Mexico, Colombia, Guatemala và Brazil hay không?

- H₀: µ₁ = µ₂ = µ₃ = µ₄ (bốn vùng có cùng vector trung bình chất lượng)
- H₁: có ít nhất một cặp vùng khác nhau
- Mức ý nghĩa: α = 0,05

## 2. Dữ liệu

**Nguồn.** Dữ liệu đánh giá cà phê Arabica của Coffee Quality Institute (CQI), đăng trên Kaggle:

- Kaggle: https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi
- Repo thu thập dữ liệu gốc: https://github.com/jldbc/coffee-quality-database
- Dữ liệu được lấy từ trang CQI vào tháng 1/2018.

File dùng trong dự án: `data/raw/arabica_data_cleaned.csv` (1.311 dòng, 44 cột).

**Biến phụ thuộc (p = 6):**

| Biến trong dự án | Cột gốc | Ý nghĩa | Thang đo |
|---|---|---|---|
| Fragrance | Aroma | Hương thơm | 0–10 |
| Flavor | Flavor | Mùi vị | 0–10 |
| Aftertaste | Aftertaste | Hậu vị | 0–10 |
| Acidity | Acidity | Độ chua | 0–10 |
| Body | Body | Độ đậm đà | 0–10 |
| Moisture | Moisture | Độ ẩm hạt | Thập phân |

**Nhân tố:** `Origin` (cột gốc `Country.of.Origin`), giữ bốn vùng có đủ mẫu: Mexico, Colombia, Guatemala, Brazil.

## 3. Quy trình xử lý dữ liệu

Thực hiện trong `src/01_data_cleaning.ipynb`:

1. **Lọc vùng trồng:** giữ 4 vùng trên, còn 732 quan sát.
2. **Moisture = 0:** 129 mẫu có độ ẩm bằng 0. Độ ẩm 0% không phù hợp với cà phê nhân (thường khoảng 9–13%), nên được coi là giá trị thiếu và chuyển thành missing trước khi loại outlier. Đây là quyết định phân tích dựa trên lập luận vật lý, chưa được xác minh từ tài liệu gốc của nguồn dữ liệu.
3. **Loại outlier đa chiều:** khoảng cách Mahalanobis D², ngưỡng χ²(6; 0,001) ≈ 22,46. Loại 21 quan sát.
4. **Kết quả:** 582 quan sát, không có giá trị thiếu.

| Vùng trồng | Số mẫu | Tỷ lệ |
|---|---|---|
| Mexico | 221 | 38,0% |
| Guatemala | 165 | 28,4% |
| Colombia | 100 | 17,2% |
| Brazil | 96 | 16,5% |

**Lưu ý về Moisture = 0.** Các số 0 không phân bố ngẫu nhiên: 77 trên 183 mẫu Colombia (42%) và 62 trên 177 mẫu giống Caturra có giá trị này. Nhóm có số 0 cũng có điểm cảm quan cao hơn trung bình. Vì vậy việc loại số 0 có thể làm thiên lệch so sánh giữa các vùng. Ảnh hưởng này được kiểm tra bằng ba kịch bản (xem mục 6).

## 4. Phương pháp

- **Đặc trưng mẫu:** vector trung bình x̄, ma trận hiệp phương sai S, ma trận tương quan R, định thức |S| (`03_manova.ipynb`).
- **Kiểm tra giả định:**
  - Henze-Zirkler, toàn bộ dữ liệu và từng vùng (`02_assumption_checks.ipynb`).
  - Box's M, kiểm tra đồng nhất ma trận hiệp phương sai (`02_assumption_checks.ipynb`).
  - Mardia, skewness và kurtosis (`06_sensitivity.ipynb`).
- **Kiểm định chính:** MANOVA 1 nhân tố, bốn tiêu chuẩn: Pillai's trace (chỉ số chính), Wilks' Λ, Hotelling-Lawley, Roy. Giá trị F tới hạn ở mức 5%.
- **Hậu kiểm:** ANOVA đơn biến hiệu chỉnh Holm, so sánh cặp Tukey (`03_manova.ipynb`).
- **Trực quan hóa và phân loại:** boxplot, biểu đồ trung bình, heatmap, pairplot, PCA và LDA với elip tin cậy 95%, kiểm định chéo LDA (`04_visualization.ipynb`, `05_confidence_ellipses.ipynb`).
- **Độ bền:** so sánh MANOVA theo ba kịch bản xử lý Moisture (`06_sensitivity.ipynb`).

Chỉ số chính là **Pillai's trace** vì Box's M bác bỏ giả định đồng nhất hiệp phương sai, và tiêu chuẩn này bền vững hơn khi giả định bị vi phạm.

## 5. Kết quả chính

**Kiểm tra giả định (vi phạm ở cả hai):**
- Henze-Zirkler toàn bộ dữ liệu: HZ = 2,740, p ≈ 3,5 × 10⁻¹⁶⁸. Bác bỏ chuẩn đa biến; các vùng cũng bác bỏ (p từ 5 × 10⁻⁹ đến 2 × 10⁻³⁶).
- Mardia: skewness và kurtosis đều p < 0,001 ở toàn bộ dữ liệu và từng vùng.
- Box's M: χ² = 286,52, df = 63, p ≈ 3,0 × 10⁻³⁰. Bác bỏ đồng nhất hiệp phương sai.

Với cỡ mẫu lớn các kiểm định này khá nhạy, nên nên đọc như chỉ báo về mức độ sai lệch.

**MANOVA (n = 582):**

| Tiêu chuẩn | Giá trị | F | df | F tới hạn (5%) | p-value |
|---|---|---|---|---|---|
| **Pillai's trace (chính)** | **0,4767** | **18,11** | **18; 1725** | **1,61** | **≈ 5,1 × 10⁻⁵³** |
| Wilks' Λ | 0,5824 | 18,97 | 18; 1621 | 1,61 | ≈ 2,2 × 10⁻⁵⁵ |
| Hotelling-Lawley | 0,6169 | 19,60 | 18; 1140 | 1,61 | rất nhỏ |
| Roy's greatest root | 0,3142 | 30,11 | 6; 575 | 2,11 | rất nhỏ |

Cả bốn tiêu chuẩn đều bác bỏ H₀ ở mức 5%: vùng trồng có ảnh hưởng có ý nghĩa thống kê đến vector chất lượng.

**Hậu kiểm:**
- ANOVA đơn biến (Holm): cả 6 biến đều khác biệt có ý nghĩa. Moisture có η² cao nhất (0,188), tiếp theo là Aftertaste (0,137) và Body (0,108).
- Tukey: 23 trên 36 cặp khác biệt có ý nghĩa ở mức 5%. Mexico thấp hơn cả ba vùng còn lại ở Flavor, Body và Fragrance, và thấp hơn Brazil và Colombia ở Aftertaste. Colombia và Brazil khác biệt không có ý nghĩa ở Flavor và Aftertaste, dù số trung bình có khác nhau.

**Trực quan hóa và phân loại:**
- PCA: hai thành phần đầu giải thích 83,4% phương sai (PC1 = 75,7%, PC2 = 7,7%). Elip của các vùng chồng lấp mạnh.
- LDA: LD1 = 50,9%, LD2 = 46,9%. Elip tách một phần, vẫn chồng lấp đáng kể.
- Kiểm định chéo LDA (5 lần, phân tầng): độ chính xác 56,9% ± 2,0%, so với 38,0% khi đoán vùng nhiều mẫu nhất. Cải thiện có ý nghĩa nhưng chưa đủ để phân loại xuất xứ trong thực tế.

## 6. Kiểm tra độ bền

`06_sensitivity.ipynb` so sánh ba cách xử lý Moisture:

| Kịch bản | n | Số biến | Pillai | p (Pillai) |
|---|---|---|---|---|
| A. Giữ Moisture = 0 | 720 | 6 | 0,4467 | 1,5 × 10⁻⁶² |
| **B. Moisture = 0 là missing (kết quả chính)** | **582** | **6** | **0,4767** | **5,1 × 10⁻⁵³** |
| C. Bỏ hẳn Moisture | 721 | 5 | 0,3312 | 5,7 × 10⁻⁴⁵ |

Vùng trồng có ảnh hưởng có ý nghĩa ở cả ba kịch bản, nên kết luận chính không phụ thuộc vào cách xử lý Moisture. Tuy nhiên, các so sánh liên quan đến Moisture thay đổi rõ giữa các kịch bản, nên không nên dùng kết quả Moisture để đưa ra kết luận về vùng trồng.

## 7. Cấu trúc thư mục

```text
Nhom_09_Detai12_PTTKNC/
├── data/
│   ├── raw/
│   │   └── arabica_data_cleaned.csv          # Dữ liệu gốc (1.311 dòng, 44 cột)
│   └── processed/
│       ├── clean_data.xlsx                   # Dữ liệu đã làm sạch (582 quan sát)
│       ├── assumption_results.xlsx           # Henze-Zirkler, Box's M
│       ├── manova_results.xlsx               # MANOVA, F tới hạn, ANOVA, Tukey, S, R, trung bình
│       └── sensitivity_results.xlsx          # Mardia, so sánh ba kịch bản
├── figures/                                  # 17 hình PNG
├── notebooks/
│   └── main.ipynb                            # Chạy lần lượt 01 → 06
├── src/
│   ├── 01_data_cleaning.ipynb
│   ├── 02_assumption_checks.ipynb
│   ├── 03_manova.ipynb
│   ├── 04_visualization.ipynb
│   ├── 05_confidence_ellipses.ipynb
│   └── 06_sensitivity.ipynb
├── requirements.txt
└── README.md
```

## 8. Hướng dẫn chạy

### 8.1. Chuẩn bị

- Cài **Python 3.10 trở lên** (khuyến nghị 3.12 hoặc 3.13).
- Trên VS Code, cài hai extension: **Python** và **Jupyter** (của Microsoft).
- Lấy mã nguồn: clone repo hoặc giải nén `Nhom_09_Detai12_full.zip`, rồi mở đúng thư mục gốc `Nhom_09_Detai12` trong VS Code (**File → Open Folder**).

### 8.2. Cài thư viện

Mở terminal trong VS Code (**Terminal → New Terminal**) đang đứng ở thư mục gốc, rồi chạy:

```powershell
python -m pip install -r requirements.txt
```

Kiểm tra cài đặt thành công:

```powershell
python -c "import pandas, statsmodels, pingouin, openpyxl, sklearn; print('Đã cài đủ thư viện')"
```

Nếu lệnh in ra `Đã cài đủ thư viện`, bạn có thể chuyển sang bước 8.3.

### 8.3. Chạy các notebook theo thứ tự

Chạy từng notebook trong thư mục `src/` theo đúng thứ tự dưới đây:

1. **Mở `src/01_data_cleaning.ipynb`.** Ở góc trên bên phải chọn kernel là interpreter Python bạn đã cài (ví dụ `Python 3.13`). Bấm **Run All**.
2. **Mở `src/02_assumption_checks.ipynb`**, chọn kernel, bấm **Run All**.
3. **Mở `src/03_manova.ipynb`**, chọn kernel, bấm **Run All**.
4. **Mở `src/04_visualization.ipynb`**, chọn kernel, bấm **Run All**.
5. **Mở `src/05_confidence_ellipses.ipynb`**, chọn kernel, bấm **Run All**.
6. **Mở `src/06_sensitivity.ipynb`**, chọn kernel, bấm **Run All**.

Notebook 01 phải chạy trước, vì nó tạo `data/processed/clean_data.xlsx` mà các notebook sau cần đọc. Nếu bạn chạy lại 01, cần chạy lại 02 đến 06.

Không cần sửa đường dẫn trong code: notebook tự xác định thư mục gốc khi được chạy từ `src/`. Nếu bạn chạy notebook từ một thư mục khác, kiểm tra lại mục 8.6.

### 8.4. Kết quả cần kiểm tra sau mỗi bước

| Notebook | Kết quả đúng phải thấy |
|---|---|
| 01 | Sau khi xử lý: **582 dòng**; cột `Moisture` **không có giá trị 0**; có dòng "Số mẫu có Moisture = 0 (coi là missing): 129" |
| 02 | Henze-Zirkler toàn bộ: HZ ≈ 2,74, p ≈ 3,5e-168; Box's M: χ² ≈ 286,5, df = 63, p ≈ 3,0e-30 |
| 03 | Pillai's trace ≈ 0,4767, F ≈ 18,11, p ≈ 5,1e-53 (chỉ số chính); Tukey: 23 cặp có ý nghĩa |
| 04 | Lưu được `figures/pairplot.png` và các hình boxplot, mean, heatmap |
| 05 | Lưu được `confidence_ellipse_pca.png` và `confidence_ellipse_lda.png`; LDA: LD1 ≈ 50,9%, LD2 ≈ 46,9% |
| 06 | Mardia: p < 0,001 ở tất cả các nhóm; bảng so sánh ba kịch bản; lưu được `sensitivity_results.xlsx` |

Nếu số liệu lệch khỏi bảng trên, thường do chưa chạy 01 trước hoặc `clean_data.xlsx` cũ. Xem mục 8.6.

### 8.5. Kiểm tra sau khi chạy xong

- `data/processed/` có đủ bốn file: `clean_data.xlsx`, `assumption_results.xlsx`, `manova_results.xlsx`, `sensitivity_results.xlsx`.
- `figures/` có 17 file PNG.
- Mở `clean_data.xlsx`: 582 dòng, cột Moisture không có số 0.

### 8.6. Xử lý lỗi thường gặp

| Lỗi | Nguyên nhân | Cách khắc phục |
|---|---|---|
| `WinError 4551: An Application Control policy has blocked this file` | Windows chặn việc chạy nbconvert từ bên trong notebook (qua `subprocess`) | Không chạy `notebooks/main.ipynb`. Mở từng notebook trong `src/` và bấm Run All theo thứ tự 01 → 06 |
| `ModuleNotFoundError: No module named 'pingouin'` (hoặc `statsmodels`, `openpyxl`, `sklearn`) | Chưa cài đủ thư viện, hoặc VS Code đang dùng interpreter khác | Chạy lại mục 8.2. Trong VS Code, chọn đúng interpreter bằng **Ctrl+Shift+P → Python: Select Interpreter** |
| Kernel không khởi động hoặc không chọn được kernel | Thiếu `ipykernel` | Chạy `python -m pip install ipykernel`, rồi khởi động lại VS Code |
| `FileNotFoundError: ... clean_data.xlsx` | Chưa chạy 01, hoặc chạy 02 trước 01 | Chạy lại 01, rồi chạy các notebook sau |
| `FileNotFoundError: ... 06_sensitivity.ipynb` (hoặc tên notebook khác) | Thư mục `src/` thiếu file, hoặc đang dùng bản zip cũ | Copy đủ 6 notebook từ bản mới vào `src/` |
| Số liệu khác bảng trên | `clean_data.xlsx` còn bản cũ có Moisture = 0 | Chạy lại 01 để tạo lại file đã làm sạch |
| Chạy notebook nhưng không tìm thấy file dữ liệu | Mở VS Code ở thư mục con, không phải thư mục gốc | Dùng **File → Open Folder** và chọn thư mục `Nhom_09_Detai12` |

### 8.7. Chạy lại từ đầu

Nếu muốn tái lập hoàn toàn: xóa các file trong `data/processed/` và `figures/`, rồi chạy lại 01 → 06 theo mục 8.3. Dữ liệu gốc trong `data/raw/` không bị thay đổi.

### 8.8. Về `notebooks/main.ipynb`

`main.ipynb` là notebook tiện ích, gọi nbconvert qua `subprocess` để chạy 01 → 06 liên tiếp. Trên máy có chính sách Application Control (như lỗi 4551 ở trên) hoặc khi Python nằm trong thư mục người dùng, cách này có thể bị chặn. Khi đó hãy dùng cách chạy từng notebook ở mục 8.3, vẫn cho kết quả giống nhau.

## 9. Hạn chế

- **Giả định bị vi phạm:** chuẩn đa biến và đồng nhất hiệp phương sai đều bị bác bỏ. Pillai's trace được chọn vì bền vững hơn, nhưng p-value vẫn chỉ mang tính gần đúng.
- **Moisture = 0:** được xử lý như dữ liệu thiếu dựa trên lập luận vật lý, chưa xác minh từ nguồn. Các số 0 tập trung ở Colombia và giống Caturra, và nhóm này có chất lượng cao hơn, nên việc loại chúng tạo ra thiên lệch lựa chọn. Có 13 mẫu có độ ẩm dưới 0,05 cũng đáng ngờ nhưng chưa bị loại.
- **Mẫu không ngẫu nhiên:** dữ liệu là các mẫu được chấm điểm trong cơ sở dữ liệu CQI, không phải mẫu ngẫu nhiên theo vùng trồng.
- **Nhóm không cân bằng:** từ 96 đến 221 mẫu mỗi vùng. Colombia giảm từ 183 xuống 100 mẫu sau khi xử lý số 0 và loại outlier.
- **Biến nhiễu chưa kiểm soát:** phương pháp chế biến, độ cao trồng, giống cây và năm thu hoạch. Khác biệt giữa các vùng có thể phản ánh các yếu tố này.

## 10. Hướng phát triển

- Xác minh bản chất của số 0 trong Moisture với nguồn dữ liệu gốc (repo jldbc/coffee-quality-database).
- Thử kịch bản thay số 0 bằng giá trị trung vị theo vùng hoặc theo giống.
- Bổ sung biến nhiễu và mở rộng sang MANOVA đa nhân tố.
- Áp dụng PERMANOVA để không phụ thuộc vào giả định chuẩn đa biến.
- Xây dựng mô hình phân loại có kiểm định trên tập dữ liệu độc lập.

## 11. Môi trường

Các kết quả trong README được tính bằng pingouin 0.7.0 và statsmodels 0.15.0 trên môi trường Linux, Python 3.12. Nhóm chạy trên Windows với Python 3.13. Các phiên bản thư viện có thể khác nhau ở chữ số thập phân.

## 12. Tài liệu tham khảo

- Lê, H. K. S. (2026). *Bài giảng Phân tích thống kê nhiều chiều*. HUIT.
- Johnson, R. A., & Wichern, D. W. (2007). *Applied multivariate statistical analysis* (6th ed.). Pearson.
- Coffee Quality Institute. Coffee Quality Database from CQI (dữ liệu thu thập 2018). Kaggle: https://www.kaggle.com/datasets/volpatto/coffee-quality-database-from-cqi
- Đầy đủ danh mục tham khảo có trong báo cáo `Nhom_09_PTTKNC_BaoCao.docx`.
