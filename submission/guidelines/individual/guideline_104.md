# Guideline gán nhãn — v0.1 — 104

> Bản guideline cá nhân của thành viên 104, được xây dựng độc lập trong Pha 1 dựa trên việc khảo sát toàn bộ 40 ảnh trong tập dữ liệu (`data/images/img_001.jpg` đến `img_040.jpg`).

- Phiên bản: v0.1 (Bản cá nhân khởi tạo)
- Ngày: 2026-11-16
- Người viết: Thành viên 104

---

## 1. Mục tiêu
- **Mục đích sử dụng**: Bộ nhãn bounding box (hộp chữ nhật 2D) được sử dụng để huấn luyện và đánh giá mô hình nhận diện vật thể phục vụ hệ thống tự lái (Autonomous Driving) và hệ thống camera giám sát giao thông đô thị thông minh.
- **Mức độ rủi ro**: Sai sót nghiêm trọng nhất trong môi trường tự lái là **bỏ sót (False Negative)** người đi bộ (`person`), người điều khiển xe máy (`motorcycle`), hoặc ước lượng sai ranh giới phương tiện dẫn đến nguy cơ va chạm giao thông. Ngược lại, báo sai (False Positive) vào các tác phẩm điêu khắc hoặc hình vẽ quảng cáo sẽ làm xe phanh gấp vô cớ.
- **Yêu cầu kỹ thuật**: Box phải ôm sát phần thực tế nhìn thấy được (visible pixels), đúng lớp và xác định chính xác các thuộc tính mức độ che khuất (`occluded_level`), cắt mép ảnh (`truncated`) và cờ nghi ngờ (`can_review`).

---

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thể thuộc đúng một lớp)

| Lớp | Định nghĩa (một câu) | Ví dụ minh họa | Phản ví dụ / Dễ nhầm với (Không gán) |
|---|---|---|---|
| `car` | Xe ô tô con từ 4 đến 7 chỗ (sedan, SUV, hatchback, coupe) và xe bán tải (pickup) không có nắp thùng cao. | Xe con gia đình, xe taxi trên đường (`img_001`, `img_003`, `img_010`). | Xe mô hình / đồ chơi trong tủ kính (`img_038`), tượng xe bán tải (`img_027`). |
| `truck` | Xe tải chở hàng, xe chuyên dụng cỡ lớn (xe ben, bồn, đầu kéo) có cabin tách biệt với thùng hàng. | Xe tải chở hàng (`img_001`), xe tải chuyên dụng 2 sàn chở ô tô (`img_002`). | Xe bán tải nhỏ (`car`), xe van thùng kín (`van`). |
| `van` | Xe ô tô thùng kín liền khối cỡ nhỏ hoặc vừa, chuyên dùng chở hàng nhẹ hoặc chở khách 9–16 chỗ. | Xe van giao hàng, xe cứu thương cỡ nhỏ (`img_008`, `img_025`). | Xe buýt lớn (`bus`), xe tải thùng rời (`truck`). |
| `bus` | *(Đề xuất thêm)* Xe chở khách công cộng cỡ lớn (> 16 chỗ ngồi) chạy theo tuyến hoặc du lịch. | Xe buýt nội đô, xe khách liên tỉnh (`img_009`, `img_031`, `img_037`, `img_040`). | Xe minivan chở khách nhỏ (`van`). |
| `tuk_tuk` | *(Đề xuất thêm)* Xe cơ giới ba bánh có cabin mở dùng chở khách hoặc chở hàng tại các đô thị châu Á. | Xe tuk-tuk chở khách, xe lôi máy 3 bánh (`img_004`, `img_016`, `img_035`). | Xe máy hai bánh (`motorcycle`), ô tô 4 bánh (`car`). |
| `motorcycle` | Xe mô tô, xe gắn máy 2 bánh có động cơ (xăng hoặc điện). | Xe máy Honda/Yamaha của người đi đường (`img_001`, `img_005`, `img_026`). | Xe đạp không động cơ (`img_006`, `img_029`, `img_039`), xe máy điện trẻ em đồ chơi. |
| `person` | Con người thật đang tham gia giao thông (đi bộ, đứng bên đường, hoặc đang điều khiển/ngồi trên phương tiện). | Người đi bộ qua đường, tài xế lái xe máy/xe đạp (`img_001`, `img_005`). | Tượng đồng (`img_011`), tượng đá người cưỡi ngựa (`img_017`), ma-nơ-canh (`img_033`), chân dung trên áp phích/biển quảng cáo (`img_018`). |
| `ignore_region` | Vùng không gán từng vật: cụm người/xe ở quá xa (chiều cao < 20 px) hoặc đám đông chen chúc không thể tách box rõ ràng. | Đám đông dày đặc ở hậu cảnh xa, đoàn xe ùn tắc ở chân trời. | Các đối tượng đứng độc lập nhìn rõ có kích thước >= 20 px. |

