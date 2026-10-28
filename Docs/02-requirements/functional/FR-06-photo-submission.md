# FR-06 — Tải ảnh scan và gửi bài dự thi

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-06 |
| Nhóm nghiệp vụ | Core Flow 2 |
| Người dùng/actor | Participant |
| Chủ sở hữu tài liệu | TV3 – Hoàng Duy Khánh; TV2 hỗ trợ storage |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-23; FPC-4 |
| Nguồn và mức suy diễn | [G] Core Flow 2 nộp ảnh + metadata; [P] EP-5 |
| Phụ thuộc | FR-01; FR-03; FR-04; FR-05 |
| Entity | Submission; MediaAsset; FilmFrame; Enrollment; Contest |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **tải ảnh scan và gửi bài dự thi** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `Participant` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Tải ảnh scan và gửi bài dự thi; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không bảo đảm phát hiện mọi ảnh scan giả; dung lượng/format tối đa cần chốt theo nguồn lực; không hỗ trợ upload offline trong MVP.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Contest đang nhận bài; Enrollment hợp lệ; Frame thuộc chủ sở hữu; storage khả dụng.

### 3.2 Đầu vào (Inputs)
- contest_id, category_id, frame_id, title, description, image file, optional negative/contact sheet
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **Submission; MediaAsset; FilmFrame; Enrollment; Contest** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Participant mở cuộc thi đang nhận bài, chọn hạng mục.
2. Chọn Frame thuộc FilmRoll của mình; điền tên tác phẩm và metadata bổ sung.
3. FE kiểm tra kích thước/định dạng sơ bộ; BE cấp quyền upload hoặc nhận file an toàn.
4. Client gửi ảnh; BE/storage xác nhận file thực tồn tại, checksum/metadata hợp lệ.
5. Participant bấm Gửi bài; BE kiểm tra lại deadline, enrollment, frame, loại tệp và hạn mức ngay tại thời điểm chốt.
6. BE tạo Submission liên kết MediaAsset và Frame; chuyển trạng thái SUBMITTED/PENDING_VALIDATION; trả mã bài nộp.
7. Participant xem mã bài và trạng thái trên trang theo dõi; tác vụ kiểm tra tự động được xếp hàng.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-06-01 | Upload thành công nhưng chốt nộp trễ | BE từ chối finalize, giữ file ở vùng tạm để dọn; báo hết hạn. | Không có Submission được duyệt. |
| EX-FR-06-02 | File sai MIME/không giải mã được | BE kiểm tra nội dung thực tế; từ chối, dọn file rác. | Không lưu ảnh độc hại. |
| EX-FR-06-03 | File vượt dung lượng | FE báo sớm; BE/storage cũng chặn. | Không tạo Submission. |
| EX-FR-06-04 | Network ngắt giữa upload | Cho retry an toàn hoặc tiếp tục lại; ảnh tạm không được tính là bài đã nộp. | Không tạo bài nộp trùng. |
| EX-FR-06-05 | Frame của người khác / enrollment không hợp lệ | Trả 403/422 theo trường hợp. | Không tạo liên kết sai quyền. |
| EX-FR-06-06 | Participant bấm Gửi 2 lần | Idempotency key hoặc khóa theo thao tác để tránh duplicate do retry. | Một lần chốt hợp lệ chỉ một bài. |
| EX-FR-06-07 | Storage thành công nhưng database lỗi | Compensating cleanup và log để dọn orphan object. | Không báo hoàn tất giả. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-19: Một Submission tham chiếu đúng một ảnh scan chính và đúng Frame sở hữu hợp lệ (MVP).
2. **[D – đề xuất, cần duyệt]** BR-20: Check hạn nộp trên thời gian máy chủ ở bước finalize; không chỉ kiểm tra JS.
3. **[D – đề xuất, cần duyệt]** BR-21: Chỉ chấp nhận định dạng/cỡ tối đa theo ContestRule hoặc cấu hình đã chốt; số mẫu 20MB chỉ là ví dụ, chưa có hiệu lực.
4. **[D – đề xuất, cần duyệt]** BR-22: Không lộ URL truy cập file riêng tư công khai; cho phép truy cập qua signed URL có hạn.
5. **[D – đề xuất, cần duyệt]** BR-23: Không chuyển sang SUBMITTED khi upload chưa hoàn tất.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `Submission; MediaAsset; FilmFrame; Enrollment; Contest`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-01](../functional/FR-01-authentication-rbac.md); [FR-03](../functional/FR-03-contest-configuration.md); [FR-04](../functional/FR-04-enrollment.md); [FR-05](../functional/FR-05-film-roll-frame-metadata.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `POST /uploads/init; POST /uploads/complete; POST /submissions; GET /me/submissions/{id} (chỉ đề xuất)`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-06-01 | Với contest mở và tài khoản hợp lệ, bài nộp có ID, Frame, MediaAsset và trạng thái sau gửi. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-02 | Hết hạn trên server phải bị từ chối dù browser còn mở form. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-03 | MIME giả hoặc vượt cỡ bị chặn theo cấu hình. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-04 | Upload gián đoạn không tạo bản ghi hoàn thành. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-05 | Bấm Gửi lặp không tạo Submission không mong muốn. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-06 | Người khác không thể xem file minh chứng riêng tư. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-07 | Thông báo lỗi chỉ rõ bước cần xử lý, không lộ chi tiết hệ thống. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-06-08 | Khi upload thành công nhưng chốt nộp trễ, hệ thống phải: BE từ chối finalize, giữ file ở vùng tạm để dọn; báo hết hạn. Kết quả: Không có Submission được duyệt. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-06-09 | Khi file sai mime/không giải mã được, hệ thống phải: BE kiểm tra nội dung thực tế; từ chối, dọn file rác. Kết quả: Không lưu ảnh độc hại. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-06-10 | Khi file vượt dung lượng, hệ thống phải: FE báo sớm; BE/storage cũng chặn. Kết quả: Không tạo Submission. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-06-11 | Khi network ngắt giữa upload, hệ thống phải: Cho retry an toàn hoặc tiếp tục lại; ảnh tạm không được tính là bài đã nộp. Kết quả: Không tạo bài nộp trùng. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-06-12 | Khi frame của người khác / enrollment không hợp lệ, hệ thống phải: Trả 403/422 theo trường hợp. Kết quả: Không tạo liên kết sai quyền. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-06-13 | Khi participant bấm gửi 2 lần, hệ thống phải: Idempotency key hoặc khóa theo thao tác để tránh duplicate do retry. Kết quả: Một lần chốt hợp lệ chỉ một bài. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-06-14 | Khi storage thành công nhưng database lỗi, hệ thống phải: Compensating cleanup và log để dọn orphan object. Kết quả: Không báo hoàn tất giả. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-06-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-06-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-06-02 | Unhappy / alternate | Upload thành công nhưng chốt nộp trễ | AC-FR-06-08 | Không có Submission được duyệt. |
| TC-FR-06-03 | Unhappy / alternate | File sai MIME/không giải mã được | AC-FR-06-09 | Không lưu ảnh độc hại. |
| TC-FR-06-04 | Unhappy / alternate | File vượt dung lượng | AC-FR-06-10 | Không tạo Submission. |
| TC-FR-06-05 | Unhappy / alternate | Network ngắt giữa upload | AC-FR-06-11 | Không tạo bài nộp trùng. |
| TC-FR-06-06 | Unhappy / alternate | Frame của người khác / enrollment không hợp lệ | AC-FR-06-12 | Không tạo liên kết sai quyền. |
| TC-FR-06-07 | Unhappy / alternate | Participant bấm Gửi 2 lần | AC-FR-06-13 | Một lần chốt hợp lệ chỉ một bài. |
| TC-FR-06-08 | Unhappy / alternate | Storage thành công nhưng database lỗi | AC-FR-06-14 | Không báo hoàn tất giả. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi trung bình-cao sau PoC signed URL và kiểm soát lifecycle; cần thử với ảnh JPEG/TIFF và quota storage.
- **Giới hạn giải pháp:** Không bảo đảm phát hiện mọi ảnh scan giả; dung lượng/format tối đa cần chốt theo nguồn lực; không hỗ trợ upload offline trong MVP.
- **Ước lượng thử để lập kế hoạch:** 28 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù hạn chốt nộp trong lúc upload; mất mạng và retry)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Upload lúc 23:58, upload xong 23:59:30 nhưng finalize 00:01; hoặc đã commit DB nhưng response bị mất — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Không có submission SUBMITTED thiếu MediaAsset READY; một request key chỉ một kết quả cuối.

### 13.2 Quyết định và quy trình xử lý

Quyết định tính hạn theo thời điểm server commit finalize; file tạm không phải submission; dùng client_request_id idempotent để retry và GET đối soát trạng thái.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-07; FR-04; FR-05; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-06-01 | Thiết lập close_at=00:00; finalize 00:01 → từ chối; gọi 2 lần key giống → cùng submission_id | Không có submission SUBMITTED thiếu MediaAsset READY; một request key chỉ một kết quả cuối; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-07; FR-04; FR-05. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
