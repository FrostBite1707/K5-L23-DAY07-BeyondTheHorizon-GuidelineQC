# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 414 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 414 | 0.935 | 97.8% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 101|102 | 6 | 33 | 0.935 | 97.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 101|103 | 9 | 69 | 0.935 | 95.7% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 101|104 | 5 | 93 | 0.935 | 98.9% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 102|103 | 5 | 51 | 0.935 | 98.0% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 102|104 | 9 | 127 | 0.935 | 99.2% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |
| 103|104 | 6 | 41 | 0.935 | 95.1% | 100.0% | 100.0% | 0/0 | 0 | ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 9 ({'sai_lop': 9}), trên 8 ảnh cần calibration.
