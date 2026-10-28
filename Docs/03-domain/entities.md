# DATA-01 — Entity Catalogue / Domain Model

> **Trạng thái:** DRAFT. Đề tài [G] mô tả dữ liệu ảnh phim, cuộc thi, chấm thi, AI; **tên bảng, thuộc tính, quan hệ và cardinality dưới đây là thiết kế [D]**, chưa phải ERD đã được nhóm/giảng viên duyệt.

## 1. Vì sao cần Entity Catalogue?

Để trả lời câu hỏi *“FR-06 nộp bài dùng những dữ liệu nào, các dữ liệu đó liên quan ra sao, tránh sai nguồn gốc phim bằng cách nào?”*; dùng một từ vựng dữ liệu thống nhất cho SRS, ERD, API, Test Case và Jira.

## 2. Danh mục thực thể

| Entity | Khóa chính/khóa ghép | Trường quan trọng (đề xuất) | Ý nghĩa & ràng buộc | Owner |
| --- | --- | --- | --- | --- |
| User | user_id | email UNIQUE; password_hash; status; created_at | Người dùng 4 vai trò; không lưu mật khẩu thô. | TV2 |
| Role | role_id | code UNIQUE; name | ADMIN/ORGANIZER/JUDGE/PARTICIPANT. | TV2 |
| UserRole | user_id + role_id | granted_at; granted_by | Many-to-many nếu user đa vai trò được phê duyệt. | TV2 |
| Contest | contest_id | organizer_id FK; title; description; open_at; close_at; status | close_at > open_at; lưu thời gian có timezone. | TV4 |
| Category | category_id | contest_id FK; name; active | unique(contest_id,name). | TV4 |
| ContestRound | round_id | contest_id FK; name; order_no; status | unique(contest_id,order_no); 1 round MVP. | TV4 |
| Criterion | criterion_id | contest_id/category_id/round_id FK; name; weight; max_score; version | Ghi version khi đổi scoring rubric. | TV4 |
| Prize | prize_id | contest_id/category_id FK; title; rank_order | Giải thưởng theo contest/category. | TV4 |
| Enrollment | enrollment_id | contest_id FK; participant_id FK; status; decided_at | unique(contest_id,participant_id) nếu quy chế áp dụng. | TV3 |
| FilmStock | film_stock_id | brand; stock_name; nominal_iso; active | Danh mục, phân biệt ISO nhà sản xuất và khai báo. | TV1 |
| Camera | camera_id | brand; model; active | Master data thân máy ảnh. | TV1 |
| Lens | lens_id | brand; model; active | Có thể không có lens riêng (máy compact). | TV1 |
| FilmLab | lab_id | name; location_text; active | Lab tráng phim là metadata, không nhất thiết là actor. | TV1 |
| FilmRoll | roll_id | owner_id FK; film_stock_id FK; camera_id FK; lens_id FK; iso_used; film_format; lab_id FK | Dữ liệu vật lý một cuộn; thuộc sở hữu Participant. | TV3 |
| FilmFrame | frame_id | roll_id FK; frame_number; notes; scan_info | unique(roll_id,frame_number) theo quy tắc đề xuất. | TV3 |
| MediaAsset | asset_id | object_key; mime_type; bytes; checksum_sha256; visibility; upload_status | Binary ở object storage, DB chỉ lưu object key và metadata. | TV2 |
| Submission | submission_id | contest_id/category_id/enrollment_id/frame_id/asset_id FK; submitted_at; status; version | Tách frame/source và tệp scan; kiểm owner, deadline. | TV3 |
| ValidationReport | validation_id | submission_id FK; rule_version; checks_json; status; checked_at | Audit được lý do fail từng rule. | TV2 |
| ReviewDecision | decision_id | submission_id FK; organizer_id FK; decision; reason; version; decided_at | Không xóa lịch sử khi có revision. | TV4 |
| JudgeAssignment | assignment_id | judge_id/category_id/round_id FK; assigned_at; status | Không gán trùng theo scope và chính sách. | TV4 |
| ScoreSheet | scoresheet_id | submission_id/round_id/judge_id FK; status; criterion_version; finalized_at | unique(submission_id,round_id,judge_id); khóa sau final. | TV4 |
| CriterionScore | criterion_score_id | scoresheet_id/criterion_id FK; score; comment | unique(scoresheet_id,criterion_id); score trong [0,max_score]. | TV4 |
| RankingSnapshot | ranking_id | contest_id/round_id/category_id FK; generated_at; status; version; algorithm_version | Snapshot tính điểm, đính chính bằng version. | TV2 |
| Award | award_id | ranking_id/submission_id/prize_id FK; published_at | Liên kết giải với kết quả đã công bố. | TV1 |
| ArchiveEntry | archive_id | submission_id/award_id FK; visibility; archived_at | Không công khai chứng từ tùy ý. | TV3 |
| AIJob | job_id | asset_id/submission_id FK; task_type; status; retry_count; model_version | Xử lý bất đồng bộ, retry có giới hạn. | TV5 |
| AIAnalysis | analysis_id | job_id FK; label; score; threshold; findings_json; reviewed | Cảnh báo + dữ liệu đánh giá, không tự quyết định vi phạm. | TV5 |
| Tag | tag_id | slug UNIQUE; display_label; language | Từ khóa chuẩn hóa. | TV5 |
| MediaTag | asset_id + tag_id | origin AUTO/MANUAL; status; approved_by | Tách tag thủ công và tag AI. | TV5 |
| Notification | notification_id | user_id FK; event_key UNIQUE; message; read_at | In-app notification là giải pháp đề xuất. | TV1 |
| AuditLog | audit_id | actor_id FK; action; object_type; object_id; occurred_at; metadata_safe | Không ghi token/password/PII không cần. | TV1 |

