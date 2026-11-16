# Guideline gán nhãn — v1

> **Đây là bản do giảng viên viết nhanh. Nó CHƯA đủ dùng.**
> Đừng sửa file này. Hãy copy sang `submission/guidelines/individual/` rồi sửa bản của bạn
> (xem `GUIDE.md`, Pha 1).

- Phiên bản: v1
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện** trên ảnh đường phố
(ví dụ cho camera giao thông hoặc xe tự lái).

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thuộc đúng một lớp)

| Lớp | Định nghĩa |
|---|---|
| `car` | Ô tô con. |
| `truck` | Xe tải. |
| `van` | Xe thùng kín cỡ nhỏ. |
| `motorcycle` | Xe máy, mô tô. |
| `person` | Người. |
| `ignore_region` | Vùng không cần gán từng vật. |
| `bus` | Xe buýt, xe khách, minibus trên khoảng 16 chỗ. |
| `bicycle` | Xe đạp và xích lô (xe ba bánh đạp chân), kể cả xe đạp điện có bàn đạp. Gán cả khi không có người. |
| `three_wheeler` | Xe ba bánh có động cơ: tuk-tuk, xe lam, xe lôi, xe ba gác máy. |
| `other_vehicle` | Phương tiện di chuyển được nhưng không khớp lớp nào ở trên: máy kéo, xe công nông, xe golf, xe địa hình (ATV), scooter điện đứng lái. |

### Thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi bạn không chắc, để người khác xem lại. |

**Làm rõ thuộc tính (bổ sung):**

- `occluded_level` tính theo **phần vật còn nhìn thấy**: `0` ≥ 90%; `1` 60–90%; `2` 25–60%; `3` < 25% (gần như khuất, chỉ nhận ra nhờ chi tiết đặc trưng).
- `occluded_level` là do **vật khác** che (người, xe, cột, hàng rào, hàng hóa). Vật bị mép ảnh cắt thì dùng `truncated`, không dùng `occluded_level`. Một vật có thể có cả hai.
- `truncated = yes` khi **bất kỳ phần nào** của vật nằm ngoài mép ảnh.
- Nếu dùng ô `occluded` có sẵn của CVAT thì tick khi `occluded_level` ≥ 2, để hai chỗ không mâu thuẫn.
- `ignore_region` để thuộc tính mặc định (`0`, `no`), chỉ tick `can_review` khi cần.
- Tick `can_review` khi: không chắc lớp (ví dụ `van` hay `car`, `bus` hay `van`, `motorcycle` hay `bicycle` với xe điện); ranh giới mờ do tối, mờ chuyển động hoặc ngược sáng; không phân biệt được vật thật với mô hình hoặc tượng; gặp tình huống chưa có trong mục 4. Không tick tràn lan: nếu tick quá nhiều, hãy xem lại guideline thay vì tick.

## 3. Quy tắc vẽ box

1. Gán box cho mọi người và phương tiện trong ảnh.
   - Một vật = đúng một box; không vẽ trùng, không vẽ một box ôm nhiều vật.
   - Vật có cạnh dài **< 20 px**: không gán (nếu nằm trong cụm đông thì thuộc về `ignore_region`).
   - Vật nhìn thấy **< 15%** và không nhận ra được loại (ví dụ chỉ thấy cái gương xe): không gán. Nhìn thấy < 25% nhưng nhận ra chắc chắn: vẫn gán, `occluded_level = 3`.
   - Vật không thật (tượng, hình, mô hình, phản chiếu): không gán. Ảnh không có vật hợp lệ nào thì **để trống**, không ép gán.
   - Người ngồi trong khoang **kín**, chỉ thấy qua kính: không gán. Người ở khoang **hở** (xe diễu hành mui trần, tuk-tuk, thùng xe tải, xích lô): gán nếu thấy rõ ≥ 25%.
   - `ignore_region` dùng cho: (a) đám đông hoặc dãy xe máy, xe đạp đỗ dày đặc không tách được từng vật: vẽ 1–3 box bao phần cụm không tách được, các vật ở tiền cảnh tách được vẫn gán riêng; (b) vật mờ chuyển động mạnh không nhận ra loại: box bao vùng mờ, tick `can_review`. Không dùng `ignore_region` để né một vật khó đơn lẻ (gán lớp gần nhất + `can_review`), và không dùng cho phương tiện "không có lớp".
