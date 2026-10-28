# RISK-01 — Sổ rủi ro và trigger hành động

| ID | Rủi ro | Khả năng / tác động | Dấu hiệu nhận biết | Owner | Ứng phó | Dự phòng nếu xảy ra |
|---|---|---|---|---|---|---|
| R-01 | Scope Web + Flutter + AI quá lớn | Cao / Cao | Story hours > available hours | TV1 | Chốt MVP và xác nhận GV sớm | Web responsive; Flutter làm mở rộng |
| R-02 | Backend là điểm nghẽn | Cao / Cao | FE chờ API > 1 ngày | TV2 | Chốt OpenAPI/mock và review hợp đồng | Chia endpoint nghiệp vụ có review |
| R-03 | Storage lỗi/quota | Trung bình / Cao | Upload không finalize | TV2 | PoC + quota + cleanup | Giới hạn file theo thầy duyệt |
| R-04 | AI detector sai cao | Cao / Cao | False positive ảnh phim | TV5 | Benchmark có nhãn, warning-only | Manual review, báo giới hạn |
| R-05 | BR điểm đồng điểm chưa chốt | Trung bình / Cao | Ranking không thể công bố | TV4 | Xin quy tắc D-06 trước code | Dừng publish, nêu lý do |
| R-06 | Mất bản quyền riêng tư ảnh | Trung bình / Cao | Public thấy negative/PII | TV1 | Private storage, consent & tests | Gỡ link/cô lập asset/khắc phục |
| R-07 | SRS không khớp code | Cao / Trung bình | PR đổi API mà FR chưa sửa | TV1 | PR yêu cầu update RTM/Docs | Tạo CR và sửa nhất quán |
| R-08 | Sinh viên bận thi/không đủ giờ | Cao / Cao | Actual hours thấp 2 ngày liên tiếp | TV1 | Capacity weekly, support reviewer | Giảm mở rộng / đổi owner |
| R-09 | Hạn nộp thực tế không đúng 06/12 | Chưa rõ / Cao | Chưa có xác nhận GV | TV1 | Hỏi trực tiếp và ghi D-10 | Rebaseline Sprint ngay |

## Quy tắc xử lý

Risk có `trigger`, `owner`, `action`, `decision date`; không ghi “có rủi ro” rồi không làm gì. Tại sprint review cập nhật xác suất/tác động và những sự kiện đã xảy ra. Khi đổi scope bắt buộc, cần decision từ giảng viên.
