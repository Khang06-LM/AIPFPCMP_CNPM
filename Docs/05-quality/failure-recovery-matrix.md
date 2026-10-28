# FRM-01 — Failure & Recovery Matrix / Lỗi, khôi phục, hậu điều kiện

> **DRAFT, solution proposal [D]**. Phân biệt **lỗi dữ liệu nghiệp vụ** (422/409), **lỗi phân quyền** (401/403), **lỗi tạm thời hạ tầng** (5xx/timeout) và **kết quả chưa rõ do mất phản hồi**. Không chuyển một lần request timeout thành khẳng định chắc chắn thao tác thất bại.

## 1. Quy trình xử lý sự cố chung

`Detect → Preserve invariant → Classify → Retry/reconcile/compensate → Record evidence → Verify business state`.

- *Retry* chỉ an toàn khi thao tác idempotent hoặc có idempotency key / kiểm tra version.
- *Compensate* khi không thể transaction nguyên tử qua DB và Object Storage: có log, cleanup job, thời gian giữ file tạm TBD.
- *Reconcile* đọc lại trạng thái từ Backend khi FE không nhận được phản hồi.
- *Alert* nếu không thể phục hồi tự động. Không cho job retry vô hạn.
- Sau mọi khôi phục phải kiểm tra trạng thái nghiệp vụ và record liên quan, không chỉ kiểm tra HTTP 200.

## 2. Ma trận kiểm soát lỗi xuyên module

| ID | Điểm lỗi/điều kiện | Rủi ro nghiệp vụ | Cách phát hiện | Xử lý + recovery | Trạng thái dữ liệu phải còn | Test ID / phụ trách |
|---|---|---|---|---|---|---|
| F-01 | FE bắt đầu upload rồi mất mạng | File không hoàn tất | Timeout / upload token chưa READY | Abort/retry an toàn; cleanup temp | Không có Submission SUBMITTED | TC-BIZ-SUB-03 / TV3+TV2 |
| F-02 | Upload READY nhưng finalize sau close_at | Nộp muộn được tính hợp lệ | Backend so server time ngay commit | Reject, file tạm cleanup | Không có new SUBMITTED | TC-BIZ-SUB-01 / TV2 |
| F-03 | Client nhấn finalize hai lần / response mất | Tạo bài trùng | Trùng idempotency key | Trả status/id của lần đầu; GET reconcile | 1 logical Submission | TC-BIZ-SUB-02 / TV2+TV3 |
| F-04 | Storage xong, DB transaction lỗi | Orphan asset | Job reconciliation asset READY nhưng chưa linked | Schedule cleanup/mark orphan + audit | Không công nhận completed Submission | TC-BIZ-SUB-05 / TV2 |
| F-05 | Backend DB commit xong nhưng client nhận timeout | User gửi lặp | Request trace/idempotency record | GET status before retry, replay same key | Không ghi lặp | TC-BIZ-SUB-02 / TV2 |
| F-06 | Validation worker nhận job trùng | Hai report mâu thuẫn | unique(submission_id,version,rule_version) | Dedup job; latest report with provenance | 1 report hiệu lực/version | TC-BIZ-VAL-01 / TV2 |
| F-07 | AI service bị lỗi/timeouts | Không có cảnh báo | AIJob FAILED_RETRYABLE/UNAVAILABLE | Backoff bounded; fallback manual | Không auto-reject | TC-BIZ-AI-02 / TV5+TV4 |
| F-08 | AI flag sai (false positive) | Loại tác phẩm hợp lệ | Human review, appeal/evidence | Organizer override có lý do, lưu model/version | AIAnalysis tồn tại, ReviewDecision độc lập | TC-BIZ-AI-01 / TV4+TV5 |
| F-09 | 2 Organizer cùng duyệt bài | Quyết định ghi đè | row `version` mismatch | 409; refresh và quyết định lại | Không mất lịch sử / 1 quyết định hiệu lực | TC-BIZ-REVIEW-01 / TV4 |
| F-10 | APPROVED xuất hiện nghi vấn sau khi có điểm | Bảng xếp hạng sai | AI flag + incident / user report | UNDER_INVESTIGATION, freeze downstream theo D-16 | Giữ score lịch sử, không public kết quả sai | TC-BIZ-REVIEW-03 / TV4+TV2 |
| F-11 | Judge sửa từ 2 tab / finalize 2 lần | Điểm sai hoặc trùng | version, request key | Optimistic lock + GET reconcile | 1 ScoreSheet hiệu lực/version | TC-BIZ-SCORE-01 / TV4+TV2 |
| F-12 | Thay rubric sau khi chấm | Tính điểm khác thang | rubric_version mismatch | Chặn, hoặc version mới có approval | Bản chấm cũ không bị âm thầm đổi | TC-BIZ-SCORE-05 / TV4 |
| F-13 | 2 tác phẩm đồng giải thiếu tie-break | Tự chọn giải thiếu căn cứ | tie detector tại rank gate | Mark TIE_UNRESOLVED; hỏi Organizer | Không có published award sai | TC-BIZ-PUB-02 / TV2+TV4 |
| F-14 | Công bố kết quả sau khi DB ghi nhưng event archive lỗi | Public và archive lệch | outbox pending, reconcile | Retry archive idempotent; UI báo trạng thái đúng | Published snapshot không nhân đôi | TC-BIZ-PUB-03 / TV1+TV2 |
| F-15 | Sai kết quả sau công bố | Mất tin cậy, xóa lịch sử | Approved incident | Correction request; publish version mới, giữ v1 | v1/v2 truy vết được | TC-BIZ-PUB-04 / TV1+TV4 |
| F-16 | Private evidence URL bị lộ / hết hạn | Vi phạm riêng tư | Access log/403 | Signed URL ngắn; rotate/revoke quyền khi cần | Public không truy cập negative | TC-BIZ-ARCH-01 / TV2+TV3 |
| F-17 | Notification provider lỗi sau approve | Trạng thái bị hiểu sai | outbox failed | Retry có giới hạn; in-app fallback | Submission vẫn APPROVED | TC-BIZ-NOTIFY-01 / TV1 |
| F-18 | Kết quả analytics đếm trùng | Dashboard sai | So số row vs distinct id | Quy định truy vấn KPI, dedup + reconciliation | Metric đúng logic | TC-BIZ-METRIC-01 / TV5 |

