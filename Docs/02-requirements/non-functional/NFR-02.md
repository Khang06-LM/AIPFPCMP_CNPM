# NFR-02 — Quản lý ảnh và an toàn lưu trữ

| Thuộc tính | Nội dung |
|---|---|
| Trạng thái | **Draft / Review Required** |
| Người phụ trách review | TV2 |
| Nguồn | Cloud object storage là yêu cầu G; dung lượng/quota/TIFF bắt buộc tùy quy chế. |
| Phạm vi áp dụng | Các API/giao diện/luồng có liên quan, ưu tiên 5 Core Flow |

## Yêu cầu kiểm chứng được

Ảnh thật lưu ở object storage; link chứng cứ riêng tư có hạn, kiểm MIME thực và kích thước, tránh URL public mặc định.

## Thiết kế chứng minh

Thử upload JPEG/TIFF hợp lệ, giả MIME, quá cỡ, ngắt mạng và kiểm tra truy cập không quyền.

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

- **Tình huống:** File upload xong nhưng DB failed.
- **Cách chứng minh theo đề xuất [D]:** Đối soát orphan temp object và không có Submission hoàn tất sai.
- **Nguồn chứng cứ cần cung cấp khi có code:** commit SHA, environment, testcase, Expected/Actual, HTTP/DB logs đã ẩn dữ liệu nhạy cảm.
- **Ngưỡng số đo chưa được xác nhận:** chưa tự đặt SLA, concurrent users, quota hoặc time retention là yêu cầu của giảng viên; cần nhóm đo PoC và phê duyệt trước khi đánh dấu VERIFIED.
- **Tài liệu liên kết:** [Failure Recovery](../../05-quality/failure-recovery-matrix.md) · [Coverage](../../04-traceability/requirement-coverage.md).
