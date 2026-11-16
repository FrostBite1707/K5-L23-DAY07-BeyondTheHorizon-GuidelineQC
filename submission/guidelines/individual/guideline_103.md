# Guideline gán nhãn — v0 (bản khởi đầu)

> **Đây là bản do giảng viên viết nhanh. Nó CHƯA đủ dùng.**
> Đừng sửa file này. Hãy copy sang `submission/guidelines/individual/` rồi sửa bản của bạn
> (xem `GUIDE.md`, Pha 1).

- Phiên bản: v0
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện** trên ảnh đường phố
(ví dụ cho camera giao thông hoặc xe tự lái).

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thuộc đúng một lớp)

| Lớp             | Định nghĩa                   |
| --------------- | ---------------------------- |
| `car`           | Ô tô con.                    |
| `truck`         | Xe tải.                      |
| `van`           | Xe thùng kín cỡ nhỏ.         |
| `motorcycle`    | Xe máy, mô tô.               |
| `tuktuk`        | Xe tuktuk                    |
| `bicycle`       | Xe đạp                       |
| `cycle`         | Xe xích lô                   |
| `rider`         | Người lái xe                   |
| `person`        | Người.                       |
| `ignore_region` | Vùng không cần gán từng vật. |

### Thuộc tính

| Thuộc tính       | Giá trị            | Ý nghĩa                                                                     |
| ---------------- | ------------------ | --------------------------------------------------------------------------- |
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated`      | `no`, `yes`        | Vật bị cắt ở mép ảnh.                                                       |
| `can_review`     | tick / không       | Tick khi bạn không chắc, để người khác xem lại.                             |

## 3. Quy tắc vẽ box

1. Gán box cho mọi người và phương tiện trong ảnh.
2. Box ôm sát vật.
3. Chọn đúng lớp và thuộc tính.
4. Không gán nếu ảnh quá mờ hoặc không nhìn rõ vật.
5. Chỉ gán vật, người thật thật, không gán mô hình, đồ chơi hoặc ảnh phản chiếu qua gương.
6. Không gán nếu có quá nhiều người/vật trong cùng khung hình.

## 4. Ca khó

| Ảnh     | Tình huống | Rule ID | Quyết định | Lý do |
| ------- | ---------- | ------- | ---------- | ----- |
| img_011 |   Xe mô hình         |     5    |     Không gán       |   Xe không phải xe thật    |
| img_014 |   Hình người đi xe bị nhòe         |     4    |     Không gán       |   Hình nhòe không nhìn rõ người và xe    |
| img_017 |   Tượng người cưỡi ngựa        |     5    |     Không gán       |   Tượng không phải người thật.    |
| img_019 |   Người quá đông       |     6    |     Không gán       |   Người quá nhiều không thể gán nhãn hết    |
| img_020 |   Ảnh phản chiếu qua gương       |     5    |     Không gán       |   Ảnh chiếu qua gương    |
| img_021 |   Quá nhiều người/phương tiện      |     6    |     Không gán       |   Nhiều người cùng đứng đợi đèn xanh    |
| img_038 |   Xe đồ chơi      |     5    |     Không gán       |   Xe đồ chơi    |

## 5. QC và phiên bản

- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline.
- Ca mơ hồ: tick `can_review`.
