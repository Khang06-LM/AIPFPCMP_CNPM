# Backend (BE) — Technical Contract & Implementation Guide

> **Trạng thái:** Bản mô tả Backend đề xuất, không có mã nguồn Backend đính kèm trong gói tài liệu này. **Nguồn:** [P] dự kiến Node.js Express **hoặc** ASP.NET Core, PostgreSQL, JWT/RBAC, Cloud Storage; chưa được nhóm chốt stack cuối.

## 1. Trách nhiệm BE

- Xác thực JWT, 4 role, quyền owner và assignment; không tin role/user/time từ client.
- Nguồn dữ liệu chuẩn cho Contest, Enrollment, Roll, Frame, Submission, Validation, Review, Judge, Score, Ranking, Archive.
- Lưu metadata trong PostgreSQL, binary trong object storage; link file private có hạn.
- Kết nối AI async có retry và fallback; AI flags không tự đưa bài sang REJECTED.
- Audit các quyết định nhạy cảm, chống thao tác trùng (upload finalize, review, score finalize, publish).

## 2. Stack dự kiến

| Lớp | Lựa chọn trong kế hoạch | Quyết định |
|---|---|---|
| Runtime/API | Node.js/Express hoặc ASP.NET Core | **TBD D-11** |
| Database | PostgreSQL | Đề xuất [P] |
| Auth | JWT + RBAC | [G] |
| File storage | Firebase Storage / Azure Blob | TBD |
| API docs | OpenAPI/Swagger | [P] |
| AI | Python service / API | [P] |

**Không chạy lệnh cài đặt dưới đây như thể dự án đã có source**; chỉ áp dụng sau khi TV2 chốt tech stack và khởi tạo BE. Đừng báo thầy “deploy xong” nếu chưa có URL chạy được.

## 3. Phân lớp module đề xuất

```text
BE/
├── README.md
├── src/
│   ├── auth/            # login, RBAC, owner checks
│   ├── contests/        # contests, categories, criteria
│   ├── enrollments/     # đăng ký dự thi
│   ├── film/            # roll, frame, metadata
│   ├── submissions/     # upload finalize, validation
│   ├── reviews/         # manual decision, audit
│   ├── judging/         # assignment, score, ranking
│   ├── archive/         # public result / media policy
│   └── ai/              # jobs, status, predictions
├── migrations/          # sau khi đã chốt ERD
└── tests/               # unit/integration/API security
```

## 4. Endpoint contract — sơ bộ, CHƯA PHẢI API đã triển khai

| Method | Path (đề xuất) | Auth | FR | State / lỗi nổi bật |
|---|---|---|---|---|
| POST | `/auth/login` | Public | FR-01 | 401 sai credential, không lộ user existence |
| GET | `/auth/me` | Any authenticated | FR-01 | 401 hết phiên |
| POST | `/contests` | Organizer | FR-03 | 422 invalid deadline, 403 role |
| POST | `/contests/{id}/enrollments` | Participant | FR-04 | 409 duplicate, 422 closed |
| POST | `/film-rolls` | Participant | FR-05 | 422 invalid ISO |
| POST | `/film-rolls/{id}/frames` | Roll owner | FR-05 | 409 duplicate frame; 403 not owner |
| POST | `/uploads/init` | Participant | FR-06 | private upload object key, limit TBD |
| POST | `/submissions` | Participant | FR-06 | 422 invalid frame/deadline, 409 duplicate |
| GET | `/submissions/{id}/validation-report` | Owner/Organizer | FR-07 | rule-level pass/fail/error |
| POST | `/submissions/{id}/review` | Organizer scoped | FR-08 | 409 stale version, 422 missing reject reason |
| POST | `/judge-assignments` | Organizer scoped | FR-09 | 422 invalid judge/round |
| PUT | `/judge/scoresheets/{id}` | Assigned Judge | FR-10 | 422 invalid score, 403 not assigned |
| POST | `/judge/scoresheets/{id}/finalize` | Assigned Judge | FR-10 | 409 finalized, 422 incomplete |
| POST | `/contests/{id}/publish-results` | Organizer | FR-11 | 409 missing scores/tie-break |
| GET | `/archive` | Public | FR-12 | public fields only |
| GET | `/organizer/contests/{id}/analytics` | Organizer scoped | FR-16 | 403 other contest |

### JSON response mẫu (đề xuất)

```json
{
  "data": {"id": "<uuid>", "status": "SUBMITTED"},
  "meta": {"requestId": "<trace-id>"}
}
```

