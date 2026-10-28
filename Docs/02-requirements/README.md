# SRS — Danh mục yêu cầu hệ thống

> **Bản V3 / 1.0-review / 28-10-2026** · Đây là đặc tả để nhóm và giảng viên kiểm tra, không phải báo cáo kiểm thử đạt.

## 1. Nguồn yêu cầu và ký hiệu
- **[G]** Đề tài giảng viên: *Nền tảng tổ chức và quản lý cuộc thi nhiếp ảnh phim tích hợp trí tuệ nhân tạo*, 6 trang.
- **[P]** Kế hoạch nhóm: *Kế hoạch dự án – Nền tảng quản lý cuộc thi nhiếp ảnh phim tích hợp AI*, 13 trang; 11 Epic/69 Story/284 SP.
- **[D]** Quy tắc/giải pháp nhóm suy luận để biến mô tả thành yêu cầu có thể kiểm thử; cần phê duyệt.
- **[TBD]** Chưa quyết định. Không trình bày giả định là yêu cầu đã được thầy chấp thuận.

## 2. Cây phân rã và cấu trúc
`Business Problem → Business Goal → Core Flow/Business Process → FR/NFR → BR → Entity & Data → AC → TC → Jira Story → PR/Test Evidence`

Hệ thống có **4 vai trò** và **5 Core Flow**; `FR-01..FR-18` là cách nhóm chia module chi tiết, không phải 18 mã do thầy đặt. Các file dưới đây là **đơn vị review yêu cầu**, mỗi file có biến thể lỗi và tiêu chí nghiệm thu.

## 3. Danh mục Functional Requirements

| Requirement | Tên | Actor | Story kế hoạch | Ưu tiên đề xuất | Trạng thái |
| --- | --- | --- | --- | --- | --- |
| [FR-01](functional/FR-01-authentication-rbac.md) | Xác thực, phiên đăng nhập và phân quyền theo vai trò | Khách, Participant, Organizer, Judge, Administrator | FPC-3; FPC-18; FPC-9 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-02](functional/FR-02-admin-masterdata-audit.md) | Quản trị người dùng, danh mục phim và nhật ký hệ thống | Administrator | FPC-8; FPC-10; FPC-11; FPC-12 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-03](functional/FR-03-contest-configuration.md) | Tạo và cấu hình cuộc thi, hạng mục, vòng và tiêu chí chấm | Contest Organizer | FPC-13; FPC-14; FPC-15; FPC-16 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-04](functional/FR-04-enrollment.md) | Đăng ký tham gia và phê duyệt đăng ký | Participant; Contest Organizer | FPC-17; FPC-19 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-05](functional/FR-05-film-roll-frame-metadata.md) | Quản lý cuộn phim, khung hình và siêu dữ liệu analog | Participant | FPC-21; FPC-22 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-06](functional/FR-06-photo-submission.md) | Tải ảnh scan và gửi bài dự thi | Participant | FPC-23; FPC-4 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-07](functional/FR-07-automatic-validation.md) | Kiểm tra điều kiện hợp lệ của bài dự thi | System; Organizer | FPC-27 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-08](functional/FR-08-manual-review.md) | Duyệt bài và xử lý yêu cầu chỉnh sửa | Contest Organizer | FPC-28; FPC-42 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-09](functional/FR-09-judge-assignment.md) | Phân công giám khảo theo cuộc thi, vòng và hạng mục | Contest Organizer; Judge | FPC-29 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-10](functional/FR-10-judging-scoring.md) | Chấm điểm đa tiêu chí, nhận xét và chốt theo vòng | Judge; Contest Organizer | FPC-30; FPC-31 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-11](functional/FR-11-ranking-result-publication.md) | Tổng hợp điểm, xếp hạng và công bố kết quả | Organizer; Participant; Public viewer | FPC-32; FPC-33 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-12](functional/FR-12-digital-archive.md) | Kho lưu trữ số, triển lãm và truy xuất hồ sơ tác phẩm | Public viewer; Participant; Organizer | FPC-34; FPC-35; FPC-36 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-13](functional/FR-13-duplicate-similarity-ai.md) | Phát hiện ảnh trùng và ảnh tương đồng cao | AI Service; Organizer | FPC-37; FPC-38; FPC-42 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-14](functional/FR-14-generated-image-ai.md) | Phát hiện ảnh có dấu hiệu do AI tạo | AI Service; Organizer | FPC-39; FPC-42 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-15](functional/FR-15-auto-tagging.md) | Gắn nhãn, phân loại và tìm kiếm nội dung ảnh | AI Service; Participant; Organizer | FPC-40; FPC-36 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-16](functional/FR-16-analytics.md) | Báo cáo thống kê và phân tích cuộc thi | Organizer; Administrator | FPC-41; FPC-12 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-17](functional/FR-17-participant-portfolio.md) | Hồ sơ cá nhân, portfolio, lịch sử và theo dõi trạng thái | Participant | FPC-18; FPC-20; FPC-24 | MUST – lõi | DRAFT – chưa nghiệm thu |
| [FR-18](functional/FR-18-notifications-audit.md) | Thông báo nghiệp vụ và truy vết thay đổi | System; Participant; Organizer; Judge | FPC-11; FPC-24; FPC-33 (hỗ trợ) | SHOULD – hỗ trợ vận hành | DRAFT – chưa nghiệm thu |

