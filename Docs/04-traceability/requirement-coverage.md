# COV-01 — Requirement Coverage & Evidence Matrix

> **Status: DRAFT.** Đang đo **độ phủ thiết kế kiểm thử**, chưa có chứng cứ các yêu cầu đã triển khai/đạt. Không sử dụng tỷ lệ này để báo cáo “đã hoàn thành x% phần mềm”.

## 1. Mô hình truy vết

`Bối cảnh [G] → Core Flow → FR → BR/Invariant → Entity → API/FE design → AC → TC → Jira FPC → PR/Commit → Test Run Evidence → Result`.

- `Specified`: FR có actor, luồng chính, lỗi, rule.
- `Designed`: có Entity/API/state/business decisions + test design.
- `Implemented`: có PR merged và build/deploy (evidence thật).
- `Verified`: test run PASS gắn commit, environment và trace.
- `Approved`: BA/owner/reviewer duyệt; không tự chuyển trạng thái chỉ vì AI đã viết tài liệu.

## 2. Bảng coverage ưu tiên theo nhóm

| Requirement | Quy tắc trọng điểm | Test thiết kế liên quan | Entity trọng điểm | Evidence implementation / test | Trạng thái |
|---|---|---|---|---|---|
| FR-01 | INV-06, RBAC | TC-BIZ-SCORE-02, TC-STATE-01 | User/UserRole | Chưa có | DRAFT |
| FR-02 | Audit và deactivate danh mục | TC-BIZ-REVIEW-04 | User, FilmStock, AuditLog | Chưa có | DRAFT |
| FR-03 | Rubric lock | TC-BIZ-SCORE-05 | Contest, Criterion | Chưa có | DRAFT |
| FR-04 | Enrollment status và duplicate | TC-BIZ-SUB-04 (ownership gate) | Enrollment | Chưa có | DRAFT |
| FR-05 | INV-02, snapshot metadata | TC-BIZ-SUB-04 | FilmRoll/FilmFrame | Chưa có | DRAFT |
| FR-06 | INV-01..04, SUB table | TC-BIZ-SUB-01..05 | Submission/MediaAsset | Chưa có | DRAFT |
| FR-07 | Validation version | TC-BIZ-VAL-01 | ValidationReport | Chưa có | DRAFT |
| FR-08 | INV-05, audit re-review | TC-BIZ-REVIEW-01/03/04 | ReviewDecision | Chưa có | DRAFT |
| FR-09 | INV-06, revoke Judge | TC-BIZ-SCORE-02 | JudgeAssignment | Chưa có | DRAFT |
| FR-10 | INV-07/08, SCORE table | TC-BIZ-SCORE-01..05 | ScoreSheet/CriterionScore | Chưa có | DRAFT |
| FR-11 | INV-09/10, PUB table | TC-BIZ-PUB-01..04 | RankingSnapshot/Award | Chưa có | DRAFT |
| FR-12 | INV-11, consent | TC-BIZ-ARCH-01 | ArchiveEntry/MediaAsset | Chưa có | DRAFT |
| FR-13 | INV-12, duplicate flag | TC-BIZ-AI-01 | AIAnalysis | Chưa có | DRAFT |
| FR-14 | INV-12, detector false positive | TC-BIZ-AI-01/02 | AIJob/AIAnalysis | Chưa có | DRAFT |
| FR-15 | tag provenance | TC-FR-15-xx trong test-matrix | Tag/MediaTag | Chưa có | DRAFT |
| FR-16 | metric dedup | TC-BIZ-METRIC-01 | Submission/Contest | Chưa có | DRAFT |
| FR-17 | điểm chỉ hiện sau publish | TC-FR-17-xx trong test-matrix | User/Submission | Chưa có | DRAFT |
| FR-18 | INV-13, event idempotent | TC-BIZ-NOTIFY-01 | Notification/AuditLog | Chưa có | DRAFT |

## 3. Kiểm tra tính đầy đủ

- [ ] Mỗi FR có ít nhất 1 happy + 1 unhappy test thực tế.
- [ ] Mỗi transition quan trọng có API guard, actor và test.
- [ ] Mỗi Entity dùng bởi >= 1 FR và bất biến được kiểm tra ở BE/DB.
- [ ] Mỗi Story có 1 owner, effort-hour, PR và test evidence khi DONE.
- [ ] Những quyết định `TBD` làm thay đổi logic đều được highlight trong PR và Jira BLOCKED.
- [ ] Không ghi PASS nếu chỉ có expected result.

## 4. Cách bổ sung evidence sau khi code

Ví dụ định dạng một dòng:

`FR-06 | FPC-23 | PR #<id> | commit <sha> | test TC-BIZ-SUB-02 | staging <env> | PASS/FAIL | reviewer <login> | date <yyyy-mm-dd>`.

**Hiện chưa điền bằng chứng thật**. Xem thêm [ma trận Jira](requirements-matrix.md) và [Test Matrix](../05-quality/test-matrix.md).
