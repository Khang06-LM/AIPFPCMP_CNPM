# FR-02 — Quản trị người dùng, danh mục phim và nhật ký hệ thống

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-02 |
| Nhóm nghiệp vụ | Nền tảng |
| Người dùng/actor | Administrator |
| Chủ sở hữu tài liệu | TV1 – Lê Minh Khang |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-8; FPC-10; FPC-11; FPC-12 |
| Nguồn và mức suy diễn | [G] vai trò Administrator; [P] EP-2 |
| Phụ thuộc | FR-01 |
| Entity | User; Role; FilmStock; Camera; Lens; FilmLab; AuditLog |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **quản trị người dùng, danh mục phim và nhật ký hệ thống** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `Administrator` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Quản trị người dùng, danh mục phim và nhật ký hệ thống; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không xây dựng hệ thống IAM doanh nghiệp hay dashboard real-time ở MVP.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Đăng nhập vai trò Admin.

### 3.2 Đầu vào (Inputs)
- lọc user, trạng thái khóa; danh mục film stock/camera/lens/lab; sửa đổi
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **User; Role; FilmStock; Camera; Lens; FilmLab; AuditLog** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Admin mở dashboard; hệ thống xác minh quyền.
2. Admin tra cứu danh sách tài khoản hoặc danh mục dùng chung.
3. Admin thêm/sửa/khóa bản ghi và gửi lý do nếu thuộc hành động cần audit.
4. BE validate dữ liệu và quan hệ tham chiếu trước khi cập nhật.
5. BE lưu thay đổi; tạo AuditLog (ai, hành động, thời điểm, đối tượng).
6. FE hiển thị kết quả và dữ liệu đã cập nhật.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-02-01 | Tên danh mục đã tồn tại | Báo trùng giá trị; giữ bản ghi cũ. | Không tạo bản ghi lặp. |
| EX-FR-02-02 | Khóa user đang đăng nhập | Các lần gọi API sau phải bị từ chối theo chính sách revoke đã chốt. | Không tiếp tục thao tác đặc quyền. |
| EX-FR-02-03 | Xóa film stock đang được dùng | Chặn xóa cứng; đề nghị ngừng sử dụng. | Không mất lịch sử metadata. |
| EX-FR-02-04 | Admin thiếu quyền thao tác | Trả 403 ở API. | Không cập nhật. |
| EX-FR-02-05 | Lỗi cơ sở dữ liệu khi ghi audit | Transaction rollback hoặc cơ chế audit bền vững; ghi lỗi vận hành. | Không báo thành công giả. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-05: Không xóa cứng danh mục đã được tham chiếu trong submission, chỉ vô hiệu hóa theo phương án.
2. **[D – đề xuất, cần duyệt]** BR-06: Admin không tự ý xem file minh chứng riêng tư nếu không có quyền nghiệp vụ.
3. **[D – đề xuất, cần duyệt]** BR-07: Thao tác thay đổi quyền/khóa user phải được ghi log.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `User; Role; FilmStock; Camera; Lens; FilmLab; AuditLog`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-01](../functional/FR-01-authentication-rbac.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `GET/PATCH /admin/users; CRUD /admin/master-data; GET /admin/audit (đề xuất)`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-02-01 | Chỉ Admin xem và sửa danh sách người dùng. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-02-02 | Danh mục mới hiển thị trong form metadata sau khi lưu. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-02-03 | Danh mục đã tham chiếu không bị xóa cứng làm sai bài thi cũ. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-02-04 | Mỗi hành động nhạy cảm có AuditLog với actor/time/object. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-02-05 | Sai vai trò nhận 403. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-02-06 | Khi tên danh mục đã tồn tại, hệ thống phải: Báo trùng giá trị; giữ bản ghi cũ. Kết quả: Không tạo bản ghi lặp. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-02-07 | Khi khóa user đang đăng nhập, hệ thống phải: Các lần gọi API sau phải bị từ chối theo chính sách revoke đã chốt. Kết quả: Không tiếp tục thao tác đặc quyền. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-02-08 | Khi xóa film stock đang được dùng, hệ thống phải: Chặn xóa cứng; đề nghị ngừng sử dụng. Kết quả: Không mất lịch sử metadata. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-02-09 | Khi admin thiếu quyền thao tác, hệ thống phải: Trả 403 ở API. Kết quả: Không cập nhật. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-02-10 | Khi lỗi cơ sở dữ liệu khi ghi audit, hệ thống phải: Transaction rollback hoặc cơ chế audit bền vững; ghi lỗi vận hành. Kết quả: Không báo thành công giả. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-02-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-02-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-02-02 | Unhappy / alternate | Tên danh mục đã tồn tại | AC-FR-02-06 | Không tạo bản ghi lặp. |
| TC-FR-02-03 | Unhappy / alternate | Khóa user đang đăng nhập | AC-FR-02-07 | Không tiếp tục thao tác đặc quyền. |
| TC-FR-02-04 | Unhappy / alternate | Xóa film stock đang được dùng | AC-FR-02-08 | Không mất lịch sử metadata. |
| TC-FR-02-05 | Unhappy / alternate | Admin thiếu quyền thao tác | AC-FR-02-09 | Không cập nhật. |
| TC-FR-02-06 | Unhappy / alternate | Lỗi cơ sở dữ liệu khi ghi audit | AC-FR-02-10 | Không báo thành công giả. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi cao với CRUD và FK; dashboard giám sát nâng cao xếp ưu tiên sau.
- **Giới hạn giải pháp:** Không xây dựng hệ thống IAM doanh nghiệp hay dashboard real-time ở MVP.
- **Ước lượng thử để lập kế hoạch:** 18 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù danh mục bị ngừng sử dụng nhưng đã có bài nộp)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Admin muốn xóa FilmStock Kodak X đã được 30 FilmRoll tham chiếu — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Không có FK mồ côi; thay đổi quyền/danh mục có AuditLog.

### 13.2 Quyết định và quy trình xử lý

Dùng `active=false`; không xóa cứng nếu có bản ghi tham chiếu; form nhập mới không chọn giá trị inactive, hồ sơ cũ vẫn hiển thị giá trị lịch sử.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-05; NFR-06; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-02-01 | Tạo FilmRoll dùng stock X → deactivate X → bản cũ đọc được, bản mới không thể chọn X | Không có FK mồ côi; thay đổi quyền/danh mục có AuditLog; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-05; NFR-06. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
