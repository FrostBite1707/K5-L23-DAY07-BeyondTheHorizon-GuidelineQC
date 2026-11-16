# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 239 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 239 | 0.827 | 88.3% | 49.4% | 87.4% | 105/70 | 22 | CHƯA ĐẠT |
| 101|102 | 6 | 28 | 0.871 | 82.1% | 64.3% | 96.4% | 0/5 | 0 | CHƯA ĐẠT |
| 101|103 | 9 | 40 | 0.856 | 85.0% | 57.5% | 87.5% | 27/2 | 21 | CHƯA ĐẠT |
| 101|104 | 5 | 53 | 0.775 | 98.1% | 24.5% | 90.6% | 19/21 | 0 | CHƯA ĐẠT |
| 102|103 | 5 | 22 | 0.816 | 77.3% | 45.5% | 72.7% | 26/3 | 0 | CHƯA ĐẠT |
| 102|104 | 9 | 75 | 0.842 | 93.3% | 52.0% | 89.3% | 32/20 | 0 | CHƯA ĐẠT |
| 103|104 | 6 | 21 | 0.805 | 71.4% | 71.4% | 76.2% | 1/19 | 1 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 631 ({'bo_sot_thua': 155, 'iou_thap': 110, 'sai_thuoc_tinh': 134, 'co_can_review': 164, 'bat_dong_ignore': 40, 'sai_lop': 28}), trên 34 ảnh cần calibration.
