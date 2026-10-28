# BDT-01 — Business Decision Tables / Bảng quyết định nghiệp vụ

> **Version 1.0 · DRAFT FOR REVIEW · 28/10/2026**. Đây là **thiết kế đề xuất [D]**, không phải quy định giảng viên đã chốt. Các bảng mô tả thứ tự xử lý cho Backend; thay đổi phải cập nhật FR, BR, Entity, Test và Jira đồng thời.

## 1. Phạm vi, giả định và nguyên tắc

- **Nguồn [G]:** tổ chức cuộc thi, nộp ảnh kèm metadata, kiểm duyệt, chấm điểm, kết quả, lưu trữ và AI hỗ trợ.
- **Nguồn [P]:** 5 Core Flow, 4 vai trò, mã Story FPC trong kế hoạch nhóm.
- **Đề xuất [D]:** chính sách hạn nộp tính ở server finalize; xử lý trùng bằng idempotency; snapshot bài nộp; không công bố tie-break chưa giải quyết.
- Ưu tiên điều kiện: **(1) Xác thực/quyền → (2) tồn tại và quyền sở hữu → (3) trạng thái → (4) deadline/quota → (5) kiểm tra tệp/dữ liệu → (6) transaction → (7) sự kiện hậu xử lý**.
- Nếu nhiều điều kiện sai, trả lỗi ổn định nhưng không rò dữ liệu cuộc thi hoặc dữ liệu của người khác. Không được dựa vào kiểm tra UI để hợp thức hóa dữ liệu.

## 2. BDT-SUB — Quyết định chốt nộp bài (FR-06)

| Rule | JWT hợp lệ | Enrollment hợp lệ | Contest đang mở *tại thời điểm BE chốt* | Frame thuộc người nộp | File READY / đạt cấu hình | Quota hợp lệ | Quyết định |
|---|---|---|---|---|---|---|---|
| SUB-01 | Y | Y | Y | Y | Y | Y | Tạo Submission SUBMITTED, ghi submitted_at server, xếp validation |
| SUB-02 | N | - | - | - | - | - | 401; không tạo Submission |
| SUB-03 | Y | N | - | - | - | - | 403/422 theo chính sách; không tạo Submission |
| SUB-04 | Y | Y | N | - | - | - | 409 `CONTEST_CLOSED`; giữ file tạm cho cleanup |
| SUB-05 | Y | Y | Y | N | - | - | 403 hoặc 404 để tránh lộ frame người khác |
| SUB-06 | Y | Y | Y | Y | N | - | 422 `INVALID_ASSET`; không chuyển status |
| SUB-07 | Y | Y | Y | Y | Y | N | 409 `QUOTA_EXCEEDED` (D-02 còn TBD) |
| SUB-08 | Y | Y | Y | Y | Y | Y | Retry cùng idempotency_key: trả lại cùng kết quả, không tạo row mới |

`-` nghĩa là không đánh giá tiếp sau lỗi ưu tiên; mã HTTP cụ thể cần chốt trong API contract. Không công nhận file upload thành bài đã nộp khi chưa finalize thành công.

## 3. BDT-REVIEW — Quyết định kiểm duyệt (FR-07/08/13/14)

| Rule | Validation định lượng | AI flag | Evidence thủ công | Người quyết định | Kết quả đề xuất |
|---|---|---|---|---|---|
| REV-01 | PASS | Không | Không cần | Organizer có quyền | APPROVED + log |
| REV-02 | FAIL (định dạng/thiếu mandatory) | Bất kỳ | Không | Organizer | REJECTED hoặc REVISION_REQUIRED theo thể lệ; lưu reason |
| REV-03 | PASS | Flag duplicate/AI | Chưa rõ | Organizer | UNDER_INVESTIGATION/PENDING_REVIEW; không auto-reject |
| REV-04 | PASS | Flag duplicate/AI | Âm bản/contact sheet có sức thuyết phục | Organizer | Có thể APPROVE và ghi giải trình; không khẳng định 100% |
| REV-05 | PASS | AI_SERVICE_UNAVAILABLE | Chưa rõ | Organizer | Manual review; AI result `PENDING`/`UNAVAILABLE`, không coi là FAIL |
| REV-06 | PASS | Bất kỳ | Bài đã chấm/công bố | Organizer + người có quyền đính chính | Chuyển incident/correction workflow, không sửa dữ liệu lịch sử trực tiếp |