## 3. Ví dụ quan trọng: `F-04` — Storage thành công, DB thất bại

**Không thể rollback Cloud Storage bằng DB transaction thông thường.** Thiết kế tham khảo:

1. Tạo UploadSession id và temporary object key giới hạn quyền.
2. Sau upload, server xác nhận object tồn tại và MIME/checksum/quota.
3. Transaction DB: kiểm deadline/quyền, tạo Submission và liên kết MediaAsset, ghi outbox `SUBMISSION_CREATED`.
4. Nếu bước (3) lỗi: đánh dấu session `ORPHAN_PENDING_CLEANUP`; cleanup job xóa object an toàn sau thời gian grace (TBD), có retry + log; không báo nộp thành công.
5. Nếu response mất sau khi (3) đã commit: `GET /submissions/by-request-key` hoặc retry cùng key để trả kết quả cũ; tuyệt đối không tạo bản thứ hai.

**Acceptance:** Không có bản ghi SUBMITTED thiếu asset READY; không có 2 Submission từ cùng request; orphan cuối cùng được xử lý theo quy trình và có audit. Chưa được báo đã triển khai.

## 4. Ví dụ quan trọng: `F-11` — Lost response sau save/finalize score

- FE không thể kết luận “chưa lưu” chỉ vì timeout.
- FE tải lại ScoreSheet theo id hoặc request key; hiển thị version/status từ Backend.
- Nếu response chứa 409 VERSION_CONFLICT: hiển thị lựa chọn tải lại, không tự ghi đè bản mới.
- Nếu FINALIZED thành công: lần finalize lặp không nhân đôi bảng điểm.

## 5. Cách ghi bằng chứng kiểm thử

| Field | Bắt buộc khi chạy test |
|---|---|
| Case ID | F-xx / TC-BIZ-xx |
| Build/Commit | SHA hoặc tag, môi trường test |
| Setup | Seed user/contest/submission IDs; giờ giả lập, rule_version |
| Action | Request/response, điều kiện lỗi đã mô phỏng |
| Expected | DB state, storage state, response, audit + notification |
| Actual | Kết quả đo được sau khi chạy |
| Verdict | PASS/FAIL/BLOCKED/NOT_RUN |
| Evidence | Link log đã ẩn bí mật, ảnh, CI hoặc PR |

**Hiện chưa có bằng chứng test thực tế được cung cấp**, toàn bộ trường Actual để trống/NOT_RUN.
