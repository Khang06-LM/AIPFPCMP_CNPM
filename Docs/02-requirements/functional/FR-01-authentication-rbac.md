# FR-01 — Xác thực, phiên đăng nhập và phân quyền theo vai trò

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-01 |
| Nhóm nghiệp vụ | Nền tảng |
| Người dùng/actor | Khách, Participant, Organizer, Judge, Administrator |
| Chủ sở hữu tài liệu | TV2 – Bùi Đức Hưng |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-3; FPC-18; FPC-9 |
| Nguồn và mức suy diễn | [G] phân quyền JWT/RBAC và 4 vai trò; [P] EP-1/EP-2 |
| Phụ thuộc | không |
| Entity | User; Role; UserRole; Session/AuditLog |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **xác thực, phiên đăng nhập và phân quyền theo vai trò** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `Khách, Participant, Organizer, Judge, Administrator` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Xác thực, phiên đăng nhập và phân quyền theo vai trò; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không cam kết chống mọi loại tấn công; cơ chế refresh, reset mật khẩu và đăng nhập đa vai trò phải chốt.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Hệ thống có tài khoản mẫu hoặc màn hình đăng ký; vai trò khởi tạo bởi quản trị viên theo chính sách.

### 3.2 Đầu vào (Inputs)
- email, mật khẩu; token truy cập; danh tính/role đã xác thực
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **User; Role; UserRole; Session/AuditLog** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Người dùng mở trang đăng nhập; nhập email/mật khẩu.
2. FE kiểm tra trường trống và định dạng email, gửi yêu cầu đăng nhập qua HTTPS.
3. BE tìm tài khoản theo email, đối chiếu password hash, kiểm tra trạng thái tài khoản.
4. BE phát token và xác định tập vai trò hợp lệ; FE lưu phiên theo thiết kế bảo mật đã chốt.
5. FE định tuyến tới portal phù hợp; API kiểm tra vai trò tại mỗi request.
6. Hệ thống ghi sự kiện đăng nhập, tránh ghi mật khẩu/token thô.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-01-01 | Email không tồn tại/sai mật khẩu | Trả thông báo chung “Thông tin đăng nhập không hợp lệ”; không tiết lộ email có tồn tại. | Không tạo phiên. |
| EX-FR-01-02 | Tài khoản bị khóa | Trả lỗi không được phép đăng nhập; log lý do theo chính sách. | Không phát token. |
| EX-FR-01-03 | Token hết hạn | API trả 401; FE yêu cầu đăng nhập lại hoặc thực hiện refresh nếu có. | Không ghi dữ liệu. |
| EX-FR-01-04 | Participant gọi API Admin | API trả 403; FE không hiển thị chức năng bị cấm. | Không đổi dữ liệu. |
| EX-FR-01-05 | User có nhiều vai trò | Chính sách chọn/đổi vai trò phải được nhóm xác nhận; không tự cấp thêm quyền. | Quyền đúng tập role được duyệt. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-01: Chỉ người dùng đã xác thực mới sử dụng tài nguyên cần đăng nhập.
2. **[D – đề xuất, cần duyệt]** BR-02: Vai trò được kiểm tra tại API, không chỉ ẩn nút ở FE.
3. **[D – đề xuất, cần duyệt]** BR-03: Mọi tác vụ tác động dữ liệu phải kiểm tra quyền sở hữu hoặc phân công.
4. **[D – đề xuất, cần duyệt]** BR-04: Mật khẩu lưu dưới dạng hash có salt; không lưu plain text.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `User; Role; UserRole; Session/AuditLog`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** Không có requirement trước bắt buộc..
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `POST /auth/login; GET /auth/me; POST /auth/logout (đề xuất; hợp đồng API chưa duyệt)`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-01-01 | Đăng nhập đúng trả trạng thái thành công và có thể gọi API thuộc vai trò. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-01-02 | Sai mật khẩu không tạo token và trả lỗi thống nhất. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-01-03 | API Admin trả 403 với token Participant. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-01-04 | API đã bảo vệ trả 401 nếu thiếu/hết hạn token. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-01-05 | Mật khẩu/token không xuất hiện trong response log hoặc repository. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-01-06 | Khi email không tồn tại/sai mật khẩu, hệ thống phải: Trả thông báo chung “Thông tin đăng nhập không hợp lệ”; không tiết lộ email có tồn tại. Kết quả: Không tạo phiên. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-01-07 | Khi tài khoản bị khóa, hệ thống phải: Trả lỗi không được phép đăng nhập; log lý do theo chính sách. Kết quả: Không phát token. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-01-08 | Khi token hết hạn, hệ thống phải: API trả 401; FE yêu cầu đăng nhập lại hoặc thực hiện refresh nếu có. Kết quả: Không ghi dữ liệu. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-01-09 | Khi participant gọi api admin, hệ thống phải: API trả 403; FE không hiển thị chức năng bị cấm. Kết quả: Không đổi dữ liệu. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-01-10 | Khi user có nhiều vai trò, hệ thống phải: Chính sách chọn/đổi vai trò phải được nhóm xác nhận; không tự cấp thêm quyền. Kết quả: Quyền đúng tập role được duyệt. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-01-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-01-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-01-02 | Unhappy / alternate | Email không tồn tại/sai mật khẩu | AC-FR-01-06 | Không tạo phiên. |
| TC-FR-01-03 | Unhappy / alternate | Tài khoản bị khóa | AC-FR-01-07 | Không phát token. |
| TC-FR-01-04 | Unhappy / alternate | Token hết hạn | AC-FR-01-08 | Không ghi dữ liệu. |
| TC-FR-01-05 | Unhappy / alternate | Participant gọi API Admin | AC-FR-01-09 | Không đổi dữ liệu. |
| TC-FR-01-06 | Unhappy / alternate | User có nhiều vai trò | AC-FR-01-10 | Quyền đúng tập role được duyệt. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi cao nếu dùng thư viện auth chuẩn; PoC đăng nhập 4 role và thử API trái quyền ngay đầu kỳ.
- **Giới hạn giải pháp:** Không cam kết chống mọi loại tấn công; cơ chế refresh, reset mật khẩu và đăng nhập đa vai trò phải chốt.
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù tài khoản bị khóa trong lúc jwt còn hạn)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Phiên từ thiết bị A được cấp token, Admin khóa user ở thiết bị B, thiết bị A gửi yêu cầu quản trị — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** User.status=LOCKED không được tạo mutation; AuditLog lưu quyết định khóa.

### 13.2 Quyết định và quy trình xử lý

Backend kiểm tra trạng thái user và vai trò hiện hành tại thao tác nhạy cảm; từ chối dù token còn hạn; token không đại diện quyền vĩnh viễn.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-02; NFR-01; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-01-01 | Đăng nhập A → khóa từ Admin → POST thay đổi dữ liệu với token cũ → bị 403/401 và DB không đổi | User.status=LOCKED không được tạo mutation; AuditLog lưu quyết định khóa; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-02; NFR-01. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
