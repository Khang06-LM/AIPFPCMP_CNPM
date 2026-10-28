# FR-15 — Gắn nhãn, phân loại và tìm kiếm nội dung ảnh

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-15 |
| Nhóm nghiệp vụ | Core Flow 5 / AI |
| Người dùng/actor | AI Service; Participant; Organizer |
| Chủ sở hữu tài liệu | TV5 – Đào Thị Thanh Bình |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-40; FPC-36 |
| Nguồn và mức suy diễn | [G] auto-tagging/categorization; [P] EP-9/EP-8 |
| Phụ thuộc | FR-06; FR-12 |
| Entity | Tag; MediaAsset; AIAnalysis; ArchiveEntry |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **gắn nhãn, phân loại và tìm kiếm nội dung ảnh** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `AI Service; Participant; Organizer` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Gắn nhãn, phân loại và tìm kiếm nội dung ảnh; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Tag có thể sai và mang thiên lệch; không hứa tag chính xác mọi ảnh/phong cách nghệ thuật.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Ảnh có thể đọc, tính năng tagging có mô hình/API, đã xác nhận ngôn ngữ.

### 3.2 Đầu vào (Inputs)
- image_id, model, taxonomy, candidate_tags
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **Tag; MediaAsset; AIAnalysis; ArchiveEntry** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. BE gửi job sau khi ảnh sẵn sàng.
2. AI service sinh tập tag mô tả nội dung nhìn thấy (ví dụ: phong cảnh, chân dung).
3. Chuẩn hóa tag theo taxonomy, loại trùng và nội dung không phù hợp.
4. Lưu AutoTags cùng nguồn/version, tách biệt ManualTags.
5. Organizer có quyền sửa/bỏ tag khi được cấu hình.
6. Archive dùng tag được duyệt để tìm kiếm.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-15-01 | AI tạo tag không liên quan | Organizer sửa/xóa, có ghi nhận. | Không khóa người dùng theo AI. |
| EX-FR-15-02 | Tag trùng ngôn ngữ khác | Normalize theo từ điển/locale; không mất tag gốc để audit. | Tìm kiếm ổn định. |
| EX-FR-15-03 | Không có tag | Archive vẫn tìm được theo metadata thủ công. | Không chặn xuất bản. |
| EX-FR-15-04 | API trả nội dung không an toàn | Lọc và đánh dấu review. | Không hiển thị trực tiếp. |
| EX-FR-15-05 | Job thất bại | Ghi error, retry có giới hạn, không xóa ảnh. | Luồng nộp bài bình thường. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-51: Tag tự động chỉ mô tả dự đoán, không sửa metadata film stock do người dùng khai báo.
2. **[D – đề xuất, cần duyệt]** BR-52: Không kết luận địa điểm/danh tính người trong ảnh nếu thiếu dữ liệu được phép.
3. **[D – đề xuất, cần duyệt]** BR-53: Manual tag có ưu tiên hiển thị theo chính sách.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `Tag; MediaAsset; AIAnalysis; ArchiveEntry`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-06](../functional/FR-06-photo-submission.md); [FR-12](../functional/FR-12-digital-archive.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `POST /internal/ai/jobs/tag; PATCH /media/{id}/tags; GET /archive?tag=`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-15-01 | Ảnh mẫu có danh sách tag và trạng thái review. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-15-02 | AutoTag tách khỏi FilmStock/ISO do người dùng khai báo. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-15-03 | Tag có thể được sửa/xóa mà không mất ảnh. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-15-04 | Không có AI tag vẫn tìm theo hạng mục/film stock. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-15-05 | Khi ai tạo tag không liên quan, hệ thống phải: Organizer sửa/xóa, có ghi nhận. Kết quả: Không khóa người dùng theo AI. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-15-06 | Khi tag trùng ngôn ngữ khác, hệ thống phải: Normalize theo từ điển/locale; không mất tag gốc để audit. Kết quả: Tìm kiếm ổn định. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-15-07 | Khi không có tag, hệ thống phải: Archive vẫn tìm được theo metadata thủ công. Kết quả: Không chặn xuất bản. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-15-08 | Khi api trả nội dung không an toàn, hệ thống phải: Lọc và đánh dấu review. Kết quả: Không hiển thị trực tiếp. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-15-09 | Khi job thất bại, hệ thống phải: Ghi error, retry có giới hạn, không xóa ảnh. Kết quả: Luồng nộp bài bình thường. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-15-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-15-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-15-02 | Unhappy / alternate | AI tạo tag không liên quan | AC-FR-15-05 | Không khóa người dùng theo AI. |
| TC-FR-15-03 | Unhappy / alternate | Tag trùng ngôn ngữ khác | AC-FR-15-06 | Tìm kiếm ổn định. |
| TC-FR-15-04 | Unhappy / alternate | Không có tag | AC-FR-15-07 | Không chặn xuất bản. |
| TC-FR-15-05 | Unhappy / alternate | API trả nội dung không an toàn | AC-FR-15-08 | Không hiển thị trực tiếp. |
| TC-FR-15-06 | Unhappy / alternate | Job thất bại | AC-FR-15-09 | Luồng nộp bài bình thường. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi trung bình khi dùng model có sẵn; tạo taxonomy nhỏ trước.
- **Giới hạn giải pháp:** Tag có thể sai và mang thiên lệch; không hứa tag chính xác mọi ảnh/phong cách nghệ thuật.
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù ai gắn nhãn sai hoặc nội dung nhạy cảm)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Ảnh có bóng người nhưng AI gắn tag portrait không chính xác; tag tự động hiển thị công khai — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Manual tag provenance tách AUTO; không tự tạo khẳng định không kiểm chứng.

### 13.2 Quyết định và quy trình xử lý

Tag AUTO là đề xuất; chỉnh/ẩn tag sai; không suy luận danh tính hay địa điểm riêng tư từ ảnh; không sửa metadata phim.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-12; FR-17; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-15-01 | Auto tag sai → user/organizer sửa → tag hiển thị thay đổi có nguồn gốc | Manual tag provenance tách AUTO; không tự tạo khẳng định không kiểm chứng; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-12; FR-17. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