**Cần chốt:** thể lệ cho phép yêu cầu bổ sung sau hạn không; có cho phép Organizer đảo duyệt đã APPROVED hay phải có hai cấp xác nhận (`D-13`, `D-16`).

## 4. BDT-SCORE — Lưu/chốt phiếu điểm (FR-09/10)

| Rule | Judge được gán | Bài đủ điều kiện chấm | ScoreSheet | Điểm hợp lệ | Version yêu cầu | Kết quả |
|---|---|---|---|---|---|---|
| SCORE-01 | Y | Y | DRAFT | Y | Hiện hành | SAVE_DRAFT; version+1 |
| SCORE-02 | N | - | - | - | - | 403; giữ dữ liệu |
| SCORE-03 | Y | N | - | - | - | 409; không chấm bài bị loại/đang điều tra |
| SCORE-04 | Y | Y | FINALIZED | - | - | 409; chỉ cho `REOPEN` theo quyết định được phê duyệt |
| SCORE-05 | Y | Y | DRAFT | N | Hiện hành | 422 với criterion lỗi, không lưu một phần |
| SCORE-06 | Y | Y | DRAFT | Y | Cũ | 409 `VERSION_CONFLICT`; FE phải GET trạng thái mới |
| SCORE-07 | Y | Y | DRAFT | Y | Hiện hành | FINALIZE nguyên tử, khóa cập nhật, ghi audit |
| SCORE-08 | Y | Y | FINALIZED | Y | Trùng request_key đã hoàn tất | Trả cùng kết quả finalize, không tạo phiếu thứ hai |

## 5. BDT-PUBLISH — Quyết định công bố (FR-11)

| Rule | Đủ phiếu chốt | Bài đủ điều kiện | Cùng rubric_version | Đồng điểm đã xử lý | Organizer phê duyệt | Quyết định |
|---|---|---|---|---|---|---|
| PUB-01 | Y | Y | Y | Y | Y | Tạo snapshot version mới, `PUBLISHED` |
| PUB-02 | N | - | - | - | - | `BLOCKED_MISSING_SCORES` |
| PUB-03 | Y | N | - | - | - | `BLOCKED_ELIGIBILITY` |
| PUB-04 | Y | Y | N | - | - | `BLOCKED_RUBRIC_MISMATCH` |
| PUB-05 | Y | Y | Y | N | - | `TIE_UNRESOLVED`; yêu cầu chính sách D-06 |
| PUB-06 | Y | Y | Y | Y | N | DRAFT; không public |
| PUB-07 | Y | Y | Y | Y | Y, đã PUBLISHED | Không overwrite snapshot; chỉnh sửa qua correction version + audit |

Không biến điều kiện `đủ phiếu` thành con số cố định nếu chưa giải quyết D-05. Dữ liệu dùng chấm phải có snapshot và mốc thời gian chốt.

## 6. BDT-ARCHIVE — Công khai tác phẩm và minh chứng (FR-12/17)

| Công bố kết quả | Quyền xuất bản | Asset là ảnh chính | Asset là âm bản/contact sheet | Public xem? |
|---|---|---|---|---|
| Không | Bất kỳ | Bất kỳ | Bất kỳ | Không |
| Có | Không / chưa chốt | Có | - | Không; cần xác nhận đồng ý |
| Có | Có | Có | - | Có, theo phạm vi license/consent |
| Có | Có | - | Có | Không mặc định; quyền chứng cứ riêng biệt |

## 7. Cách nghiệm thu bảng quyết định

- Mỗi rule tương ứng test id `TC-BDT-<GROUP>-<NN>` trong [Business Scenarios](../05-quality/business-scenario-tests.md).
- Kiểm tra **Actual** trên UI + API + DB (và object storage khi cần), không đánh dấu PASS dựa vào tài liệu.
- Bất kỳ rule thay đổi phải có Change Request, người phê duyệt và version (xem [Governance](../01-governance/requirements-management.md)).