*Lưu ý về vật không thuộc lớp nào*: Xe đạp không động cơ, xe đẩy tay không thuộc đối tượng gán nhãn trong bảng taxonomy; nếu người điều khiển xe đạp xuất hiện rõ ràng thì chỉ gán box `person` cho người đó, không gán box xe cho xe đạp (hoặc đưa cả cụm vào `ignore_region` nếu bị che khuất phức tạp).

### Thuộc tính

1. **`occluded_level`** (Mức độ che khuất - đo bằng tỷ lệ diện tích nhìn thấy được so với toàn bộ hình thể ước tính):
   - `0` (Không bị che): Vật thể trọn vẹn, nhìn thấy > 90% diện tích bề mặt.
   - `1` (Che nhẹ): Bị che khuất từ 10% đến 30% diện tích (ví dụ: bị người khác che một phần bánh xe hoặc đuôi xe).
   - `2` (Che vừa/nhiều): Bị che khuất từ 31% đến 70% diện tích bởi vật thể khác hoặc chướng ngại vật trên đường.
   - `3` (Che nghiêm trọng): Bị che khuất > 70% diện tích nhưng vẫn còn đủ đặc trưng nhận dạng chắc chắn (đầu xe, nửa thân trên người). Nếu bị che > 90% không còn nhận ra được thì không gán.
2. **`truncated`** (Cắt mép ảnh):
   - `no`: Vật thể nằm trọn vẹn 100% bên trong giới hạn 4 cạnh của khung hình.
   - `yes`: Vật thể bị cắt cụt một phần bởi ít nhất một mép ảnh (trái, phải, trên, dưới).
3. **`can_review`** (Cờ đánh dấu ca nghi vấn):
   - `false` (mặc định): Tự tin về lớp và vị trí box.
   - `true`: Tick chọn khi có bất kỳ sự mơ hồ nào về ranh giới box, phân loại lớp hoặc tình huống biên chưa có quy tắc rõ ràng để người kiểm duyệt (reviewer) rà soát lại.

---

## 3. Quy tắc vẽ box

1. **Nguyên tắc ôm sát phần nhìn thấy (Tight Visible Bounding Box)**:
   - Box chỉ bao quanh phần pixel thực tế nhìn thấy được của vật thể. Không phóng to box ra ngoài không gian trống để đoán phần bị che khuất.
2. **Quy tắc Người đi xe máy (Person on Motorcycle)**:
   - Gán **01 box `person`**: Bao trọn phần cơ thể nhìn thấy được của người ngồi trên xe (đầu, thân, tay, chân).
   - Gán **01 box `motorcycle`**: Bao trọn toàn bộ xe máy nhìn thấy được. Phần box xe máy được phép giao nhau (overlap) với phần box của người ngồi trên xe.
3. **Quy tắc Xe chuyên dụng chở xe (`img_002.jpg`)**:
   - Gán **01 box `truck`** cho toàn bộ khung xe chở (bao gồm đầu kéo và dàn khung 2 tầng).
   - Gán riêng **từng box `car`** cho từng chiếc ô tô con được chở trên các tầng của sàn xe, vì mỗi chiếc xe con này vẫn là thực thể vật lý mà hệ thống camera tự lái cần nhận diện để phòng tránh va chạm.
4. **Quy tắc Hàng hóa chất trên phương tiện (`img_035.jpg`)**:
   - Bounding box phương tiện chỉ ôm sát phần thân vỏ cứng của phương tiện.
   - Tuyệt đối không kéo box phình to ra để ôm các kiện hàng, bao tải hàng chất cồng kềnh vượt quá khổ khung xe.
5. **Quy tắc Vật thể nhân tạo / Không thật (False Positives)**:
   - **Tượng điêu khắc**: Tượng danh nhân cưỡi ngựa (`img_017.jpg`), tượng bán tải trang trí (`img_027.jpg`), tượng đồng (`img_011.jpg`) -> **Không gán box**.
   - **Hình in / Quảng cáo**: Hình ảnh người trên biển quảng cáo tấm lớn (`img_018.jpg`) -> **Không gán box**.
   - **Hình phản chiếu**: Hình ảnh phương tiện hoặc người qua gương chiếu hậu (`img_020.jpg`) hoặc mặt kính tòa nhà -> **Không gán box**.
   - **Đồ chơi / Ma-nơ-canh**: Ma-nơ-canh thời trang (`img_033.jpg`), ô tô đồ chơi mô hình trong tủ kính (`img_038.jpg`) -> **Không gán box**.
