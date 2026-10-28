# FR-17 — Hồ sơ cá nhân, portfolio, lịch sử và theo dõi trạng thái

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-17 |
| Nhóm nghiệp vụ | Core Flow 2 / Core Flow 5 |
| Người dùng/actor | Participant |
| Chủ sở hữu tài liệu | TV3 – Hoàng Duy Khánh |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-18; FPC-20; FPC-24 |
| Nguồn và mức suy diễn | [G] Participant có portfolio, trạng thái, kết quả và feedback; [P] EP-4/EP-5 |
| Phụ thuộc | FR-01; FR-04; FR-05; FR-06; FR-08; FR-11 |
| Entity | User; Submission; Enrollment; ArchiveEntry; FilmFrame |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **hồ sơ cá nhân, portfolio, lịch sử và theo dõi trạng thái** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `Participant` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Hồ sơ cá nhân, portfolio, lịch sử và theo dõi trạng thái; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không xây dựng mạng xã hội, bình luận hay theo dõi nhiếp ảnh gia ở MVP.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Participant đăng nhập; đã có hoặc chưa có cuộc thi tham gia.

### 3.2 Đầu vào (Inputs)
- profile fields, pagination, status filter
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **User; Submission; Enrollment; ArchiveEntry; FilmFrame** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Participant mở hồ sơ và xem thông tin cá nhân.
2. Hệ thống hiển thị cuộc thi đã đăng ký theo trạng thái.
3. Participant chọn mục Bài đã nộp; xem Submission ID, ảnh thu nhỏ và trạng thái kiểm duyệt.
4. Nếu cuộc thi đã công bố, hiển thị kết quả/feedback được phép xem.
5. Participant chọn portfolio gồm ảnh đủ điều kiện công khai theo cấu hình.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-17-01 | Chưa tham gia cuộc thi | Hiển thị empty state kèm CTA xem cuộc thi. | Không lỗi 500. |
| EX-FR-17-02 | Submission bị từ chối | Hiển thị trạng thái và lý do được phép xem. | Không hiển thị điểm chưa chấm. |
| EX-FR-17-03 | Đã có kết quả nhưng chưa publish | Ẩn xếp hạng/feedback bí mật. | Không rò kết quả. |
| EX-FR-17-04 | Truy cập /users/{id} của người khác | Từ chối dữ liệu riêng tư. | Không lộ PII. |
| EX-FR-17-05 | Ảnh archive bị gỡ quyền công khai | Portfolio ẩn file public, giữ lịch sử nội bộ. | Tôn trọng consent. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-57: Chỉ hiển thị lịch sử của người đăng nhập (trừ vai trò được cấp quyền).
2. **[D – đề xuất, cần duyệt]** BR-58: Phản hồi giám khảo có thể bị giữ kín trước công bố theo chính sách.
3. **[D – đề xuất, cần duyệt]** BR-59: Portfolio công khai phải có sự đồng ý/phạm vi xuất bản.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `User; Submission; Enrollment; ArchiveEntry; FilmFrame`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-01](../functional/FR-01-authentication-rbac.md); [FR-04](../functional/FR-04-enrollment.md); [FR-05](../functional/FR-05-film-roll-frame-metadata.md); [FR-06](../functional/FR-06-photo-submission.md); [FR-08](../functional/FR-08-manual-review.md); [FR-11](../functional/FR-11-ranking-result-publication.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `GET /me; GET /me/submissions; GET /me/portfolio; PATCH /me`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-17-01 | Participant xem được profile, enrollments và submissions của mình. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-17-02 | Bài bị từ chối thể hiện trạng thái đúng. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-17-03 | Kết quả chưa công bố không lộ. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-17-04 | Người dùng chưa có dữ liệu thấy trạng thái rỗng. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-17-05 | Khi chưa tham gia cuộc thi, hệ thống phải: Hiển thị empty state kèm CTA xem cuộc thi. Kết quả: Không lỗi 500. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-17-06 | Khi submission bị từ chối, hệ thống phải: Hiển thị trạng thái và lý do được phép xem. Kết quả: Không hiển thị điểm chưa chấm. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-17-07 | Khi đã có kết quả nhưng chưa publish, hệ thống phải: Ẩn xếp hạng/feedback bí mật. Kết quả: Không rò kết quả. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-17-08 | Khi truy cập /users/{id} của người khác, hệ thống phải: Từ chối dữ liệu riêng tư. Kết quả: Không lộ PII. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-17-09 | Khi ảnh archive bị gỡ quyền công khai, hệ thống phải: Portfolio ẩn file public, giữ lịch sử nội bộ. Kết quả: Tôn trọng consent. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-17-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-17-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-17-02 | Unhappy / alternate | Chưa tham gia cuộc thi | AC-FR-17-05 | Không lỗi 500. |
| TC-FR-17-03 | Unhappy / alternate | Submission bị từ chối | AC-FR-17-06 | Không hiển thị điểm chưa chấm. |
| TC-FR-17-04 | Unhappy / alternate | Đã có kết quả nhưng chưa publish | AC-FR-17-07 | Không rò kết quả. |
| TC-FR-17-05 | Unhappy / alternate | Truy cập /users/{id} của người khác | AC-FR-17-08 | Không lộ PII. |
| TC-FR-17-06 | Unhappy / alternate | Ảnh archive bị gỡ quyền công khai | AC-FR-17-09 | Tôn trọng consent. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi cao, ưu tiên lịch sử/trạng thái trước portfolio nâng cao.
- **Giới hạn giải pháp:** Không xây dựng mạng xã hội, bình luận hay theo dõi nhiếp ảnh gia ở MVP.
- **Ước lượng thử để lập kế hoạch:** 14 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù lịch sử thi đấu hiển thị điểm trước công bố)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Participant mở portfolio trong lúc Judge đang chấm, API có score draft — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Không để draft score lộ qua API, export hay cache.

### 13.2 Quyết định và quy trình xử lý

Ẩn điểm/nhận xét chưa được phép công bố ở cả FE và BE; portfolio public cần consent; trạng thái xử lý vẫn có thể xem trong phạm vi chính mình.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-10; FR-11; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-17-01 | Participant GET trước publish → không nhận điểm; sau publish → nhận phạm vi được phép | Không để draft score lộ qua API, export hay cache; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-10; FR-11. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