## 3. Quan hệ và cardinality chính

- `User (1) — (N) FilmRoll`: chủ sở hữu cuộn.
- `FilmRoll (1) — (N) FilmFrame`: một cuộn nhiều khung; số khung duy nhất trong cuộn theo quy tắc đề xuất.
- `Contest (1) — (N) Category/ContestRound/Enrollment/Submission`.
- `User (N) — (N) Contest` qua `Enrollment`.
- `FilmFrame (1) — (N) Submission` **nếu** quy chế cho phép cùng ảnh thi nhiều cuộc thi; nếu chỉ 1 cần constraint nghiệp vụ khác.
- `MediaAsset (1) — (N) Submission` chỉ khi cho reuse file; MVP một submission tham chiếu một ảnh chính.
- `Submission (1) — (N) ReviewDecision/ValidationReport/AIAnalysis` cho phép lưu lịch sử.
- `JudgeAssignment` nối Judge với Category/Round; `ScoreSheet` nối Judge–Submission–Round; `CriterionScore` nối ScoreSheet–Criterion.
- `RankingSnapshot (1) — (N) Award` và `Award (1) — (0..1) ArchiveEntry` tùy chính sách công bố.

## 4. Quy tắc toàn vẹn ưu tiên cho hệ thống

1. **Source provenance:** Mọi bài nộp phải có `frame_id` hợp lệ; `FilmFrame.roll_id → FilmRoll.owner_id` phải tương ứng `Enrollment.participant_id` của Submission.
2. **Contest membership:** `Submission.contest_id`, `Category.contest_id` và `Enrollment.contest_id` phải giống nhau; không chỉ kiểm FK riêng lẻ.
3. **Deadline:** `submitted_at` lấy từ server, không từ trình duyệt; so với rule snapshot của cuộc thi.
4. **Scoring independence:** `ScoreSheet` chỉ tạo cho Judge được assignment và Submission Approved.
5. **Evidence privacy:** `MediaAsset.visibility` không mặc định public đối với negative/contact sheet.
6. **Concurrent edits:** `Submission.version`, `ReviewDecision.version` và ScoreSheet status hỗ trợ chống ghi đè.
7. **Historical integrity:** Thay đổi criterion cần version; ranking dựa trên bộ rubric đã chấm.
8. **Retention:** Quy tắc backup/xóa/consent chưa chốt, ghi `TBD`, không coi dùng cloud là lưu trữ vĩnh viễn.

