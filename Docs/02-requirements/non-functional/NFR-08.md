# NFR-08 — Bảo vệ quyền riêng tư và bản quyền ảnh

| Thuộc tính | Nội dung |
|---|---|
| Trạng thái | **Draft / Review Required** |
| Người phụ trách review | TV1/TV3 |
| Nguồn | G cho tài liệu negative/contact sheet tùy chọn; chính sách lưu lâu dài/đồng ý cần xác nhận. |
| Phạm vi áp dụng | Các API/giao diện/luồng có liên quan, ưu tiên 5 Core Flow |

## Yêu cầu kiểm chứng được

Tách dữ liệu riêng tư và công khai; không hiển thị âm bản/contact sheet công khai mặc định; chỉ dùng ảnh có quyền sử dụng.

## Thiết kế chứng minh

Kiểm payload public API, xem ảnh bằng vai trò lạ, quyền công khai archive.

## Acceptance Criteria / bài kiểm tra

1. Viết ít nhất **một bài kiểm thử đạt** và **một bài kiểm thử thất bại/hành vi bị từ chối** cho NFR này.
2. Lưu **điều kiện môi trường, bộ dữ liệu, expected, actual, evidence** và người thực hiện; không tự đánh dấu `Pass` trước khi chạy.
3. Nếu có ngưỡng định lượng chưa được chốt, ghi `TBD`, người duyệt, ngày quyết định; không gắn số liệu giả.
4. Không đưa bí mật, dữ liệu cá nhân và ảnh không có quyền chia sẻ vào bằng chứng công khai.

## Phương án thực hiện và giới hạn

- **Khả thi sơ bộ:** khả thi nếu chọn một stack và có kiểm thử tự động/tối thiểu theo phạm vi; kiểm chứng thực tế bằng PoC.
- **Giới hạn:** không cam kết compliance tiêu chuẩn doanh nghiệp, SLA cloud, audit bất biến tuyệt đối hoặc 100% chống gian lận khi chưa có thiết kế và dữ liệu kiểm chứng.
- **Kết nối:** [FR Index](../README.md) · [Test Plan](../../05-quality/test-strategy.md) · [Feasibility](../../06-feasibility/feasibility-study.md).

## V3 — Non-functional scenario verification (chưa kiểm thử)

- **Tình huống:** Ảnh chứng cứ riêng tư trong archive public.
- **Cách chứng minh theo đề xuất [D]:** Public không thể lấy signed URL/asset; role-permission test độc lập UI.
- **Nguồn chứng cứ cần cung cấp khi có code:** commit SHA, environment, testcase, Expected/Actual, HTTP/DB logs đã ẩn dữ liệu nhạy cảm.
- **Ngưỡng số đo chưa được xác nhận:** chưa tự đặt SLA, concurrent users, quota hoặc time retention là yêu cầu của giảng viên; cần nhóm đo PoC và phê duyệt trước khi đánh dấu VERIFIED.
- **Tài liệu liên kết:** [Failure Recovery](../../05-quality/failure-recovery-matrix.md) · [Coverage](../../04-traceability/requirement-coverage.md).
