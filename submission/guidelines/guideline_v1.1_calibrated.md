# Label contract — Phố châu Á — BỔ SUNG v1.1

Các quyết định dưới đây do người học xác nhận trong trao đổi và được trợ lý ghi lại. Bản này bổ sung v1.0; không thay đổi taxonomy.rules. Việc chốt quyết định không xác nhận rằng nhãn đã được sửa và export.

## 1. Câu hỏi mở đã chốt

| Câu hỏi | Quyết định | Ảnh ví dụ | case_id |
|---|---|---|---|
| Xe máy sát nhau nhưng vẫn tách được từng chiếc (mục 6 câu 2) | Gán từng xe nếu phân biệt được từng chiếc và đáp ứng điều kiện kích thước, che khuất của v1.0. Không dùng ignore_region chỉ vì xe nằm sát nhau. | img_028, img_029, img_030 | CAL-02 |

Quyết định chỉ áp dụng cho trường hợp tách được xe. Chưa bổ sung ngưỡng cho cụm xe lớn nhưng không tách được. Các câu hỏi mở khác ở mục 6 v1.0 chưa được chốt trong bản này. img_030 thuộc pilot và được dùng làm ví dụ.

## 2. Ca khó và cách áp dụng quy tắc hiện có

| Ảnh | Tình huống | Quyết định | Căn cứ | case_id |
|---|---|---|---|---|
| img_005 | Chỉ thấy tay người; trước đó người học vẫn gán person theo hướng dẫn sai của trợ lý | Không gán nếu phần nhìn thấy dưới 20% người; rà và bỏ box không đạt điều kiện. Không mặc định thấy tay là đủ gán person. | Mục 4.1.2 v1.0; không thay đổi quy tắc | CAL-01 |
| img_002 | YOLOE-S gán xe bán tải thành three_wheeler | Đổi sang car theo định nghĩa lớp. Dùng làm ví dụ để sửa lỗi tương tự khi review batch. | Mục 2 v1.0: bán tải thuộc car; không đổi taxonomy | CAL-03 |

img_002 thuộc pilot, không thuộc bộ batch_reviewed. Các thay đổi nhãn batch nếu có phải được sửa trong CVAT và export thành submission/labels/qc_fixed.json; giữ bản review ban đầu để bảo toàn kết quả đo bẫy.

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Bản giảng viên viết sẵn | Quy tắc gốc |
| v1.1 | Làm rõ cách gán cụm xe sát nhau khi từng xe vẫn tách được; ghi hai ca áp dụng lại quy tắc che khuất và phân lớp sẵn có | CAL-02; CAL-01 và CAL-03 là sửa cách áp dụng, không đổi ngưỡng hoặc định nghĩa lớp |
