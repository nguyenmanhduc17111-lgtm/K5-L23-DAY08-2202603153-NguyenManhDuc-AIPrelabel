# Báo cáo Day 08 — 2202603153 - Nguyễn Mạnh Đức

Bản nháp tổng hợp với trợ lý AI từ các file đầu ra và nhận xét người học cung cấp. Các mục ghi “cần xác nhận” chưa phải kết luận cá nhân đã chốt.

## 1. Model và recipe (Part 2–3)

| Lớp | Model | Ngưỡng | Cơ sở |
|---|---|---|---|
| person | YOLO26x | 0.35 | threshold_sweep trên gold; lượt thử r01–r02 |
| motorbike | YOLO26x | 0.50 | threshold_sweep trên gold |
| bicycle, car, bus, truck | YOLO26x | 0.30 | Mặc định: bicycle/car chưa đạt mục tiêu recall; bus/truck không có vật gold |
| three_wheeler | YOLOE-26s, prompt tuk-tuk/cyclo/cargo tricycle | 0.15 | r03 và threshold_sweep; r04 thử YOLOE-L để so sánh nhưng bộ all dùng S |

Lệnh gộp đã dùng:

```powershell
D:\day08\.venv\Scripts\python.exe scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "person=0.35,motorbike=0.5,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

SAM 2.1 Tiny được chọn để thử chạy CPU. Lượt r05 dùng box YOLO26x cho SAM, tạo 14 mask trên 2 ảnh, trung bình 9.52 giây/ảnh cho cả luồng. Đây không phải thời gian riêng của SAM.

YOLO26 gọi xích lô ở img_032 là bicycle với score 0.26; nâng ngưỡng làm mất box thay vì sửa lớp. YOLOE được thử để tìm lớp three_wheeler. Một box person score 0.42 ở img_032 bị mất khi tăng ngưỡng từ 0.25 lên 0.5. Các nhận xét có AI hỗ trợ đối chiếu ảnh; xem model_log.csv.

Ngưỡng được chọn và đánh giá trên cùng 4 ảnh gold nên số đo có thể lạc quan. Các lớp ít vật có kết luận yếu; không suy rộng từ mẫu nhỏ.

## 2. AI error profile



| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---:|---:|---|
| accepted | 441 | 90.7% | 486 box máy |
| edited | 7 | 1.4% | 486 box máy |
| deleted | 26 | 5.3% | 486 box máy |
| added | 29 | 6.1% | 478 đối tượng sau duyệt |






## 3. Thiên kiến tự động hóa và ảnh bẫy



| Loại | Bắt được |
|---|---|
| extra | 3/3 |
| missing | 2/3 |
| loose | 2/3 |
| wrong_class | 3/3 |

Hai ca script còn đánh dấu chưa đạt: img_009 (loose), img_023 (missing). Đối chiếu ảnh cho thấy nhãn tham chiếu có vấn đề: img_009 ghi bicycle cho xe máy; box gốc img_023 bao nhiều xe. Chưa sửa đáp án bẫy hoặc nhãn đúng để ép khớp.

## 4. Máy có đáng dùng không?

Kết luận cá nhân về việc dùng máy: cần người học xác nhận dựa trên thời gian và kết quả bẫy.

Ba nguyên tắc kiểm tra để giảm thiên kiến (cần người học xác nhận):

1. Ẩn nhãn máy và quét toàn ảnh trước khi hiện nhãn ở mỗi ảnh.
2. Rà mọi box để loại box thừa và chỉnh từng cạnh theo phần nhìn thấy; chú ý các lỗi extra và loose đã lọt.
3. Quét lại từng vùng ảnh để tìm vật bị sót, nghỉ sau khoảng 10 ảnh và lưu thời gian thực tế.

## 5. Mask SAM cho 3 vật



| Ảnh/vật | SAM | Điểm dương / âm | Nhận xét người học | File |
|---|---|---|---|---|
| img_003, xe ba bánh chở hàng | 2.1 Tiny | 3 / 0 | Dính người lái và người phía sau; phần hàng hóa chưa được xác nhận riêng | masks/img003_three_wheeler.jpg |
| img_013, tuk-tuk | 2.1 Tiny | 3 / 2 | Vẫn dính người lái và người phía sau dù thêm điểm âm | masks/img013_tuktuk.jpg |
| img_017, tuk-tuk | 2.1 Tiny | 2 / 0 | Người học đánh giá đúng | masks/img017_tuktuk.jpg |


## 6. Calibration

Có 3 quyết định trong qc/calibration_decisions.csv và phần bổ sung trong guidelines/guideline_v1.1_calibrated.md:

- CAL-01, img_005: chỉ thấy tay; nếu phần nhìn thấy dưới 20% người thì bỏ nhãn. Đây là áp dụng lại v1.0 sau hướng dẫn sai của trợ lý.
- CAL-02, img_028/029/030: xe sát nhau nhưng còn tách được thì gán riêng, tuân theo kích thước và che khuất. Trường hợp không tách được chưa được bổ sung ngưỡng cụ thể.
- CAL-03, img_002: xe bán tải bị YOLOE nhận nhầm three_wheeler, đổi sang car theo định nghĩa có sẵn.

img_002 và img_030 thuộc pilot, dùng làm ví dụ. CAL-01 và CAL-03 là sửa cách áp dụng/lỗi máy, không thay định nghĩa lớp hoặc taxonomy.rules. CAL-02 làm rõ một phần câu hỏi mở.

## 7. QC và quyết định

Nguồn qc/qc_sample.csv, qc/qc_review.csv và qc/qc_report_auto.md. Người học kiểm 5 ảnh ngẫu nhiên (003, 006, 014, 031, 035), 0 ảnh ca khó, seed 42; tổng 100 đối tượng. Người kiểm cũng là người review nên có hạn chế về tính độc lập.

Theo xác nhận của người học: lỗi đối tượng 0/100=0%; ảnh lỗi 0/5=0%. Không ghi nhận loại lỗi nào trong mẫu này. Sáu annotation vừa chỉnh thuộc ảnh 004, 008, 010, 018, 024, 025, đều ngoài mẫu QC này; phiếu QC của năm ảnh vẫn giữ nguyên.

Cận trên Wilson 95% cho tỷ lệ ảnh lỗi thật là khoảng 43%; mẫu này không chứng minh được ngưỡng 2%. Script kiểm định dạng và tính các số đo, không tự xác nhận nhãn ôm sát, đúng lớp, hoặc vật cả người lẫn máy bỏ sót.

**Đề xuất từ script: Release**, ngày 2026-11-17: mẫu QC 0/5 ảnh lỗi và nhãn hiện tại khớp 10/12 bẫy. Quyết định cuối cần người học xác nhận. 

## 8. Ghi chú cá nhân

Dùng trợ lý AI để kiểm định dạng, đối chiếu một số ảnh pilot, ghi các nhận xét được người học xác nhận. 

Máy: Windows, Ryzen 5 5500U, khoảng 16 GB RAM; Python 3.12, PyTorch CPU. Chạy model local; CVAT local bằng Docker.

Các dự đoán dùng trong phân tích được chạy bằng model local, không lấy từ data/cache/

Các vướng mắc: chạy lệnh sai thư mục; sweep dùng file pilot không trùng gold






