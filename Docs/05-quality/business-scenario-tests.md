# BIZ-TC-01 — Kịch bản phản biện nghiệp vụ, dữ liệu seed và oracle kiểm thử

> **Test design only — NOT RUN**. Các định danh `U-A`, `C-01` và mốc giờ chỉ là **dữ liệu seed đề xuất**, không phải dữ liệu production. Test phải xác minh cả **UI, API, DB, Storage, audit** khi phù hợp.

## 1. Seed dùng chung trước khi chạy

| Seed | Giá trị giả lập | Mục đích |
|---|---|---|
| U-P1 | Participant A, enrolled/approved contest C-01 | Người nộp hợp lệ |
| U-P2 | Participant B, sở hữu roll R-B | Kiểm tra chéo quyền |
| U-O1 | Organizer C-01 | Duyệt/chốt công bố |
| U-J1, U-J2 | Judges assigned round 1 category CAT-01 | Chấm và thiếu phiếu |
| C-01 | open_at 2026-11-01 00:00+07, close_at 2026-11-02 00:00+07 | Giả lập deadline; không phải thời hạn thật |
| R-A / F-A | Roll của U-P1, frame no. 01 | Liên kết provenance |
| AS-01 | JPEG bytes hợp lệ, asset READY | Nộp thành công |
| AS-02 | File `.jpg` nhưng payload không phải ảnh | MIME giả |
| S-01 | Submission APPROVED của U-P1 | Duyệt và chấm |
| SS-01 | ScoreSheet DRAFT version=2 của U-J1 | Concurrent write |
| AI-FLAG | Cảnh báo với model_version test | Kiểm tra override, không auto reject |

**Giả định cần hỏi:** Số bài tối đa (D-02), file types (D-04), Judge count (D-05), scoring/tie-break (D-06), re-review (D-16).

## 2. Các kịch bản có expected rõ ràng

