# Guideline gán nhãn — v1.0 — Thống nhất nhóm

> Bản đặc tả chung v1.0 đã được toàn bộ các thành viên trong nhóm (101, 102, 103, 104) thảo luận, giải quyết mâu thuẫn và chốt thống nhất ở Pha 2. Trước khi chốt nhãn độc lập ở Pha 3, nhóm điều chỉnh các mốc `occluded_level` thành 25%, 50%, 75%. Mọi thành viên phải dùng cùng bảng mốc bên dưới; nhãn nháp đã vẽ theo mốc cũ cần được rà và sửa trước khi đo IAA.

- Phiên bản: `v1.0` (Agreed)
- Ngày: 2026-11-16
- Thành viên tham gia thống nhất: 101, 102, 103, 104

---

## 1. Mục tiêu
- **Mục đích sử dụng**: Bộ nhãn bounding box (hộp chữ nhật 2D) dùng để huấn luyện và đánh giá mô hình nhận diện vật thể cho hệ thống xe tự lái (Autonomous Driving) và camera giám sát giao thông đô thị thông minh.
- **Mức độ rủi ro**: Sai sót nghiêm trọng nhất trong môi trường tự lái là **bỏ sót (False Negative)** người đi bộ (`person`), người điều khiển phương tiện (`rider`) hoặc các phương tiện giao thông trên đường, dẫn đến nguy cơ va chạm giao thông. Báo sai (False Positive) vào các tác phẩm điêu khắc hoặc tranh vẽ làm hệ thống phanh gấp vô căn cứ.
- **Yêu cầu kỹ thuật**: Box phải ôm sát phần thực tế nhìn thấy được (visible pixels), đúng lớp theo taxonomy v1.0 và gán chính xác các thuộc tính đo được (`occluded_level`), cắt mép ảnh (`truncated`), và đánh dấu cờ nghi vấn (`can_review`).

---

## 2. Lớp và thuộc tính

### Danh mục 10 lớp đối tượng (mỗi vật thuộc đúng một lớp)

| Lớp | Định nghĩa (một câu) | Ví dụ minh họa | Phản ví dụ / Dễ nhầm với (Không gán) |
|---|---|---|---|
| `car` | Xe ô tô con từ 4 đến 7 chỗ (sedan, SUV, hatchback, coupe) và xe bán tải (pickup) không có nắp thùng cao. | Xe con gia đình, xe taxi 4 bánh (`img_001`, `img_003`, `img_006`, `img_010`). | Xe mô hình đồ chơi (`img_038`), tượng xe (`img_027`), xe tải (`truck`). |
| `truck` | Xe tải chở hàng, xe ben, đầu kéo, xe chuyên dụng cỡ lớn có cabin tách biệt với thùng hàng. | Xe tải chở hàng (`img_001`), xe đầu kéo 2 tầng chở ô tô con (`img_002`). | Xe bán tải nhỏ (`car`), xe van thùng kín (`van`). |
| `van` | Xe ô tô thùng kín liền khối cỡ nhỏ hoặc vừa chở hàng nhẹ hoặc chở khách dưới 16 chỗ. | Xe van giao hàng, xe cứu thương cỡ nhỏ (`img_005`, `img_008`, `img_025`). | Xe buýt lớn (`bus`), xe tải thùng rời (`truck`). |
| `bus` | Xe chở khách công cộng cỡ lớn (> 16 chỗ ngồi) chạy tuyến nội đô hoặc liên tỉnh. | Xe buýt nội đô, xe khách du lịch (`img_009`, `img_031`, `img_037`, `img_040`). | Xe minivan dưới 16 chỗ (`van`). |
| `three_wheeler` | Xe cơ giới ba bánh có động cơ dùng để chở khách hoặc chở hàng: xe tuk-tuk, xe lam, xe lôi, xe ba gác máy. | Xe tuk-tuk chở khách, xe ba gác máy chở hàng (`img_004`, `img_016`, `img_035`). | Xe máy 2 bánh (`motorcycle`), xe xích lô đạp chân (`bicycle`). |
| `motorcycle` | Xe mô tô, xe gắn máy 2 bánh có động cơ (xăng hoặc điện). | Xe máy Honda/Yamaha chạy trên đường (`img_001`, `img_005`, `img_026`). | Xe đạp không động cơ (`bicycle`), xe máy điện đồ chơi trẻ em. |
| `bicycle` | Xe đạp và xe xích lô (xe 3 bánh đạp chân), kể cả xe đạp điện có bàn đạp. Gán cả khi có người hoặc không có người. | Xe đạp dựng ven đường, xích lô chở khách (`img_003`, `img_006`, `img_023`, `img_029`, `img_034`, `img_039`). | Xe máy có động cơ (`motorcycle`), xe đẩy tay, xe nôi. |
| `rider` | Người đang điều khiển hoặc ngồi trên phương tiện di chuyển hở (xe máy, xe đạp, xe 3 bánh, xích lô, thùng hở). | Người lái xe máy, hành khách ngồi sau xe máy, người đạp xe đạp, người lái tuk-tuk (`img_001`, `img_005`, `img_021`). | Người đi bộ hoặc đứng trên vỉa hè (`person`), người ngồi trong ô tô kín. |
| `person` | Con người thật đang đi bộ, đứng bên đường hoặc không ngồi trên phương tiện. | Người đi bộ sang đường, người đứng đợi bên vỉa hè, người gánh hàng rong (`img_001`, `img_003`). | Người lái xe máy/xe đạp (`rider`), tượng đồng (`img_011`), tượng đá (`img_017`), ma-nơ-canh (`img_033`), ảnh quảng cáo (`img_018`). |
| `ignore_region` | Vùng không cần gán từng vật: cụm người/xe quá đông đúc ở hậu cảnh xa không thể tách rời hoặc cụm vật thể nhỏ (< 20 px). | Dãy xe máy hoặc đám đông chen chúc ở hậu cảnh ngã tư (`img_021`), nền mờ chuyển động mạnh (`img_014`). | Các đối tượng đứng độc lập nhìn rõ ở tiền cảnh (bắt buộc gán box riêng). |

