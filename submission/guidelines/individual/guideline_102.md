# Guideline gán nhãn — <phiên bản> — <mã hoặc nhóm>

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: …
- Ngày: …
- Người viết / duyệt: …

## 1. Mục tiêu
Mô hình nào dùng nhãn này, để làm gì, sai ở đâu thì nguy hiểm.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con. | img_002.jpg | xe trưng bày |
| `truck` | Xe tải. | img_001.jpg | - |
| `van` | Xe thùng kín cỡ nhỏ. | img_005.jpg |
| `motorcycle` | Xe máy, mô tô. | img_003.jpg |xe đạp |
| `person` | Người. | img_002.jpg | tượng, manequim |
| `ignore_region` | Vùng không cần gán từng vật. | img_006.jpg | -|
| `Three-wheeler` | Xe ba bánh |img_003.jpg | - |


Vật **không thuộc lớp nào** thì làm gì (không gán? vùng ignore? lớp mới?).

### Thuộc tính
Mỗi thuộc tính: tên, **các mức cố định**, nghĩa từng mức, **ngưỡng cụ thể** (ví dụ che bao nhiêu % thì mức nào).
| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi bạn không chắc, để người khác xem lại. |

## 3. Quy tắc vẽ box
Box ôm sát phần nhìn thấy; vật bị che; vật bị cắt mép ảnh; phản chiếu / hình in / tượng / đồ chơi; xe chủ (ego); vật quá nhỏ; đám đông; một vật có nhiều phần (người và xe)…

## 4. Ca khó
| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| img_011 |   Xe mô hình         |     5    |     Không gán       |   Xe không phải xe thật    |
| img_017 |   Tượng người cưỡi ngựa        |     5    |     Không gán       |   Tượng không phải người thật.    |
| img_020 |   Ảnh phản chiếu qua gương       |     5    |     Không gán       |   Ảnh chiếu qua gương    |
| img_021 |   Quá nhiều người/phương tiện      |     6    |     Không gán       |   Nhiều người cùng đứng đợi đèn xanh    |
| img_038 |   Xe đồ chơi      |     5    |     Không gán       |   Xe đồ chơi    |

Nếu chưa chốt, đánh dấu `can_review` trên box hoặc ghi vấn đề cấp ảnh. Dùng mã quy tắc trong nhật ký QC.

## 5. QC và phiên bản
Ngưỡng QC áp dụng, ai quyết ca mơ hồ, lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