| Test ID | Điều kiện và bước tái hiện | HTTP/kết quả hiển thị dự kiến | Oracle kiểm DB/State |
|---|---|---|---|
| TC-BIZ-SUB-01 | U-P1 upload AS-01 trước close_at; giả lập thời gian backend > close_at khi finalize | 409 CONTEST_CLOSED | 0 Submission SUBMITTED mới; file là temp/pending cleanup |
| TC-BIZ-SUB-02 | Gửi 2 POST finalize đồng thời với cùng client_request_id=REQ-1 | Cùng submission_id hoặc retry status hợp lệ | `COUNT(submission WHERE request_key='REQ-1')=1` |
| TC-BIZ-SUB-03 | Ngắt mạng giữa upload, chưa complete asset | FE hiển thị retry, không “đã nộp” | Không có SUBMITTED trỏ asset incomplete |
| TC-BIZ-SUB-04 | U-P1 gửi frame của U-P2 | 403/404; không lộ metadata riêng | `submission_count` không đổi |
| TC-BIZ-SUB-05 | Storage OK, ép DB rollback | 5xx có correlation_id | Không SUBMITTED; orphan cleanup tracked |
| TC-BIZ-VAL-01 | Worker xử lý cùng submission_version + rule_version hai lần | Report vẫn là một bản hiệu lực/version | unique validation effective key |
| TC-BIZ-REVIEW-01 | Hai Organizer mở version 1; A quyết định approve; B reject trên version 1 | A OK; B 409 VERSION_CONFLICT | Không ghi đè quyết định A; B có thể refresh |
| TC-BIZ-REVIEW-03 | S-01 đã APPROVED và có draft score; phát hiện flag | Chuyển UNDER_INVESTIGATION bởi role được phép | Audit quyết định cũ còn; scoring/ranking gate kiểm trạng thái mới |
| TC-BIZ-REVIEW-04 | Organizer muốn reject không có reason | 422, hiển thị trường lý do bắt buộc | Không có ReviewDecision hợp lệ thiếu lý do |
| TC-BIZ-SCORE-01 | SS-01 save từ tab A version 2 → v3; tab B save version 2 | B 409 VERSION_CONFLICT | Dữ liệu v3 không bị ghi đè |
| TC-BIZ-SCORE-02 | Judge khác chưa được phân công POST score S-01 | 403 | Không phát sinh CriterionScore |
| TC-BIZ-SCORE-03 | Judge nhập score > max_score/thiếu criterion | 422 field errors | Phiếu điểm không final một phần |
| TC-BIZ-SCORE-04 | SS-01 FINALIZED rồi gọi PUT draft | 409 | Giá trị FINALIZED không đổi; correction qua audit nếu duyệt |
| TC-BIZ-SCORE-05 | Rubric v1 có FINALIZED, Organizer đổi trọng số in-place | 409 hoặc đề nghị v2 có approval | Score dùng v1 vẫn nguyên |
| TC-BIZ-PUB-01 | Ranking còn 1 phiếu điểm bắt buộc DRAFT | 409 BLOCKED_MISSING_SCORES | Không tồn tại published snapshot mới |
| TC-BIZ-PUB-02 | Hai bài đồng 9.5, chưa có tie-break D-06 | 409/blocked TIE_UNRESOLVED | Không gán Award tùy tiện |
| TC-BIZ-PUB-03 | Publish commit thành công, outbox gửi archive event timeout | API báo đúng publish state; retry archive riêng | 1 snapshot chính, archive idempotent |
| TC-BIZ-PUB-04 | Publication v1, xác nhận sai → approve correction v2 | v2 công khai, v1 còn lịch sử | Không overwrite v1, audit có reason |
| TC-BIZ-AI-01 | AI-FLAG cảnh báo sai, Organizer xem evidence và approve | Approved có review reason | AIAnalysis vẫn còn; không auto REJECTED |
| TC-BIZ-AI-02 | AI service timeout, bài vượt qua format/metadata validation | Chuyển manual review / AI_UNAVAILABLE | Không auto REJECTED chỉ do timeout |
| TC-BIZ-ARCH-01 | Public request signed URL âm bản của S-01 | 403/404 | Evidence asset private; access log có thể kiểm |
| TC-BIZ-NOTIFY-01 | Approve commit, lỗi notification queue, retry 2 lần | User cuối cùng nhận 1 logical notification | Submission Approved giữ nguyên; event_key unique |
| TC-BIZ-METRIC-01 | Một logical submission retry 3 lần | Dashboard hiển thị 1 | DISTINCT submission_id count=1 |

## 3. Kịch bản xuyên module E2E bắt buộc demo

**E2E-01 Happy:** Organizer tạo contest/hạng mục/rubric → Participant enrollment được duyệt → tạo roll/frame → upload/finalize submission → auto validation → Organizer approve → Judge assigned score/finalize → ranking snapshot → Organizer publish → archive ảnh đoạt giải. **Mỗi bước lưu object id và trạng thái.**

**E2E-02 Unhappy:** Participant upload muộn → không có bài SUBMITTED → không xuất hiện trong hàng duyệt/chấm/xếp hạng.

**E2E-03 Unhappy:** AI flag sai → Organizer review evidence → có thể approve theo chính sách; không có quyết định loại tự động.

**E2E-04 Unhappy:** Một Judge thiếu phiếu → kết quả không được tự công bố; phải giải quyết theo policy D-05.

**E2E-05 Recovery:** Score save response timeout → GET đọc lại, không nhân đôi/ghi đè; archive/notification có thể khôi phục độc lập.

## 4. Test Run Record (copy khi thực thi)

```text
Case ID:
Test build / commit:
Tester / date:
Environment:
Seed IDs + rule version:
Steps executed:
Expected:
Actual:
API/DB/Storage verification:
Evidence link:
Verdict: NOT_RUN | PASS | FAIL | BLOCKED
Defect Jira key (nếu FAIL):
```

Chỉ khi có test run thật mới được thay `NOT_RUN` thành `PASS`.
