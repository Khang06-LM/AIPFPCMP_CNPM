# FR-07 — Kiểm tra điều kiện hợp lệ của bài dự thi

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-07 |
| Nhóm nghiệp vụ | Core Flow 3 |
| Người dùng/actor | System; Organizer |
| Chủ sở hữu tài liệu | TV2 – Bùi Đức Hưng |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-27 |
| Nguồn và mức suy diễn | [G] Core Flow 3 tự kiểm tra deadline/format/metadata; [P] EP-6 |
| Phụ thuộc | FR-06; FR-03; FR-05 |
| Entity | Submission; ValidationReport; MediaAsset; Contest |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **kiểm tra điều kiện hợp lệ của bài dự thi** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `System; Organizer` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Kiểm tra điều kiện hợp lệ của bài dự thi; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không thể xác minh nguồn gốc phim chỉ bằng metadata và định dạng file.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Submission đã được finalize, có file lưu trữ.

### 3.2 Đầu vào (Inputs)
- submission_id, contest rules snapshot, metadata/file properties
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **Submission; ValidationReport; MediaAsset; Contest** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Hệ thống nhận sự kiện Submitted.
2. BE lấy quy chế và dữ liệu Submission tại thời điểm nộp.
3. Kiểm tra hạn nộp, thể loại/hạng mục, định dạng/size/scan, metadata bắt buộc.
4. Ghi ValidationReport gồm trạng thái từng quy tắc, thông điệp, thời gian, phiên bản quy tắc.
5. Nếu đạt điều kiện máy kiểm được, chuyển PENDING_REVIEW; nếu không, chuyển NEEDS_CORRECTION/INVALID theo policy.
6. Đưa Submission tới hàng đợi Organizer; AI chạy tách biệt nếu có cấu hình.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-07-01 | Thiếu trường metadata bắt buộc | Đánh fail rule tương ứng; nếu cho sửa thì trạng thái NEEDS_CORRECTION. | Không gửi chấm. |
| EX-FR-07-02 | File object không tồn tại | Đánh trạng thái ERROR, cần retry hoặc can thiệp; không coi bài không hợp lệ do lỗi hệ thống. | Không tự loại thí sinh. |
| EX-FR-07-03 | Quy tắc contest đổi sau nộp | Dùng rule snapshot tại thời điểm nộp hoặc phiên bản được phê duyệt. | Kết quả tái lập. |
| EX-FR-07-04 | Job chạy lặp | Idempotent theo submission/version; cập nhật trạng thái có kiểm soát. | Không nhân đôi report bất hợp lý. |
| EX-FR-07-05 | AI service timeout | Validation máy vẫn hoàn tất phần không phụ thuộc AI; thêm AI_PENDING. | Không chặn vô hạn. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-24: Quy tắc xác thực máy chỉ kiểm chứng điều kiện định lượng; không kết luận tác phẩm có phải phim thật.
2. **[D – đề xuất, cần duyệt]** BR-25: Báo cáo validation phải truy vết từng rule và nguyên nhân.
3. **[D – đề xuất, cần duyệt]** BR-26: Chạy lại validation không tạo nhiều bản quyết định mâu thuẫn.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `Submission; ValidationReport; MediaAsset; Contest`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-06](../functional/FR-06-photo-submission.md); [FR-03](../functional/FR-03-contest-configuration.md); [FR-05](../functional/FR-05-film-roll-frame-metadata.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `POST /submissions/{id}/validate (internal); GET /submissions/{id}/validation-report`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-07-01 | Một submission hợp lệ tạo ValidationReport từng rule. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-07-02 | Metadata thiếu được chỉ ra cụ thể. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-07-03 | Lỗi service được phân biệt với tác phẩm không hợp lệ. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-07-04 | Validation lại có thể truy lịch sử và phiên bản. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-07-05 | Không auto reject chỉ vì AI gắn cờ. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-07-06 | Khi thiếu trường metadata bắt buộc, hệ thống phải: Đánh fail rule tương ứng; nếu cho sửa thì trạng thái NEEDS_CORRECTION. Kết quả: Không gửi chấm. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-07-07 | Khi file object không tồn tại, hệ thống phải: Đánh trạng thái ERROR, cần retry hoặc can thiệp; không coi bài không hợp lệ do lỗi hệ thống. Kết quả: Không tự loại thí sinh. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-07-08 | Khi quy tắc contest đổi sau nộp, hệ thống phải: Dùng rule snapshot tại thời điểm nộp hoặc phiên bản được phê duyệt. Kết quả: Kết quả tái lập. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-07-09 | Khi job chạy lặp, hệ thống phải: Idempotent theo submission/version; cập nhật trạng thái có kiểm soát. Kết quả: Không nhân đôi report bất hợp lý. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-07-10 | Khi ai service timeout, hệ thống phải: Validation máy vẫn hoàn tất phần không phụ thuộc AI; thêm AI_PENDING. Kết quả: Không chặn vô hạn. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-07-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-07-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-07-02 | Unhappy / alternate | Thiếu trường metadata bắt buộc | AC-FR-07-06 | Không gửi chấm. |
| TC-FR-07-03 | Unhappy / alternate | File object không tồn tại | AC-FR-07-07 | Không tự loại thí sinh. |
| TC-FR-07-04 | Unhappy / alternate | Quy tắc contest đổi sau nộp | AC-FR-07-08 | Kết quả tái lập. |
| TC-FR-07-05 | Unhappy / alternate | Job chạy lặp | AC-FR-07-09 | Không nhân đôi report bất hợp lý. |
| TC-FR-07-06 | Unhappy / alternate | AI service timeout | AC-FR-07-10 | Không chặn vô hạn. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi cao cho rules tĩnh, trung bình khi version hóa quy chế; test snapshot.
- **Giới hạn giải pháp:** Không thể xác minh nguồn gốc phim chỉ bằng metadata và định dạng file.
- **Ước lượng thử để lập kế hoạch:** 16 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù validator chạy hai lần; một bài fail nhưng ai chậm)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Job validation bị queue gửi hai lần; AI detector không phản hồi — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Không một job retry nào tự động sinh quyết định duyệt trái ngược; report cần reason codes.

### 13.2 Quyết định và quy trình xử lý

Quy tắc xác định (format, deadline, metadata) được tính độc lập AI; lưu report theo submission_version+rule_version; AI_PENDING không đổi thành AI_REJECTED.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-08; FR-13; FR-14; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-07-01 | Run validation 2 lần cùng version → không có 2 quyết định hiệu lực; tắt AI → vẫn xử lý rule định lượng | Không một job retry nào tự động sinh quyết định duyệt trái ngược; report cần reason codes; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-08; FR-13; FR-14. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