```json
{
  "error": {"code": "SUBMISSION_CLOSED", "message": "Cuộc thi đã hết hạn nhận bài", "field": null},
  "meta": {"requestId": "<trace-id>"}
}
```

## 5. Quy định validation/transaction

- Không chấp nhận datetime từ FE thay cho `submitted_at` do BE ghi.
- `Submission` chỉ tạo sau upload hoàn tất; nếu storage thành công nhưng DB fail phải dọn object mồ côi theo cơ chế đã chọn.
- `approve/reject`, `finalize score`, `publish` cần optimistic locking/version để hai người không ghi đè.
- `Criterion` đổi sau khi có ScoreSheet cần version hoặc bị khóa.
- Chỉ `Approved` submission được phân cho Judge; chỉ `Finalized` ScoreSheet đi vào ranking.
- Public archive trả metadata whitelist; không trả âm bản hoặc signed URL evidence private.
- AI job timeout ghi `PENDING/ERROR`, không tự loại bài.

## 6. Environment variables — chỉ liệt kê tên, KHÔNG CHỨA SECRET

```dotenv
DATABASE_URL=<set-locally>
JWT_SECRET=<set-locally>
OBJECT_STORAGE_BUCKET=<set-locally>
OBJECT_STORAGE_CREDENTIALS=<set-locally>
AI_SERVICE_URL=<set-locally>
```

Dùng `.env.example` chứa placeholder. Không commit `.env`, JSON service credentials, token, secret; nếu lộ phải rotate, không chỉ xóa commit.

## 7. Hướng dẫn chạy sau khi có source

1. TV2 xác nhận stack và cập nhật README này với **lệnh chính xác** thực sự đã thử trên máy sạch.
2. Tạo database/migrations/seed riêng; document cách cấu hình cloud và quyền bucket.
3. Chạy unit, integration, API security test; lưu log kết quả trên PR/Jira.
4. Tạo OpenAPI và khóa contract để FE/AI có thể mock.
5. Chỉ tuyên bố staging OK khi có URL và test smoke đạt.

## 8. Bằng chứng phải bổ sung khi code xong

`Node/.NET runtime version`, lệnh setup/run/test, env sample, migration version, API docs URL, staging URL, commit/tag, test PASS/FAIL, known issues.

[FR Index](../Docs/02-requirements/README.md) · [Entity & Data Dictionary](../Docs/03-domain/data-dictionary.md) · [Test Strategy](../Docs/05-quality/test-strategy.md)

## V3 — Backend business integrity / API error contract (đề xuất, chưa có code)

**Không coi endpoint ví dụ là đã triển khai.** Tech Lead phải chốt stack Express/ASP.NET theo D-11 và gắn Swagger thật khi có.

### Luồng finalize bài nộp (FR-06)

1. `POST /uploads/init` → upload_session_id + signed upload URL có hạn (optional design).
2. Storage upload → `POST /uploads/complete` → asset READY sau kiểm MIME/checksum/size.
3. `POST /submissions` với `Idempotency-Key` → BE kiểm JWT/owner/enrollment/contest clock/quota/file READY tại transaction.
4. `GET /me/submissions/{id}` hoặc query theo request-key → FE reconcile lost response.
5. Cleanup job xử lý asset không liên kết; không tự tính file tạm là bài đã nộp.

### Chuẩn lỗi JSON đề xuất

```json
{
  "error": {
    "code": "CONTEST_CLOSED",
    "message": "Cuộc thi đã hết thời hạn nhận bài.",
    "correlation_id": "request-trace-id",
    "details": []
  }
}
```

- `401` chưa xác thực, `403` không đủ quyền, `404` ẩn tài nguyên không có quyền xem, `409` state/version conflict, `422` business validation, `5xx` lỗi dependency.
- Không đưa stack trace, secret, URL âm bản riêng tư hoặc PII vào response và audit log.
- `ScoreSheet` cần `version` optimistic lock, finalize có idempotency; publication cần immutable snapshot.
- Nếu dịch vụ AI timeout, không tự reject bài; dùng AIJob + manual review fallback theo [Decision Log](../Docs/01-governance/open-questions.md).

**Chuẩn kiểm thử bắt buộc:** [Business Tests](../Docs/05-quality/business-scenario-tests.md), [Failure Matrix](../Docs/05-quality/failure-recovery-matrix.md), [Invariants](../Docs/03-domain/business-invariants.md).