2. Box ôm sát vật.
   - Sai lệch mỗi cạnh tối đa khoảng 2 px. Box ôm **phần nhìn thấy**, không ước đoán phần bị che.
   - Vật bị cắt ở mép: cạnh box nằm sát mép ảnh, không vẽ ra ngoài ảnh.
   - Ô tô, xe tải, van, bus, xe ba bánh: tính thân xe, gương, bánh, đèn; không tính ăng-ten, bóng đổ, phản chiếu trên mặt đường.
   - Xe máy: tính thân xe, bánh trước và sau, tay lái, gương, yên, sidecar; không tính thân trên người ngồi.
   - Xe đạp, xích lô: tính khung, bánh, tay lái, yên, giỏ gắn cố định, ghế khách của xích lô; không tính thân trên người đạp hoặc người ngồi.
   - Người: tính cơ thể thấy được, mũ bảo hiểm, nón, khẩu trang, ba lô đeo trên người; không tính quang gánh, hàng xách tay, ô, vật dài vác trên vai, xe người đó đang ngồi.
   - Hàng nằm trong thân xe hoặc thùng xe thì tính; hàng chằng, xếp hoặc nhô ra **ngoài thân xe** (bao hàng chất cao, ống dài) thì không tính.
   - Xe đầu kéo + rơ moóc đang nối liền: gộp thành **1 box** `truck`.
   - Người lái và người ngồi sau (xe máy, xe đạp, xe ba bánh, xích lô): **mỗi người một box `person`**, xe một box riêng; các box được phép chồng nhau. Người chỉ thấy nửa thân trên (do bị che) vẫn một box ôm phần thấy.
3. Chọn đúng lớp và thuộc tính.
   - Đi lần lượt: (1) có phải vật thật không? không thì không gán; (2) có phải người hoặc phương tiện di chuyển được không? không thì không gán; (3) chọn lớp theo mục 2, phương tiện không khớp lớp nào thì `other_vehicle`; (4) đủ lớn và đủ thấy không (cạnh dài ≥ 20 px, thấy ≥ 15% hoặc nhận ra chắc chắn)? không thì không gán; (5) tách riêng được không? không thì `ignore_region`; (6) vẽ box, điền `occluded_level` và `truncated`.
   - Giữa hai lớp mà không chắc: chọn lớp gần nhất, tick `can_review`.
   - Khi hai quy tắc mâu thuẫn, ưu tiên theo thứ tự các bước ở trên, rồi mới đến ca cụ thể ở mục 4.

## 4. Ca khó

Cột "Ví dụ" trỏ tới ảnh trong bộ `images/`.

**A. Phân loại phương tiện**

| Tình huống | Ví dụ | Cách gán |
|---|---|---|
| Xe chở ô tô (xe đầu kéo có tầng) | img_002 | 1 box `truck` cho cả tổ hợp. Mỗi xe được chở nhìn thấy ≥ 25% thì gán thêm `car` với `occluded_level` phù hợp. |
| Tuk-tuk, xe lam, xe ba bánh có động cơ | img_004, 016, 035, 040 | `three_wheeler`. Người lái và hành khách thấy rõ có box `person` riêng. Tuk-tuk có biển "taxi" vẫn là `three_wheeler`. |
| Xích lô (đạp chân chở khách) | img_023 | `bicycle`, box ôm cả xe lẫn ghế khách. Người đạp và khách là `person` riêng. |
| Xe đạp (có hoặc không người) | img_003, 006, 029, 034, 039 | `bicycle` + `person` nếu có người. |
| Xe máy có sidecar | img_022 | 1 box `motorcycle` ôm cả xe và sidecar. |
| Xe máy hoặc xe ba bánh chở hàng cồng kềnh, vật dài | img_029, 035 | Box chỉ ôm thân xe, không kéo dài theo hàng nhô ra ngoài. |
| Taxi 4 bánh | img_006, 040 | `car` (hoặc lớp thực tế của xe). Mui hoặc biển taxi tính vào box. |
| Phương tiện lạ (máy kéo, xe công nông, xe golf, ATV, scooter đứng lái) | — | `other_vehicle`. Xe đẩy tay, xe nôi, xe lăn tay không phải phương tiện: không gán. |

**B. Người**

| Tình huống | Ví dụ | Cách gán |
|---|---|---|
| Người lái và người ngồi sau xe máy | img_005, 006, 008, 010, 021 | Mỗi người một box `person`, mỗi xe một box `motorcycle`. |
| Người ngồi trên xe máy đang đỗ | img_010 | `person` + `motorcycle`. |
| Người gánh hàng, xách hàng | img_003 | Box `person` không gồm quang gánh và hàng. Nón lá tính vào người. |
| Người ở khoang hở | img_004, 019 | `person` nếu thấy rõ. |
| Đám đông đứng sát nhau | img_019, 032 | Người tách được và cao ≥ 25 px: gán riêng. Phần còn lại: `ignore_region`. |
| Người mặc đồ hóa trang, linh vật | img_019 | Người thật bên trong thấy rõ: `person`. Linh vật là hình nộm hoặc đồ vật: không gán. Không chắc thì `can_review`. |
| Người bị mép ảnh cắt | img_016, 040 | `person` + `truncated = yes`. |
| Người mờ do chuyển động | img_014 | Người rõ ở tiền cảnh: `person`. Người và xe mờ ở nền: `ignore_region` + `can_review`. |

**C. Không phải vật thật**

