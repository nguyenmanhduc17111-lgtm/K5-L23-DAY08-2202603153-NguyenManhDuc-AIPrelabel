# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 486; đối tượng sau duyệt: 478; ngưỡng accepted: IoU >= 0.9
- Đã **loại 30 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 441 | 441/486 = 90.7% | 486 box máy |
| edited (sửa) | 7 | 7/486 = 1.4% | 486 box máy |
| deleted (xoá) | 26 | 26/486 = 5.3% | 486 box máy |
| added (vẽ thêm) | 29 | 29/478 = 6.1% | 478 đối tượng sau duyệt |

Trong số deleted + added, **7 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 22.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 247 | 3 | 2 | 9 |
| bicycle | 9 | 1 | 0 | 0 |
| motorbike | 110 | 0 | 0 | 9 |
| car | 56 | 0 | 4 | 3 |
| bus | 3 | 0 | 0 | 2 |
| truck | 1 | 0 | 2 | 4 |
| three_wheeler | 15 | 3 | 18 | 2 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 63 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 57 | 19 | 6 | 75.0% (KTC 95%: 64–83%) | 90.5% (KTC 95%: 81–96%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 10/12 = 83.3% (KTC 95%: 55–95%) (ngưỡng đạt: 80%).
  - extra: 3/3
  - missing: 2/3
  - loose: 2/3
  - wrong_class: 3/3
  - lọt: img_009.jpg (loose), img_023.jpg (missing)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