*Lưu ý đối với người ngồi trong xe*:
- Người ngồi trong khoang kín của ô tô (`car`, `truck`, `van`, `bus`) nhìn qua kính: **Không gán box**.
- Người điều khiển hoặc hành khách trên phương tiện hở (`motorcycle`, `bicycle`, `three_wheeler`, xích lô): **Gán box `rider`**.

### Thuộc tính đối tượng

1. **`occluded_level`** (ước lượng tỉ lệ diện tích vật thể bị vật khác che khuất; các mốc thống nhất là **25%, 50%, 75%**):
   - `0`: Bị che từ 0% đến **dưới 25%**.
   - `1`: Bị che từ **25% đến dưới 50%**.
   - `2`: Bị che từ **50% đến dưới 75%**.
   - `3`: Bị che từ **75% đến dưới 100%**, nhưng vẫn nhận dạng chắc chắn đối tượng và lớp.
   - Nếu không còn nhận dạng chắc chắn thì **không gán**, bất kể ước lượng tỉ lệ che khuất. Đúng tại mốc 25%, 50% hoặc 75% thì chọn mức cao hơn.

2. **`truncated`** (Cắt mép ảnh):
   - `no`: Toàn bộ vật thể nằm trọn vẹn bên trong khung ảnh.
   - `yes`: Vật thể bị mép biên ảnh cắt mất một phần.

3. **`can_review`** (Cờ đánh dấu cần kiểm tra lại):
   - `false` (mặc định): Tự tin về nhãn và ranh giới box.
   - `true`: Tick chọn khi gặp tình huống biên lưỡng lự, ranh giới mờ hoặc ca khó chưa có tiền lệ để cả nhóm thảo luận tại buổi họp calibration Pha 5.

---

## 3. Quy tắc vẽ box (Gắn mã Rule ID)

- **`R-01` (Tight Visible Bounding Box)**: Bounding box phải ôm sát phần pixel thực tế nhìn thấy được của vật thể (sai lệch không quá 2 px). Không phóng to box ra ngoài khoảng trống để ước đoán phần bị che khuất.
- **`R-02` (Rider & Vehicle Overlap)**:
  - Trên phương tiện 2–3 bánh (`motorcycle`, `bicycle`, `three_wheeler`): Mỗi người trên xe (người lái và người ngồi sau) được gán **01 box `rider`** ôm trọn phần cơ thể nhìn thấy được.
  - Phương tiện được gán **01 box riêng** (`motorcycle`, `bicycle` hoặc `three_wheeler`) ôm trọn thân xe.
  - Box của `rider` và box của phương tiện được phép giao nhau (overlap).
