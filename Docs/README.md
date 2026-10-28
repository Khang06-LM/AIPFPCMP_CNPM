# Docs — Software Engineering Project Documentation

> **Phiên bản 0.9 — Review Required (28/10/2026).** Đọc [README gốc](../README.md) để xem tình trạng dự án. Đây là tài liệu yêu cầu/thiết kế **dự thảo có thể phản biện**, chưa phải chứng cứ code hoặc test chạy.

## Cấu trúc phân rã

| Phần | Mục đích | Bắt đầu ở đâu |
|---|---|---|
| 00-overview | Problem, Context, Stakeholders, Core Flows, từ điển | [Problem Statement](00-overview/problem-statement.md) |
| 01-governance | Quản lý/review/thay đổi Requirements | [Requirements Management](01-governance/requirements-management.md) |
| 02-requirements | 18 FR độc lập, 8 NFR, AC, Test | **[SRS / Requirement Index](02-requirements/README.md)** |
| 03-domain | Entity, data dictionary, ERD, business rules | [Entity Catalogue](03-domain/entities.md) |
| 04-traceability | Mapping Core Flow ↔ FR ↔ FPC ↔ Test | [Requirements Matrix](04-traceability/requirements-matrix.md) |
| 05-quality | Test strategy, Test Matrix, E2E | [Test Case Matrix](05-quality/test-matrix.md) |
| 06-feasibility | Khả thi, risk, capacity theo giờ | [Feasibility Study](06-feasibility/feasibility-study.md) |
| 07-review | Nộp GitHub, trình bày và Q&A, report map | [Submission Guide](07-review/submission-guide.md) |

## Quy tắc học thuật và quản trị

- `[G]` từ đề tài giảng viên, `[P]` từ kế hoạch nhóm, `[D]` nhóm suy luận và đề xuất, `TBD` chưa quyết định.
- Không trình bày các đề xuất (ví dụ 20 MB, một ảnh mỗi Submission, công thức tie-break) như yêu cầu gốc khi chưa có phê duyệt.
- **Mọi Test Case đang NOT RUN; không có chứng cứ PASS trong hai tài liệu nguồn.**
- Người viết tự kiểm nguồn + reviewer chéo; PR thay đổi Docs phải cập nhật Traceability và Changelog.

## Lối tắt cho buổi hỏi bài

1. [Problem & Context](00-overview/problem-statement.md) — tại sao làm.
2. [Flow xuyên suốt](00-overview/end-to-end-flows.md) — các chức năng nối nhau.
3. [FR-06 Nộp ảnh chi tiết](02-requirements/functional/FR-06-photo-submission.md) — pre/main/unhappy/BR/entity/AC/TC/feasibility.
4. [Entity Catalogue](03-domain/entities.md) — dữ liệu nào và ràng buộc.
5. [Traceability](04-traceability/requirements-matrix.md) — link Jira/test.
6. [Open Questions](01-governance/open-questions.md) — điều chưa chốt, không đoán.


## Source index theo trang

[Chỉ mục nguồn [G]/[P] theo trang](00-overview/source-index.md)

## V3 — Hồ sơ phản biện nghiệp vụ

| Câu hỏi của thầy | Tài liệu để mở |
|---|---|
| Business rules quyết định thế nào? | [Business Decision Tables](03-domain/business-decision-tables.md) |
| Các trạng thái và bước bị cấm? | [State Transitions](03-domain/state-transitions.md) |
| Điều gì phải luôn đúng? | [Business Invariants](03-domain/business-invariants.md) |
| Lỗi mạng/DB/AI phục hồi thế nào? | [Failure & Recovery](05-quality/failure-recovery-matrix.md) |
| Có dữ liệu cụ thể và test expected? | [Business Scenario Tests](05-quality/business-scenario-tests.md) |
| Chứng minh đáp ứng tới đâu? | [Requirement Coverage](04-traceability/requirement-coverage.md) |
| Không đủ giờ/phạm vi chưa chốt? | [Gap Analysis](06-feasibility/requirement-gap-analysis.md) |
| Thầy đưa một tình huống mới? | [Business Defense Playbook](07-review/business-defense-playbook.md) |
