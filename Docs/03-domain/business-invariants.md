# INV-01 — Business Invariants / Điều kiện luôn đúng

> **DRAFT [D]**. Invariant là điều kiện không được phá vỡ ngay cả khi mất mạng, retry, concurrent requests hoặc dịch vụ AI bị lỗi. Đây là các mục tiêu thiết kế để review; chưa có bằng chứng đã được code enforce.

| ID | Invariant | Enforcement đề xuất | Test kiểm chứng | FR |
|---|---|---|---|---|
| INV-01 | Không có Submission SUBMITTED trỏ tới MediaAsset chưa `READY` | BE transaction + guard; đối soát object key | TC-BIZ-SUB-03 | FR-06 |
| INV-02 | Frame thuộc Roll, Roll thuộc đúng Participant/enrollment | JOIN kiểm owner + FK/composite constraints | TC-BIZ-SUB-04 | FR-05/06 |
| INV-03 | `submitted_at` do server tạo khi finalize và đúng chính sách deadline | Backend/DB UTC server time | TC-BIZ-SUB-01 | FR-06 |
| INV-04 | Cùng `client_request_id` không tạo nhiều Submission cho cùng user/scope | unique(user_id, request_key) + idempotent response | TC-BIZ-SUB-02 | FR-06 |
| INV-05 | Chỉ Submission APPROVED hợp lệ mới được chấm; pending investigation không tự vào vòng mới | query guard và version | TC-BIZ-REVIEW-03 | FR-08/09/10 |
| INV-06 | Judge chỉ xem/chấm assignment được cấp, không dựa vào nút ẩn ở FE | RBAC + contest ownership + round assignment | TC-BIZ-SCORE-02 | FR-09/10 |
| INV-07 | Mọi criterion score nằm trong [0,max_score] tương ứng rubric version đã chốt | BE validation + DB constraint nếu phù hợp | TC-BIZ-SCORE-03 | FR-10 |
| INV-08 | ScoreSheet FINALIZED không update in-place, revision cần audit | status guard + optimistic lock + append-only revision | TC-BIZ-SCORE-04 | FR-10 |
| INV-09 | Ranking chỉ tính phiếu FINALIZED của bài đủ điều kiện và rubric tương thích | snapshot query + pre-publication gate | TC-BIZ-PUB-01 | FR-11 |
| INV-10 | Không tự chọn người thắng khi policy đồng điểm chưa xác nhận | `TIE_UNRESOLVED` block publishing | TC-BIZ-PUB-02 | FR-11 |
| INV-11 | Public không tải được contact sheet/negative mặc định | private bucket/object ACL + signed URL scope | TC-BIZ-ARCH-01 | FR-12 |
| INV-12 | AI flag không tự động chuyển submission sang REJECTED | AI output riêng ReviewDecision + RBAC | TC-BIZ-AI-01 | FR-13/14 |
| INV-13 | Notification failure không rollback hành động nghiệp vụ đã commit | outbox queue, idempotent delivery | TC-BIZ-NOTIFY-01 | FR-18 |
| INV-14 | Mọi trạng thái quyết định được lưu actor/time/reason/version và không xóa lịch sử khi sửa | AuditLog + new decision / revision | TC-BIZ-REVIEW-04 | FR-08/11 |

## Ưu tiên thực hiện theo nguồn lực

- **MUST cho demo end-to-end:** INV-01..INV-03, INV-05..INV-09, INV-12; các quy tắc này ảnh hưởng trực tiếp tính đúng nghiệp vụ.
- **MUST trước khi khẳng định xử lý lỗi an toàn:** INV-04, INV-10, INV-11, INV-13, INV-14; có thể giảm phạm vi bằng ngăn chức năng không an toàn, **không được bỏ silent**.
- Nếu thiếu giờ triển khai hệ thống hàng đợi hoàn chỉnh, có thể dùng transaction/outbox tối thiểu hoặc job thủ công cho demo nhưng phải nói rõ hạn chế và rủi ro.

## Khi invariant bị phá vỡ

1. Ghi incident ID và dấu vết object ID/actor/time (không đưa secret vào log).
2. Ngăn bước nghiệp vụ kế tiếp, không tự công bố hoặc tự xóa lịch sử.
3. Đối soát DB/Storage, sửa theo quy trình có người phê duyệt và thử lại test tái hiện.
4. Cập nhật [Failure Recovery](../05-quality/failure-recovery-matrix.md), backlog và tài liệu liên quan.