| Tình huống | Ví dụ | Cách gán |
|---|---|---|
| Tượng người hoặc tượng thú | img_011, 017, 028 | Không gán. Tượng không phải `person`. |
| Công trình hình xe (bia mộ hình xe tải) | img_027 | Không gán. |
| Hình nộm | img_033 | Không gán. |
| Hình người trên áp phích, pano, tranh tường, ảnh dán xe | img_018, 030, 031, 033, 040 | Không gán. |
| Xe đồ chơi, xe mô hình trong tủ | img_038 | Không gán. |
| Xe cỡ thật dù là xe cổ, xe trưng bày, xe diễu hành | img_019, 024 | Gán theo lớp thực tế (thường là `car`). Xe mô hình thu nhỏ đặt trên nóc xe: không gán; không phân biệt được cỡ thật hay mô hình thì gán + `can_review` (img_024). |
| Phản chiếu: gương chiếu hậu, mặt đường ướt, kính cửa hàng | img_020, 015, 031 | Không gán. |
| Ảnh không có vật hợp lệ nào | img_017, 020, 033, 038 | **Để trống**, không ép gán. Ảnh trống là dữ liệu hợp lệ. |

**D. Mật độ và điều kiện ảnh**

| Tình huống | Ví dụ | Cách gán |
|---|---|---|
| Dòng xe máy đông, xe máy đỗ dày | img_021, 030, 008 | Gán từng xe và từng người ở tiền cảnh (thấy ≥ 25%, cạnh dài ≥ 20 px). Phần chen chúc phía sau: `ignore_region`. |
| Vật nhỏ, ở xa | img_013, 025, 031, 032, 039 | Cạnh dài ≥ 20 px: gán. Nhỏ hơn: không gán (hoặc vào `ignore_region` nếu trong cụm). |
| Ảnh tối, ngược sáng, ảnh trắng đen | img_007, 008, 013, 022, 031 | Gán như bình thường. Không nhận ra được loại thì bỏ hoặc tick `can_review`. |
| Vật sau hàng rào, cột | img_018 | Vẫn gán, `occluded_level` theo phần còn thấy. |

**E. Cắt ở mép và bộ phận**

| Tình huống | Ví dụ | Cách gán |
|---|---|---|
| Xe bị mép ảnh cắt | img_012, 015, 040 | Box ôm phần thấy, `truncated = yes`. |
| Chỉ thấy một bộ phận (gương xe, góc thân xe) | img_033, 040 | Dưới 15% hoặc không nhận ra loại: không gán. |

## 5. QC và phiên bản

- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline.
- Ca mơ hồ: tick `can_review`.
- **Tự kiểm trước khi nộp (bổ sung):** mỗi ảnh đã xem lại một lượt đầy đủ, kể cả phóng to vùng nhỏ và đông; không còn người hay phương tiện hợp lệ chưa gán (đặc biệt người ngồi sau xe máy, xe bị che, xe đạp, xe buýt ở nền); không có box trùng hoặc box ôm hai vật; không gán nhầm tượng, hình, mô hình, phản chiếu; mọi box đều có lớp, `occluded_level`, `truncated` đúng; số ca tick `can_review` hợp lý.
- **Kiểm chéo (bổ sung):** lấy ngẫu nhiên 10–20% ảnh để người thứ hai gán độc lập. Hai box được coi là khớp khi **cùng lớp và IoU ≥ 0.7**. Ca lệch lớp, hoặc IoU < 0.5, hoặc thiếu hay thừa box: họp lại, thống nhất cách gán, ghi vào mục 4 và tăng phiên bản.
- **Rà lại khi tăng phiên bản (bổ sung):** nhãn đã gán theo phiên bản cũ phải được rà lại cho các ca bị ảnh hưởng.
- **Cấu hình CVAT (bổ sung):** danh sách label trong project gồm 10 lớp ở mục 2 và phải giữ **cùng thứ tự** giữa các thành viên, nếu không ID lớp trong file COCO xuất ra sẽ bị lệch. 6 lớp gốc giữ nguyên thứ tự của v0; 4 lớp mới (`bus`, `bicycle`, `three_wheeler`, `other_vehicle`) được thêm vào sau.
- **Ranh giới lớp cần thống nhất (bổ sung):** bán tải (pickup) là `truck`; xích lô đạp chân là `bicycle`, tuk-tuk có động cơ là `three_wheeler`; ranh giới `bus` / `van` ở khoảng 16 chỗ; xe máy điện dạng xe máy là `motorcycle`, xe đạp điện có bàn đạp là `bicycle`, scooter đứng lái là `other_vehicle`; xe đẩy tay, xe nôi, xe lăn tay không được coi là phương tiện.

| Phiên bản | Ngày | Thay đổi |
|---|---|---|
| v0 | — | Bản khởi đầu của giảng viên: 6 lớp, 3 thuộc tính, 3 quy tắc vẽ box, mục ca khó để trống. |
| v1 | 16/11/2026 | Thêm 4 lớp (`bus`, `bicycle`, `three_wheeler`, `other_vehicle`) để mọi phương tiện đều có lớp; làm rõ ranh giới lớp và thuộc tính; thêm ngưỡng kích thước và mức nhìn thấy; bổ sung chi tiết cho 3 quy tắc vẽ box và quy tắc `ignore_region`; điền mục ca khó (34 ca từ 40 ảnh mẫu); thêm quy trình QC. |
