# RTM-01 — Requirements Traceability Matrix

> **Mục đích:** Thầy chọn bất kỳ FR nào cũng truy ra được Core Flow, Jira, owner, test, bằng chứng thực thi. Cột `FPC` là key **trong kế hoạch**; khi tạo Jira thật phải thay bằng key thực do hệ thống cấp.

| FR / Link | Luồng | Jira dự kiến | Owner | Test ID | Mức ưu tiên | Trạng thái/bằng chứng |
| --- | --- | --- | --- | --- | --- | --- |
| [FR-01](../02-requirements/functional/FR-01-authentication-rbac.md) | Nền tảng | FPC-3; FPC-18; FPC-9 | TV2 | TC-FR-01-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-02](../02-requirements/functional/FR-02-admin-masterdata-audit.md) | Nền tảng | FPC-8; FPC-10; FPC-11; FPC-12 | TV1 | TC-FR-02-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-03](../02-requirements/functional/FR-03-contest-configuration.md) | Core Flow 1 | FPC-13; FPC-14; FPC-15; FPC-16 | TV4 | TC-FR-03-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-04](../02-requirements/functional/FR-04-enrollment.md) | Core Flow 2 | FPC-17; FPC-19 | TV3 | TC-FR-04-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-05](../02-requirements/functional/FR-05-film-roll-frame-metadata.md) | Core Flow 2 | FPC-21; FPC-22 | TV3 | TC-FR-05-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-06](../02-requirements/functional/FR-06-photo-submission.md) | Core Flow 2 | FPC-23; FPC-4 | TV3 | TC-FR-06-01..08 | MUST – lõi | Draft; evidence pending |
| [FR-07](../02-requirements/functional/FR-07-automatic-validation.md) | Core Flow 3 | FPC-27 | TV2 | TC-FR-07-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-08](../02-requirements/functional/FR-08-manual-review.md) | Core Flow 3 | FPC-28; FPC-42 | TV4 | TC-FR-08-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-09](../02-requirements/functional/FR-09-judge-assignment.md) | Core Flow 4 | FPC-29 | TV4 | TC-FR-09-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-10](../02-requirements/functional/FR-10-judging-scoring.md) | Core Flow 4 | FPC-30; FPC-31 | TV4 | TC-FR-10-01..07 | MUST – lõi | Draft; evidence pending |
| [FR-11](../02-requirements/functional/FR-11-ranking-result-publication.md) | Core Flow 4 → 5 | FPC-32; FPC-33 | TV2 | TC-FR-11-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-12](../02-requirements/functional/FR-12-digital-archive.md) | Core Flow 5 | FPC-34; FPC-35; FPC-36 | TV3 | TC-FR-12-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-13](../02-requirements/functional/FR-13-duplicate-similarity-ai.md) | Core Flow 3 / AI | FPC-37; FPC-38; FPC-42 | TV5 | TC-FR-13-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-14](../02-requirements/functional/FR-14-generated-image-ai.md) | Core Flow 3 / AI | FPC-39; FPC-42 | TV5 | TC-FR-14-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-15](../02-requirements/functional/FR-15-auto-tagging.md) | Core Flow 5 / AI | FPC-40; FPC-36 | TV5 | TC-FR-15-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-16](../02-requirements/functional/FR-16-analytics.md) | Core Flow 5 / Analytics | FPC-41; FPC-12 | TV5 | TC-FR-16-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-17](../02-requirements/functional/FR-17-participant-portfolio.md) | Core Flow 2 / Core Flow 5 | FPC-18; FPC-20; FPC-24 | TV3 | TC-FR-17-01..06 | MUST – lõi | Draft; evidence pending |
| [FR-18](../02-requirements/functional/FR-18-notifications-audit.md) | Xuyên suốt 5 Core Flow | FPC-11; FPC-24; FPC-33 (hỗ trợ) | TV1 | TC-FR-18-01..06 | SHOULD – hỗ trợ vận hành | Draft; evidence pending |

## 1. Phương pháp cập nhật bằng chứng

- `Source`: liên kết yêu cầu giảng viên; `Design`: Entity/ERD/API; `Implementation`: PR/commit; `Verification`: Test Execution/Actual; `Demo`: staging URL hoặc video được phép chia sẻ.
- **Không điền “PASS” nếu chỉ có file test scenario và chưa chạy.** Nếu đã chạy, ghi thời điểm, người thực hiện, môi trường, actual và bằng chứng.
- Jira Story được gắn mô tả `Requirement: FR-xx` và link tương đối đến file Markdown trên GitHub.

## 2. Coverage 5 Core Flow — thiết kế

| Core Flow | Coverage dự kiến | Ghi chú |
|---|---|---|
| CF-1 | FR-03, FR-01 | Template nâng cao đề xuất sau |
| CF-2 | FR-04, FR-05, FR-06, FR-17 | Xử lý metadata đặc thù phim |
| CF-3 | FR-07, FR-08, FR-13, FR-14 | AI chỉ flag, Organizer quyết định |
| CF-4 | FR-09, FR-10, FR-11 | Multi-round và tie-break TBD |
| CF-5 | FR-11, FR-12, FR-15, FR-16 | Kho lưu trữ và báo cáo cơ bản |
| Cross-cut | FR-01, FR-02, FR-18; NFR-01..08 | Bảo mật, dữ liệu, tài liệu |

**Coverage ở đây = mapping tài liệu, KHÔNG có nghĩa code đã đáp ứng.**

## 3. Gaps / không được che giấu
- G-01: Chưa có acceptance của giảng viên về Web-only vs Flutter.
- G-02: Chưa xác nhận format/limit/metadata bắt buộc cho từng contest.
- G-03: Chưa chốt công thức điểm và tie-break.
- G-04: Chưa có benchmark AI đối với ảnh scan phim thật/AI-generated.
- G-05: Chưa có URL Jira, PR, CI, staging, test evidence thực tế.

[Governance](../01-governance/requirements-management.md) · [Test Matrix](../05-quality/test-matrix.md) · [Open Questions](../01-governance/open-questions.md)
