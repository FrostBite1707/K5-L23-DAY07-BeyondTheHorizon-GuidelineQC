# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 265 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 265 | 0.825 | 88.7% | 51.3% | 89.8% | 92/60 | 11 | CHƯA ĐẠT |
| 101|102 | 6 | 28 | 0.864 | 78.6% | 64.3% | 96.4% | 2/1 | 0 | CHƯA ĐẠT |
| 101|103 | 9 | 45 | 0.845 | 84.4% | 48.9% | 88.9% | 35/2 | 11 | CHƯA ĐẠT |
| 101|104 | 5 | 65 | 0.783 | 98.5% | 38.5% | 92.3% | 10/14 | 0 | CHƯA ĐẠT |
| 102|103 | 5 | 24 | 0.816 | 79.2% | 54.2% | 75.0% | 22/2 | 0 | CHƯA ĐẠT |
| 102|104 | 9 | 81 | 0.840 | 93.8% | 51.9% | 90.1% | 23/21 | 0 | CHƯA ĐẠT |
| 103|104 | 6 | 22 | 0.816 | 72.7% | 72.7% | 90.9% | 0/20 | 0 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 628 ({'bo_sot_thua': 126, 'iou_thap': 122, 'sai_thuoc_tinh': 139, 'co_can_review': 166, 'bat_dong_ignore': 45, 'sai_lop': 30}), trên 34 ảnh cần calibration.