- **`R-03` (Car Carrier `img_002.jpg`)**:
  - Gán **01 box `truck`** cho toàn bộ khung xe chở (bao gồm đầu kéo và giàn chở 2 tầng).
  - Gán riêng **từng box `car`** cho từng chiếc ô tô con nằm trên các tầng sàn chở hàng nhìn thấy được (kèm thuộc tính `occluded_level` tương ứng).
- **`R-04` (False Objects — Vật thể không thật)**:
  - **Tượng điêu khắc**: Tượng người/thú (`img_011`, `img_017`), bia mộ/tượng hình xe (`img_027`) ➔ **Không gán**.
  - **Hình in 2D**: Áp phích, pano, biển quảng cáo, tranh vẽ hình người (`img_018`, `img_030`, `img_033`) ➔ **Không gán**.
  - **Hình phản chiếu**: Gương chiếu hậu (`img_020`), mặt kính cửa hàng, mặt nước ➔ **Không gán**.
  - **Đồ chơi / Mô hình**: Xe đồ chơi, ô tô mô hình trưng bày trong tủ kính (`img_038`) ➔ **Không gán**.
  - **Ảnh không có vật thật hợp lệ** (`img_017`, `img_020`, `img_027`, `img_038`): **Để trống**, ảnh không chứa box nào vẫn là dữ liệu COCO hợp lệ.
- **`R-05` (Overloaded Cargo `img_035.jpg`)**:
  - Bounding box phương tiện chỉ ôm sát phần thân vỏ cứng của phương tiện.
  - Tuyệt đối không kéo dài box để bao quanh hàng hóa, bao tải cồng kềnh chất nhô cao ra ngoài thân xe.
- **`R-06` (Crowds & Traffic Jam `img_019.jpg`, `img_021.jpg`)**:
  - Đối với các đối tượng ở tiền cảnh nhìn rõ (chiều cao ≥ 20 px, thấy rõ ≥ 30%): Bắt buộc gán từng box riêng lẻ cho từng người (`rider`/`person`) và xe.
  - Đối với phần đám đông hoặc dãy xe ùn tắc dày đặc ở hậu cảnh xa không thể tách rời: Vẽ 1–3 box **`ignore_region`** bao quanh cụm đông. Tuyệt đối **không được bỏ trống cả ảnh**.
- **`R-07` (Minimum Box Size)**:
  - Vật thể có chiều cao hoặc chiều rộng nhỏ hơn **20 pixel**: Không gán box độc lập (nếu nằm trong cụm đông thì đưa vào `ignore_region`).
- **`R-08` (Motion Blur `img_014.jpg`)**:
  - Đối tượng ở tiền cảnh nhìn rõ: gán bình thường.
  - Vùng nhòe chuyển động mạnh không nhận dạng được: vẽ box `ignore_region` + tick `can_review`.

---

## 4. Bảng ca khó tổng hợp từ 40 ảnh

