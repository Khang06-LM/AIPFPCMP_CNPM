# FR-18 — Thông báo nghiệp vụ và truy vết thay đổi

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-18 |
| Nhóm nghiệp vụ | Xuyên suốt 5 Core Flow |
| Người dùng/actor | System; Participant; Organizer; Judge |
| Chủ sở hữu tài liệu | TV1 – Lê Minh Khang; TV2 hỗ trợ |
| Mức ưu tiên đề xuất | SHOULD – hỗ trợ vận hành |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-11; FPC-24; FPC-33 (hỗ trợ) |
| Nguồn và mức suy diễn | [G] theo dõi trạng thái/công bố; [P] audit log & thông báo; chi tiết notifier là [D] |
| Phụ thuộc | FR-04; FR-06; FR-08; FR-10; FR-11 |
| Entity | Notification; AuditLog; Submission; User |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **thông báo nghiệp vụ và truy vết thay đổi** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `System; Participant; Organizer; Judge` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Thông báo nghiệp vụ và truy vết thay đổi; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không bảo đảm gửi email/SMS thời gian thực ở MVP; audit không phải blockchain bất biến.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Có sự kiện thay đổi trạng thái và quyền nhận thông báo rõ ràng.

### 3.2 Đầu vào (Inputs)
- event_type, recipient_id, object_id, message template, seen status
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **Notification; AuditLog; Submission; User** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Nghiệp vụ sinh sự kiện (enrollment approved, submission rejected, results published).
2. BE tạo notification in-app gắn user và object, không chứa file riêng tư trong payload.
3. FE hiển thị danh sách thông báo theo tài khoản.
4. Người dùng xem chi tiết và đánh dấu đã đọc.
5. Admin/Organizer truy vấn AuditLog theo phạm vi quyền khi cần giải trình.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-18-01 | Gửi notification thất bại | Giữ trạng thái nghiệp vụ đã commit, retry queue; không rollback chấm điểm vì lỗi thông báo. | Luồng chính không bị chặn. |
| EX-FR-18-02 | Người dùng không còn quyền truy cập object | Không hiển thị nội dung chi tiết, dẫn đến trang 403. | Không lộ dữ liệu. |
| EX-FR-18-03 | Sự kiện bị gửi lại | Dedupe theo event ID. | Không spam. |
| EX-FR-18-04 | Người dùng đánh dấu đã đọc nhiều lần | Idempotent, giữ read_at ban đầu. | Không lỗi. |
| EX-FR-18-05 | Thông báo kết quả trước thời gian publish | Chặn theo publish status. | Không lộ kết quả. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-60: Thông báo không quyết định trạng thái nghiệp vụ; thay đổi trạng thái là nguồn sự thật.
2. **[D – đề xuất, cần duyệt]** BR-61: Không gửi dữ liệu nhạy cảm cho người nhận sai.
3. **[D – đề xuất, cần duyệt]** BR-62: Không được xóa log làm mất dấu vết quyết định quan trọng.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `Notification; AuditLog; Submission; User`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-04](../functional/FR-04-enrollment.md); [FR-06](../functional/FR-06-photo-submission.md); [FR-08](../functional/FR-08-manual-review.md); [FR-10](../functional/FR-10-judging-scoring.md); [FR-11](../functional/FR-11-ranking-result-publication.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `GET /me/notifications; PATCH /me/notifications/{id}/read; GET /admin/audit`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-18-01 | Mỗi sự kiện duyệt/từ chối hợp lệ có thể tạo thông báo đến đúng user. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-18-02 | Notification lỗi không làm hỏng trạng thái nghiệp vụ. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-18-03 | Không hiện nội dung chưa publish. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-18-04 | AuditLog có thời gian/actor/object/action đúng. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-18-05 | Khi gửi notification thất bại, hệ thống phải: Giữ trạng thái nghiệp vụ đã commit, retry queue; không rollback chấm điểm vì lỗi thông báo. Kết quả: Luồng chính không bị chặn. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-18-06 | Khi người dùng không còn quyền truy cập object, hệ thống phải: Không hiển thị nội dung chi tiết, dẫn đến trang 403. Kết quả: Không lộ dữ liệu. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-18-07 | Khi sự kiện bị gửi lại, hệ thống phải: Dedupe theo event ID. Kết quả: Không spam. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-18-08 | Khi người dùng đánh dấu đã đọc nhiều lần, hệ thống phải: Idempotent, giữ read_at ban đầu. Kết quả: Không lỗi. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-18-09 | Khi thông báo kết quả trước thời gian publish, hệ thống phải: Chặn theo publish status. Kết quả: Không lộ kết quả. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-18-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-18-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-18-02 | Unhappy / alternate | Gửi notification thất bại | AC-FR-18-05 | Luồng chính không bị chặn. |
| TC-FR-18-03 | Unhappy / alternate | Người dùng không còn quyền truy cập object | AC-FR-18-06 | Không lộ dữ liệu. |
| TC-FR-18-04 | Unhappy / alternate | Sự kiện bị gửi lại | AC-FR-18-07 | Không spam. |
| TC-FR-18-05 | Unhappy / alternate | Người dùng đánh dấu đã đọc nhiều lần | AC-FR-18-08 | Không lỗi. |
| TC-FR-18-06 | Unhappy / alternate | Thông báo kết quả trước thời gian publish | AC-FR-18-09 | Không lộ kết quả. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi cao với in-app notification; email chỉ bổ sung nếu có thời gian.
- **Giới hạn giải pháp:** Không bảo đảm gửi email/SMS thời gian thực ở MVP; audit không phải blockchain bất biến.
- **Ước lượng thử để lập kế hoạch:** 12 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
- **Tiêu chí xác nhận khả thi:** có PoC hoặc test trên dữ liệu mẫu và bằng chứng cấu hình thực; trường hợp không đạt phải nêu rõ phần cắt giảm và hỏi giảng viên nếu thuộc yêu cầu gốc.
- **Trách nhiệm quyết định:** TV1 BA/PM xác nhận nghiệp vụ, TV2 xác nhận tác động API/DB, owner module kiểm tra test, giảng viên duyệt những ràng buộc phạm vi quan trọng.

## 12. Checklist review riêng cho yêu cầu này

- [ ] Actor và quyền được mô tả rõ, có kiểm tra trái quyền.
- [ ] Tất cả bước Main Flow có trạng thái dữ liệu sau bước.
- [ ] Có ít nhất một happy case và các unhappy case thực tế ở mục 5.
- [ ] Đã đối chiếu BR với thể lệ cuộc thi hoặc có quyết định `TBD`.
- [ ] Entity và khóa tham chiếu khớp Data Dictionary.
- [ ] Mỗi AC có test dự kiến, chưa nhầm test design với test pass.
- [ ] Link Jira chính thức, PR, demo, test được cập nhật khi có.
- [ ] Được owner + reviewer chéo duyệt, trạng thái chuyển sang APPROVED.

**Chứng cứ liên kết:** [SRS Index](../README.md) · [Requirements Management](../../01-governance/requirements-management.md) · [Traceability](../../04-traceability/requirements-matrix.md) · [Feasibility](../../06-feasibility/feasibility-study.md).

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù thông báo gửi lỗi hoặc gửi lặp)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Organizer approve thành công nhưng server gửi email/in-app timeout; queue retry hai lần — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Notification event_key unique; trạng thái nghiệp vụ DB là source of truth.

### 13.2 Quyết định và quy trình xử lý

Ghi sự kiện sau khi DB commit bằng outbox/event_id; retry idempotent; thông báo không đảo ngược business action; chỉ gửi đúng recipient.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-08; FR-11; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-18-01 | Approve một lần, notification fails → status APPROVED, queue retry; retry chỉ 1 notification logical | Notification event_key unique; trạng thái nghiệp vụ DB là source of truth; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-08; FR-11. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
