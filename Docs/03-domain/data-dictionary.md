# DATA-02 — Data Dictionary / Schema Draft

> `varchar(255)`, UUID, JSONB và timestamp là **gợi ý** theo PostgreSQL trong kế hoạch, không phải migration đã chạy. Entity Catalogue xem [DATA-01](entities.md).

## 1. Quy ước kiểu và chuẩn hóa

- ID: `uuid`/ULID nhất quán toàn repo; không lộ thứ tự đếm người dùng nếu không cần.
- Thời gian: `timestamptz` ở UTC, FE hiển thị Asia/Ho_Chi_Minh; deadline dựa thời gian BE.
- Các status: enum/check constraint; transition được API kiểm tra thêm.
- Monetary / scores: `numeric` không dùng float khi cộng điểm hoặc quy đổi tỷ lệ chính thức.
- `object_key` lưu định danh object; không lưu signed URL hết hạn làm dữ liệu cố định.
- Trường `*_version` phục vụ optimistic locking và lịch sử.

## 2. Từ điển trường ảnh phim và nộp bài — trọng tâm phản biện

| Entity.field | Kiểu gợi ý | Bắt buộc? | Rule / ví dụ / bảo mật |
| --- | --- | --- | --- |
| FilmRoll.owner_id | UUID FK User | Có | Mỗi roll chỉ thuộc một participant; API lấy từ token. |
| FilmRoll.film_stock_id | UUID FK FilmStock | Tùy quy chế; MVP có | Film stock từ danh mục; không chứng minh tính nguyên bản. |
| FilmRoll.iso_used | integer >0 | Tùy quy chế; MVP có | Khác nominal ISO, cần phân biệt. |
| FilmRoll.film_format | varchar/enum | Tùy quy chế | 35mm/120 là ví dụ, định nghĩa enum cần chốt. |
| FilmFrame.frame_number | varchar(32) | Có | Có thể có ký hiệu ngoài số, unique trong roll. |
| FilmFrame.scan_info | jsonb/text | Tùy quy chế | Scanner/DPI/lab là metadata khai báo. |
| MediaAsset.object_key | varchar UNIQUE | Có | Private object key, không công khai trực tiếp. |
| MediaAsset.mime_type | varchar | Có | Kiểm tra content type thực tế, không tin extension. |
| MediaAsset.bytes | bigint >=0 | Có | Max size TBD theo quota/thể lệ. |
| MediaAsset.checksum_sha256 | char(64) | Khuyến nghị | Phát hiện trùng byte, không phát hiện mọi ảnh giống. |
| Submission.contest_id | UUID FK | Có | Phải khớp enrollment và category. |
| Submission.frame_id | UUID FK | Có | Frame thuộc Participant đăng nhập. |
| Submission.status | enum/check | Có | Transition theo Core Flow/state. |
| Submission.submitted_at | timestamptz | Khi finalize | Thời gian do server ghi. |
| ValidationReport.checks_json | jsonb | Có | Lưu từng rule pass/fail/ERROR; dùng snapshot version. |
| ReviewDecision.reason | text | Khi reject | Không nhận chuỗi rỗng; lịch sử không xóa. |
| Criterion.weight | numeric | Có khi dùng weighted score | 0 < weight, tổng trọng số theo policy chưa duyệt. |
| CriterionScore.score | numeric | Có khi finalize | 0 ≤ score ≤ criterion.max_score. |
| ScoreSheet.status | enum | Có | DRAFT/FINALIZED; chỉ Judge assigned. |
| RankingSnapshot.version | integer | Có | Snapshot của thuật toán tính điểm. |
| AIAnalysis.score | numeric nullable | Có thể rỗng | Không gọi là probability nếu mô hình chưa hiệu chuẩn. |
| ArchiveEntry.visibility | enum | Có | PUBLIC/PRIVATE theo consent, không tự public âm bản. |

## 3. Entity → nguồn quản lý → kiểm tra

| Entity | Nguồn tạo | Validation trọng yếu | FR |
| --- | --- | --- | --- |
| Contest | Organizer | deadline; trạng thái publish; rights | FR-03 |
| Enrollment | Participant/Organizer | unique contest+participant; policy status | FR-04 |
| FilmRoll/FilmFrame | Participant | owner; unique roll+frame; ISO | FR-05 |
| MediaAsset/Submission | Participant + BE | private storage; deadline; frame/enrollment | FR-06 |
| ValidationReport | BE rules | rule version; fail vs service error | FR-07 |
| ReviewDecision | Organizer | reason; concurrency; audit | FR-08 |
| JudgeAssignment | Organizer | role; category/round; uniqueness | FR-09 |
| ScoreSheet/CriterionScore | Judge | range; finalized; assignment | FR-10 |
| RankingSnapshot/Award | Organizer + BE | final only; tie-break; version | FR-11 |
| ArchiveEntry | System | publish & rights | FR-12 |
| AIJob/AIAnalysis/MediaTag | AI service + Organizer | model version; async fallback | FR-13..15 |

## 4. Data lifecycle / privacy

- `DRAFT` có thể sửa nhưng `SUBMITTED` phải giữ snapshot dữ liệu chấm để truy vết.
- Audit và kết quả cần retention policy; chưa có số năm cụ thể nên `TBD`.
- Media chứng cứ riêng tư chỉ được truy cập có hạn; nếu không có quyền, trả 403/404.
- Khi xóa dữ liệu hoặc rút consent, nhóm phải xác nhận tác động đến lịch sử công bố, sao lưu và luật liên quan; chưa có chính sách được duyệt.

## 5. Validation checklist trước chốt ERD
- [ ] Cùng một từ chỉ một entity (không dùng Entry/Submission lẫn lộn).
- [ ] Mỗi FK có cardinality và on-delete policy.
- [ ] Có unique chống bài/đăng ký/điểm trùng.
- [ ] Có cách lưu lịch sử điểm và công bố.
- [ ] Mỗi trường liên quan quyền riêng tư được phân loại.
- [ ] Đã cập nhật migration + OpenAPI đồng bộ.

## V3 — Contract quan trọng bổ sung (proposed; chưa migration)

| Trường | Type minh họa | Ràng buộc gợi ý | Mục tiêu |
|---|---|---|---|
| Submission.request_key | UUID/VARCHAR(64) | UNIQUE(participant_id, request_key), NOT NULL khi finalize | Chống nhấn gửi lặp/lost response |
| Submission.metadata_snapshot | JSONB | NOT NULL sau SUBMITTED | Ghi provenance thời điểm nộp |
| Submission.version | INT | DEFAULT 1, increment atomic | Optimistic lock review/edits |
| Submission.submitted_at | TIMESTAMPTZ | server-generated, NOT NULL sau finalize | Hạn nộp không lệ thuộc browser |
| ScoreSheet.version | INT | increment on each save | Tránh ghi đè từ 2 tab |
| ScoreSheet.rubric_version | INT/VARCHAR | NOT NULL khi FINALIZED | Không chấm theo rubric lẫn lộn |
| RankingSnapshot.supersedes_id | FK nullable | FK RankingSnapshot | Correction sau publish, giữ bản cũ |
| AIAnalysis.model_version | TEXT | NOT NULL | Khả năng giải thích/đánh giá AI theo model |
| NotificationOutbox.event_id | UUID | UNIQUE | Retry không phát thông báo logic trùng |

**Các trường và SQL chỉ là định hướng [D]**. Đối chiếu với [Entity](entities.md), [Business Invariants](business-invariants.md), [API Contract BE](../../BE/README.md).