## 4. Non-functional Requirements

Xem [NFR Index](non-functional/README.md). Các ngưỡng hiệu năng, dung lượng, SLA và thời gian lưu trữ hiện là `TBD/Proposed`, không phải số được giảng viên cung cấp.

## 5. Luồng liên kết và truy vết

- [5 Core Flow và liên kết FR](../00-overview/end-to-end-flows.md)
- [Business Rules](../03-domain/business-rules.md)
- [Entity Catalogue](../03-domain/entities.md)
- [Traceability Matrix FR ↔ Story ↔ Test](../04-traceability/requirements-matrix.md)
- [Test Case Matrix](../05-quality/test-matrix.md)
- [Feasibility & Limitations](../06-feasibility/feasibility-study.md)
- [Requirements Governance](../01-governance/requirements-management.md)

## 6. Cách thầy kiểm tra

Chọn **FR-06 nộp ảnh**: đọc mục 1 (nguồn), 4 (happy), 5 (unhappy), 6 (BR), 7 (Entity), 9 (AC), 10 (TC), 11 (khả thi/giới hạn). Từ FR-06 mở FR-07 → FR-08 → FR-09 → FR-10 → FR-11 để thấy một quy trình xuyên suốt. **Mọi trạng thái test hiện là “chưa chạy”.**

## 7. Phản biện nghiệp vụ nâng cao — V3

> **Phần mới để thầy lần theo từ một lỗi đến quyết định và test.** Tài liệu là đặc tả đề xuất, không là bằng chứng đã đạt.

- [BDT-01 — Decision Tables](../03-domain/business-decision-tables.md): khi nào được nộp, duyệt, chấm, công bố, archive.
- [STATE-01 — State Machines](../03-domain/state-transitions.md): chuyển trạng thái và guard theo actor/version.
- [INV-01 — Business Invariants](../03-domain/business-invariants.md): điều kiện không được sai dù lỗi mạng/AI.
- [FRM-01 — Failure & Recovery](../05-quality/failure-recovery-matrix.md): lỗi, phục hồi, hậu điều kiện và test.
- [BIZ-TC-01 — Test phản biện nghiệp vụ](../05-quality/business-scenario-tests.md): setup seed, expected, oracle DB.
- [COV-01 — Coverage/Evidence](../04-traceability/requirement-coverage.md): chưa có bằng chứng thì để DRAFT/NOT RUN.
- [GAP-01 — Phân tích thiếu sót & khả thi](../06-feasibility/requirement-gap-analysis.md): blocker, giải pháp MVP, trách nhiệm.
- [DEF-01 — Sổ tay hỏi đáp](../07-review/business-defense-playbook.md): cách giải thích tình huống thầy đặt ra.

**Đi từ ví dụ FR-06**: đọc mục 13 của [FR-06](functional/FR-06-photo-submission.md), sau đó tra `BDT-SUB` → `INV-01..04` → `F-01..05` → `TC-BIZ-SUB-01..05` → `COV-01`.