6. **Ngưỡng kích thước tối thiểu (Min Box Size)**:
   - Bỏ qua hoặc đưa vào `ignore_region` mọi vật thể độc lập có chiều cao hoặc chiều rộng nhỏ hơn **20 pixel**.

---

## 4. Bảng ca khó phát hiện trong 40 ảnh

| Ảnh | Tình huống cụ thể | Rule đề xuất | Quyết định của 104 | Lý do chi tiết |
|---|---|---|---|---|
| `img_002.jpg` | Xe tải 2 tầng chuyên dụng chở 8 ô tô con | `R-CARRIER` | Gán 1 `truck` cho xe chở + 8 box `car` riêng cho 8 xe con trên sàn | Xe con trên sàn là phương tiện thật, có hình dáng hoàn chỉnh; hệ thống tự lái cần nhận diện để tránh va chạm. |
| `img_004.jpg` | Xe ba bánh chở khách tuk-tuk | `R-TUKTUK` | Gán lớp `tuk_tuk` (nếu nhóm duyệt) hoặc tạm gán `motorcycle` | Xe 3 bánh có động cơ không thể coi là ô tô con (`car`) do kết cấu và kích thước khác biệt. |
| `img_009.jpg`, `img_040.jpg` | Xe buýt chở khách công cộng cỡ lớn | `R-BUS` | Gán lớp `bus` (nếu nhóm duyệt) hoặc tạm gán `truck` | Xe buýt kích thước lớn, nếu gán `van` sẽ sai lệch định nghĩa xe nhỏ. |
| `img_017.jpg` | Tượng đá danh nhân cưỡi ngựa trên quảng trường | `R-STATUE` | **Không gán** box nào cho cả người và ngựa | Là tác phẩm điêu khắc nghệ thuật cố định, không phải thực thể sống tham gia giao thông. |
| `img_018.jpg` | Chân dung người trên biển quảng cáo lớn ven đường | `R-ADPRINT` | **Không gán** box `person` | Là hình in 2D trên biển bảng tĩnh, gán nhãn sẽ khiến mô hình nhận diện nhầm chướng ngại vật ảo. |
| `img_020.jpg` | Xe ô tô phản chiếu trong gương chiếu hậu xe chủ | `R-REFLECTION` | **Không gán** box `car` | Là ảo ảnh phản xạ quang học, không phải vật thể vật lý trước mặt camera. |
| `img_027.jpg` | Tượng xe bán tải bằng đá đặt trang trí bên đường | `R-STATUE` | **Không gán** box `car` | Là tác phẩm điêu khắc bằng đá tĩnh, không phải phương tiện di chuyển. |
| `img_033.jpg` | Ma-nơ-canh mặc quần áo trong tủ kính bên vỉa hè | `R-MANNEQUIN` | **Không gán** box `person` | Ma-nơ-canh vô tri trong cửa hàng thời trang, không phải người đi bộ. |
| `img_035.jpg` | Xe tuk-tuk chở khối bao tải hàng nhô cao | `R-OVERLOAD` | Gán box `tuk_tuk` chỉ ôm sát thân xe, không ôm khối hàng | Tránh làm sai lệch kích thước chuẩn của phương tiện giao thông. |
| `img_038.jpg` | Xe ô tô đồ chơi mô hình trưng bày trong tủ kính | `R-TOY` | **Không gán** box `car` | Đồ chơi mô hình thu nhỏ, không tham gia giao thông. |


---

## 5. QC và phiên bản

- Quy trình kiểm soát: Mọi ca mơ hồ khi thực hiện gán nhãn cá nhân ở Pha 3 sẽ được bật cờ `can_review = true` để đưa ra thảo luận tại buổi họp Calibration ở Pha 5.
- Lịch sử thay đổi:

| Phiên bản | Thay đổi chính | Lý do / Dựa trên ca khó |
|---|---|---|
| `v0` | Bản khung sơ sài ban đầu từ giảng viên. | Chưa xử lý được xe chở xe, tuk-tuk, xe buýt, tượng, hàng quá khổ. |
| `v0.1` | Bản cá nhân của thành viên 104: định lượng ngưỡng thuộc tính %, quy tắc người đi xe máy, xe chở xe, loại bỏ tượng/đồ chơi/quảng cáo, đề xuất thêm lớp `bus` và `tuk_tuk`. | Khảo sát thực tế 40 ảnh (`img_001` đến `img_040`). |
