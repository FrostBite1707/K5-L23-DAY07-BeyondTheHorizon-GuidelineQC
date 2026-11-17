# Báo cáo QC — BeyondTheHorizon

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Thực hành đặc tả và QC nhãn bounding box 2D; tập ảnh tuyển chọn, chưa đánh giá tính đại diện cho dữ liệu vận hành.
- Nguồn và phép sử dụng dữ liệu: Theo `data/ATTRIBUTION.csv`; giữ nguyên ghi công nguồn trong gói bàn giao.
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json`; `labels_calibrated.json` SHA-256 `501930e11d32057c7b3a7115f641dac563a6c73da8212af60396db882c853f77`.
- Guideline và taxonomy khi gán / khi bàn giao: Gán theo guideline v1.0 và taxonomy v1.0; guideline v1.1 là bản dự thảo chờ nhóm xác nhận, taxonomy không đổi.

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt; 40 ảnh, bốn người gán.
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 414 cặp; IoU TB 0.935; đồng thuận lớp 97.83% (414 cặp); đồng thuận `occluded_level` 100% và `truncated` 100% trên các cặp được so sánh.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 0 box chưa ghép; 0 bị bỏ qua do ignore; 0 bất đồng vùng ignore theo báo cáo IAA hiện tại.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: Chưa có tham chiếu coach được ghi trong hồ sơ này; cần nhóm bổ sung nếu có.

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Có 9 đề xuất trên 8 ảnh trong `calibration_decisions.csv`; chưa có xác nhận review của thành viên.
- Tác động v1.1 lên nhãn và ảnh: Bản nháp đề xuất làm rõ pickup thùng mở và xe máy gắn sidecar; do có đề xuất đổi lớp sidecar, phạm vi đặt là toàn bộ 40 ảnh. Cần nhóm xác nhận R-09 và rà lại lô.
- Tham chiếu bằng chứng khi Rework: Chưa có vòng Rework.

## 4. Kết quả duyệt

- Vòng QC: 1
- Người review và ngày: Chưa có người review xác nhận; báo cáo tự động được tạo ngày 2026-11-17.
- Đã duyệt / cả lô: 0/40.
- Ảnh lỗi / ảnh đã duyệt: 0/0 (N/A; chưa có ảnh nào được người review duyệt).
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/0 (N/A; chưa có đối tượng nào được người review duyệt).
- Vật bỏ sót, ảnh và vùng: Chưa ghi nhận; cần người review kiểm ảnh gốc.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: 9 ca calibration còn chờ xác nhận; xem `calibration_decisions.csv` và `work/calibration_cases_overview.jpg`.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Máy kiểm được schema, lớp, thuộc tính và hình học box; người review cần xác nhận vật thể thực, lớp đúng và box ôm sát.

## 5. Quyết định bàn giao

- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: Chưa có người review xác nhận cho 40/40 ảnh; chỉ dùng làm bản nháp nội bộ để tiếp tục calibration.
- Nếu còn việc: Bốn thành viên review chéo theo `work/calibration_review_assignment.csv`; ghi kết quả thực tế vào `kiem_lai`, rà đủ lô trên CVAT, export lại COCO và chạy lại các lệnh calibration/QC.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: Chưa thực hiện; cần người ngoài nhóm lần theo một ca sau khi review hoàn tất.