| Ảnh | Tình huống cụ thể | Rule ID | Quyết định của nhóm | Lý do |
|---|---|---|---|---|
| `img_002.jpg` | Xe tải 2 tầng chuyên dụng chở 8 ô tô con | `R-03` | 1 box `truck` cho xe mẹ + 8 box `car` cho 8 xe trên sàn | Xe con trên sàn là phương tiện vật lý hoàn chỉnh cần nhận diện phòng tránh va chạm. |
| `img_004.jpg`, `img_016.jpg` | Xe ba bánh có động cơ chở khách tuk-tuk | `R-01` | Gán lớp `three_wheeler` cho xe, người lái gán `rider` | Xe 3 bánh động cơ thuộc nhóm `three_wheeler`. |
| `img_005.jpg`, `img_006.jpg` | Người lái và người ngồi sau xe máy | `R-02` | Mỗi người 1 box `rider` + xe 1 box `motorcycle` | Tách bạch người và phương tiện, cho phép box giao nhau. |
| `img_009.jpg`, `img_031.jpg`, `img_040.jpg` | Xe buýt lớn chở khách công cộng | `R-01` | Gán lớp `bus` | Xe chở khách cỡ lớn trên 16 chỗ. |
| `img_011.jpg`, `img_017.jpg` | Tượng đồng, tượng đá danh nhân cưỡi ngựa | `R-04` | **Không gán** box nào cho cả người và ngựa | Tác phẩm điêu khắc nghệ thuật cố định, không phải thực thể giao thông. |
| `img_014.jpg` | Ảnh người và xe bị nhòe do chuyển động | `R-08` | Tiền cảnh rõ gán `rider`/`motorcycle`; nền mờ gán `ignore_region` | Đảm bảo không bỏ sót vật rõ và không gán bừa vật mờ. |
| `img_018.jpg` | Hình in chân dung người trên biển quảng cáo | `R-04` | **Không gán** | Là tranh in 2D trên biển bảng tĩnh ven đường. |
| `img_019.jpg`, `img_021.jpg` | Ngã tư đông đúc, đám đông dừng chờ đèn đỏ | `R-06` | Tiền cảnh gán từng `rider`/`motorcycle`; hậu cảnh gán `ignore_region` | Tránh lỗi bỏ sót hệ thống trong kiểm tra QC. |
| `img_020.jpg` | Hình xe phản chiếu qua gương chiếu hậu | `R-04` | **Không gán** | Ảo ảnh quang học phản xạ, không phải vật thể thực tế. |
| `img_023.jpg` | Xe xích lô chở khách đạp chân | `R-01` | Xe gán `bicycle`, người đạp và khách gán `rider` | Phương tiện đạp chân thuộc `bicycle`. |
| `img_027.jpg` | Tượng đá hình xe bán tải trang trí | `R-04` | **Không gán** | Tác phẩm điêu khắc bằng đá tĩnh ven đường. |
| `img_033.jpg` | Ma-nơ-canh mặc đồ trong tủ kính | `R-04` | **Không gán** | Ma-nơ-canh cửa hàng thời trang. |
| `img_035.jpg` | Xe tuk-tuk chở khối bao tải hàng nhô cao | `R-05` | Box `three_wheeler` chỉ ôm sát thân xe, không ôm hàng nhô | Tránh làm sai lệch kích thước chuẩn của phương tiện. |
| `img_038.jpg` | Xe ô tô đồ chơi mô hình trong tủ kính | `R-04` | **Không gán** | Đồ chơi mô hình thu nhỏ. |

---

## 5. QC và Lịch sử phiên bản

- Mọi bất đồng khi gán nhãn ở Pha 3 được đánh dấu `can_review = true` để đưa vào phiên họp Calibration Pha 5.
- Lịch sử thay đổi:

| Phiên bản | Ngày | Thay đổi chính | Cơ sở thay đổi |
|---|---|---|---|
| `v0` | — | Bản khung sơ sài ban đầu từ giảng viên (6 lớp, không ngưỡng định lượng, không có ca khó). | Bắt đầu lab. |
| `v1.0` | 2026-11-16 | Thống nhất 10 lớp: bổ sung `bus`, `three_wheeler`, `bicycle`, tách `rider` khỏi `person`; định lượng `occluded_level` theo mốc 30%, 60%, 90%; bổ sung quy tắc gắn mã `R-01` đến `R-08`; giải quyết triệt để ca xe chở xe (`img_002`), đám đông (`img_021`), hàng quá khổ (`img_035`) và loại trừ vật không thật. | Thảo luận và biểu quyết chốt mâu thuẫn giữa 4 thành viên (101, 102, 103, 104) ở Pha 2. |
| `v1.0` (điều chỉnh trước khi chốt nhãn Pha 3) | 2026-11-16 | Đổi mốc `occluded_level` thành 25%, 50%, 75%; mức `3` áp dụng từ 75% đến dưới 100% nếu vẫn nhận dạng chắc chắn. Các nhãn nháp theo mốc cũ phải rà lại; tên thuộc tính và bốn giá trị `0`–`3` giữ nguyên. | Quyết định cập nhật của nhóm trước khi tổng hợp nhãn độc lập và đo IAA. |
