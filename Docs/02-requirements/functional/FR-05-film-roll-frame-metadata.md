# FR-05 — Quản lý cuộn phim, khung hình và siêu dữ liệu analog

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-05 |
| Nhóm nghiệp vụ | Core Flow 2 |
| Người dùng/actor | Participant |
| Chủ sở hữu tài liệu | TV3 – Hoàng Duy Khánh |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-21; FPC-22 |
| Nguồn và mức suy diễn | [G] metadata roll/frame/scan; [P] EP-5 |
| Phụ thuộc | FR-01; FR-02 |
| Entity | FilmRoll; FilmFrame; FilmStock; Camera; Lens; FilmLab |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **quản lý cuộn phim, khung hình và siêu dữ liệu analog** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `Participant` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Quản lý cuộn phim, khung hình và siêu dữ liệu analog; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Metadata do người dùng khai báo không tự chứng minh ảnh phim là ảnh nguyên bản.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Đã đăng nhập; danh mục film stock/camera/lab sẵn có hoặc có cơ chế nhập khác.

### 3.2 Đầu vào (Inputs)
- film_stock, ISO, format, camera, lens, lab, frame_number, scan_info, negative/contact sheet (tùy chọn)
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **FilmRoll; FilmFrame; FilmStock; Camera; Lens; FilmLab** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Participant mở Hồ sơ phim và tạo cuộn phim.
2. Chọn film stock, thiết bị, khổ phim, ISO; khai báo lab nếu có.
3. BE kiểm tra kiểu dữ liệu, quyền sở hữu; lưu FilmRoll.
4. Participant thêm Frame thuộc Roll; nhập số khung, ghi chú, thông số scan.
5. Hệ thống bảo đảm cặp roll_id–frame_number hợp lệ theo chính sách.
6. Hiển thị danh sách khung hình để chọn khi tạo Submission.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-05-01 | Thiếu film stock bắt buộc | Báo trường thiếu, không lưu form không hợp lệ. | Không tạo Roll. |
| EX-FR-05-02 | Frame number trùng trong Roll | Trả lỗi trùng; đưa ra số khung cần sửa. | Không phát sinh Frame thứ hai. |
| EX-FR-05-03 | Participant sửa Roll của người khác | BE trả 403/404. | Không đổi dữ liệu. |
| EX-FR-05-04 | Lab không có trong danh mục | Yêu cầu chọn khác hoặc gửi đề xuất thêm danh mục. | Không tự tạo master data trái quyền. |
| EX-FR-05-05 | Sửa metadata sau khi nộp | Theo chính sách version/khóa; mọi thay đổi phải truy được. | Submission không bị mất nguồn gốc. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-15: Frame thuộc đúng Roll và Roll thuộc Participant đăng nhập.
2. **[D – đề xuất, cần duyệt]** BR-16: Frame number không trùng trong một Roll nếu bài toán yêu cầu duy nhất.
3. **[D – đề xuất, cần duyệt]** BR-17: Thay đổi metadata đã dùng trong bài nộp cần lưu lịch sử hoặc khóa theo trạng thái.
4. **[D – đề xuất, cần duyệt]** BR-18: Negative/contact sheet là minh chứng tùy chọn theo thể lệ cuộc thi.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `FilmRoll; FilmFrame; FilmStock; Camera; Lens; FilmLab`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-01](../functional/FR-01-authentication-rbac.md); [FR-02](../functional/FR-02-admin-masterdata-audit.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `POST /film-rolls; POST /film-rolls/{id}/frames; GET /me/film-rolls; PATCH /film-frames/{id}`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-05-01 | Tạo được Roll có thông số film, camera và ISO. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-05-02 | Tạo được nhiều Frame khác số khung trong cùng Roll. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-05-03 | Không tạo được Frame trùng số trong cùng Roll. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-05-04 | Chỉ chủ sở hữu xem/sửa thông tin riêng tư. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-05-05 | Submission tham chiếu được Frame đã khai báo. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-05-06 | Khi thiếu film stock bắt buộc, hệ thống phải: Báo trường thiếu, không lưu form không hợp lệ. Kết quả: Không tạo Roll. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-05-07 | Khi frame number trùng trong roll, hệ thống phải: Trả lỗi trùng; đưa ra số khung cần sửa. Kết quả: Không phát sinh Frame thứ hai. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-05-08 | Khi participant sửa roll của người khác, hệ thống phải: BE trả 403/404. Kết quả: Không đổi dữ liệu. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-05-09 | Khi lab không có trong danh mục, hệ thống phải: Yêu cầu chọn khác hoặc gửi đề xuất thêm danh mục. Kết quả: Không tự tạo master data trái quyền. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-05-10 | Khi sửa metadata sau khi nộp, hệ thống phải: Theo chính sách version/khóa; mọi thay đổi phải truy được. Kết quả: Submission không bị mất nguồn gốc. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-05-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-05-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-05-02 | Unhappy / alternate | Thiếu film stock bắt buộc | AC-FR-05-06 | Không tạo Roll. |
| TC-FR-05-03 | Unhappy / alternate | Frame number trùng trong Roll | AC-FR-05-07 | Không phát sinh Frame thứ hai. |
| TC-FR-05-04 | Unhappy / alternate | Participant sửa Roll của người khác | AC-FR-05-08 | Không đổi dữ liệu. |
| TC-FR-05-05 | Unhappy / alternate | Lab không có trong danh mục | AC-FR-05-09 | Không tự tạo master data trái quyền. |
| TC-FR-05-06 | Unhappy / alternate | Sửa metadata sau khi nộp | AC-FR-05-10 | Submission không bị mất nguồn gốc. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi cao với FK; khó khăn là thống nhất trường nào bắt buộc theo quy chế cuộc thi.
- **Giới hạn giải pháp:** Metadata do người dùng khai báo không tự chứng minh ảnh phim là ảnh nguyên bản.
- **Ước lượng thử để lập kế hoạch:** 20 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù sửa metadata sau khi bài đã được duyệt)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Participant sửa film_stock hoặc frame number sau khi Organizer approved — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Submission.metadata_snapshot không thay đổi im lặng; owner roll == owner submission.

### 13.2 Quyết định và quy trình xử lý

Tạo snapshot metadata lúc gửi bài; không cho sửa bản snapshot sau APPROVED; yêu cầu hiệu đính có lịch sử và xét duyệt lại theo chính sách.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-06; FR-08; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-05-01 | Nộp, duyệt, sửa cuộn → snapshot submission giữ nguyên / yêu cầu sửa được kiểm soát | Submission.metadata_snapshot không thay đổi im lặng; owner roll == owner submission; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-06; FR-08. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