## 5. Các điểm cần BE/BA xác nhận trước khi migration

- Một user có nhiều role hay một role? Entity `UserRole` chỉ dùng khi chính sách multi-role được duyệt.
- Một bài có nhiều ảnh hay một ảnh? MVP giả định một MediaAsset ảnh chính; negative là asset chứng cứ riêng.
- Frame được nộp nhiều cuộc thi không? Ảnh có được re-submit sau reject không?
- Có version hóa toàn bộ ScoreSheet hay chỉ sửa bằng event reopen? 
- Có bắt buộc một vòng duyệt Enrollment không?
- Tên bảng/field thực tế phải thống nhất với [Data Dictionary](data-dictionary.md) và OpenAPI ở `BE/README.md`.

## 6. Truy vết

[Data Dictionary](data-dictionary.md) · [Business Rules](business-rules.md) · [Core Flows](../00-overview/end-to-end-flows.md) · [FR Index](../02-requirements/README.md)

## 7. V3 — Thuộc tính bổ sung để xử lý lỗi, audit và đồng bộ liên module

> **[D]** Đề xuất data model chưa phải migration đã viết. Cần rà soát field với Tech Lead và cập nhật ERD trước khi code.

| Entity / cấu trúc đề xuất | Trường bổ sung | Mục đích nghiệp vụ / constraint |
|---|---|---|
| UploadSession (mới, optional nếu có session upload) | `upload_session_id`, `owner_id`, `object_key`, `status`, `expires_at`, `checksum` | Phân biệt file tạm với file READY/linked, cleanup khi orphan; một asset không coi là nộp bài |
| Submission | `request_key`, `metadata_snapshot`, `rule_snapshot_version`, `version`, `submitted_at` | Idempotency; xác định phiên metadata/thể lệ khi nộp; chống cập nhật cạnh tranh |
| ReviewDecision | `review_revision`, `previous_decision_id`, `reason`, `actor_id`, `created_at` | Append-only review, không xóa quyết định đã có |
| ReviewIncident (tùy chọn) | `incident_id`, `submission_id`, `opened_by`, `reason`, `resolution`, `resolved_at` | Re-review sau APPROVED, không dùng cảnh báo AI để trực tiếp sửa status |
| ScoreSheet | `version`, `finalized_at`, `rubric_version`, `reopened_by`, `reopen_reason` | Lost response / optimistic lock / chốt và mở lại có kiểm soát |
| RankingSnapshot | `version`, `status`, `source_score_revision`, `approved_by`, `supersedes_id` | Published snapshot immutable, correction version mới |
| NotificationOutbox (nếu chọn outbox pattern) | `event_id`, `aggregate_id`, `status`, `attempt`, `last_error` | Notification retry idempotent không rollback nghiệp vụ |
| AIAnalysis | `model_version`, `threshold`, `review_status`, `evidence_link` | AI chỉ flag, human review riêng |
| ArchiveEntry / optional Consent | `visibility`, `consent_version`, `withdrawal_requested_at` | Public artifact không đồng nghĩa cho public âm bản |

**Invariant cross-entity:** [INV-01..14](business-invariants.md). **State/guards:** [STATE-01](state-transitions.md). **Recovery:** [FRM-01](../05-quality/failure-recovery-matrix.md).

### Lưu ý thực thi DB

- FK/unique chỉ đảm bảo một phần; `Submission.contest_id = Category.contest_id = Enrollment.contest_id` cần kiểm ở BE transaction hoặc composite FK nếu khả thi.
- `request_key` unique theo `participant_id + business_operation`, **không** unique toàn hệ thống nếu client sinh key trùng ở người khác; key có retention TBD.
- `status`, `version` phải đi cùng cập nhật điều kiện (`UPDATE ... WHERE id = ? AND version = ? AND status = ?`), kiểm `row_count=1`.
- Không lưu raw password, API keys, ảnh âm bản hoặc byte ảnh trong AuditLog.
- Các field mới chỉ thêm vào ERD chính thức sau review; không khai báo đã tạo bảng trong code.
