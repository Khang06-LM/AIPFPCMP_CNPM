# GAP-01 — Requirement Gap Analysis và quyết định làm trong MVP

> **Version 1.0 [D]**. Bản phân tích chênh lệch giữa tài liệu đề tài giảng viên [G], kế hoạch nhóm [P] và những quyết định còn chưa đủ bằng chứng. **Không phải biên bản thống nhất với thầy**.

## 1. Các loại gap

- **Business gap:** chính sách không xác định (số lượng bài, deadline grace, tie-break, sửa ảnh, quyền công bố).
- **System gap:** chức năng đề ra nhưng chưa có cơ chế lưu trạng thái, recovery, auth hoặc API contract.
- **Evidence gap:** có document nhưng chưa có PoC/test/build/PR chứng minh.
- **Capacity gap:** chưa đo được số giờ khả dụng, công việc chưa chia khả thi theo module.

## 2. Gap register / ưu tiên trước khi code

| GAP | Vấn đề và liên quan | Tác động nếu bỏ qua | Phương án xử lý MVP | Owner | Quyết định / bằng chứng cần bổ sung | Ưu tiên |
|---|---|---|---|---|---|---|
| GAP-01 | D-02: quota bài, sử dụng lại Frame (FR-06) | Nộp quá hạn mức, data model sai | Config `max_submissions` per contest/category, default TBD | TV1/TV4 | GV/group approves + TC quota | Blocker |
| GAP-02 | D-03: Enrollment approve (FR-04) | Gửi bài khi chưa đủ tư cách | Explicit enrollment status gate, policy flag TBD | TV4 | Enrollment flow approved | Blocker |
| GAP-03 | D-04: MIME/size (FR-06/07) | Upload quá tốn storage hoặc reject nhầm | JPEG MVP được đề xuất, TIFF phải xác nhận | TV2/TV3 | PoC upload + policy value | Blocker |
| GAP-04 | D-05: Judges/rounds (FR-09..11) | Xếp hạng sai khi thiếu phiếu | Tối thiểu một vòng E2E, chưa bỏ flow nhiều vòng nếu thầy bắt buộc | TV4 | SRS rule + judge coverage test | Blocker |
| GAP-05 | D-06: tie-break/scoring formula (FR-10/11) | Công bố thắng cuộc thiếu căn cứ | Không public khi tie unresolved | TV2/TV4 | signed-off rule and scoring test | Blocker |
| GAP-06 | D-07/08: bằng chứng và công khai (FR-05/12) | Rò negative, vi phạm quyền tác giả | evidence private; archive only after policy confirmation | TV1/TV3 | consent/visibility rules | High |
| GAP-07 | Re-review sau APPROVED (FR-08..11) | Chấm, ranking và archive không nhất quán | UNDER_INVESTIGATION + correction flow (D-16) | TV4 | state machine, policy & E2E | High |
| GAP-08 | DB–Storage không transaction nguyên tử (FR-06) | Orphan binary / báo thành công giả | Idempotency + cleanup/reconciliation; nếu chưa làm phải ghi rõ giới hạn | TV2 | failure injection TC-BIZ-SUB-05 | High |
| GAP-09 | Cạnh tranh cập nhật review/scores (FR-08/10) | Last-write-wins làm mất điểm | row version optimistic lock | TV2/TV4 | TC-BIZ-REVIEW-01, SCORE-01 | High |
| GAP-10 | AI-generated detection độ chính xác không ổn định (FR-14) | Loại oan thí sinh | Warning only; manual override + evaluation set | TV5 | PoC precision/recall, false positives | High |
| GAP-11 | Hệ thống Web+Flutter+AI trong 8 tuần | Quá tải, demo dở | Web responsive MVP, Flutter là option *nếu GV đồng ý* | TV1 | Scope sign-off D-01, hours | High |
| GAP-12 | 284 SP quy đổi 1 SP=0,5 ngày chưa phản ánh effort | Ước lượng ảo | Theo dõi effort hours + giờ có thể làm từng người | TV1 | TV capacity confirmed | High |
| GAP-13 | Chỉ có tài liệu; chưa có evidence implementation | Không chứng minh được acceptance | Tạo PR/test/record thật, giữ `NOT RUN` | Cả nhóm | link staging/test logs | High |
| GAP-14 | Notification vs DB commit (FR-18) | User nhận sai/lặp trạng thái | outbox hoặc in-app retry idempotent | TV1/TV2 | chaos test | Medium |

## 3. Definition of Ready (để một requirement bắt đầu triển khai)

- Có owner, actor, main/alt/exception flow rõ ràng.
- Các Business Rule gây thay đổi DB/API được phê duyệt hoặc có giả định được công bố và người chịu trách nhiệm quyết định.
- Có Entity, API input/output, role matrix, state guard và test design.
- Có effort hours + time window, dependency, fallback, giới hạn MVP.
- Không có blocker cần giảng viên xác nhận nhưng vẫn bị giả định là “đã chốt”.

## 4. Definition of Done (để chuyển từ thiết kế sang verified)

- PR merged và CI pass; code chạy trên staging; reviewer khác người viết xác nhận.
- Functional AC có test run với seed, Actual, Evidence link, PASS.
- Negative test và test phân quyền có kết quả; audit/recovery được xác nhận khi liên quan.
- Requirements/Entity/API README và Traceability được cập nhật theo code thực.
- Không còn lỗi critical/blocker chưa quyết định; limitation được ghi trong báo cáo demo.

## 5. Quy tắc quyết định cắt scope

Không âm thầm cắt 5 Core Flow hay AI function mà đề tài gốc nêu. Khi giờ thực tế thiếu, ưu tiên giảm **độ sâu** (dashboard nâng cao, UI gallery phức tạp, Flutter nếu được cho phép, nhiều vòng nâng cao) và **xin duyệt** khi phạm vi khác văn bản [G]. Xem [capacity estimation](capacity-estimation.md).
