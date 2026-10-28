# FR-11 — Tổng hợp điểm, xếp hạng và công bố kết quả

> **Trạng thái:** DRAFT / REVIEW REQUIRED · **Bản:** 1.0-review · **Ngày chuẩn bị:** 28/10/2026  
> **Đây là đặc tả yêu cầu đề xuất, KHÔNG phải chức năng đã triển khai hoặc kết quả kiểm thử đã đạt.**

## 1. Thẻ nhận diện, truy vết và nguồn gốc

| Thuộc tính | Nội dung |
| --- | --- |
| ID | FR-11 |
| Nhóm nghiệp vụ | Core Flow 4 → 5 |
| Người dùng/actor | Organizer; Participant; Public viewer |
| Chủ sở hữu tài liệu | TV2 – Bùi Đức Hưng (tính điểm); TV1 – Lê Minh Khang (công bố) |
| Mức ưu tiên đề xuất | MUST – lõi |
| Jira trong kế hoạch (chưa xác nhận key thực) | FPC-32; FPC-33 |
| Nguồn và mức suy diễn | [G] Core Flow 4/5 xếp hạng, công bố; [P] EP-7/EP-8 |
| Phụ thuộc | FR-10; FR-03; FR-08 |
| Entity | RankingSnapshot; Award; Contest; ScoreSheet; Submission |
| Trạng thái đáp ứng | Chưa có bằng chứng triển khai, test hoặc demo – cần điền link PR / test / staging |

**Quy ước nguồn:** `[G]` = nội dung từ đề tài giảng viên; `[P]` = kế hoạch nhóm; `[D]` = phân tích/thiết kế do nhóm đề xuất, cần xác nhận. Các giới hạn số lượng, thời gian và chính sách nghiệp vụ không ghi trong G/P sẽ là **TBD**, không được trình bày như yêu cầu giảng viên đã chốt.

## 2. Vấn đề, giá trị nghiệp vụ và ranh giới

**Vấn đề cần giải quyết:** Trong quy trình thu bài và tổ chức cuộc thi ảnh phim bằng các công cụ rời rạc, nghiệp vụ **tổng hợp điểm, xếp hạng và công bố kết quả** thiếu dữ liệu và trạng thái tập trung, khó truy vết quyền và kết quả xử lý.

**Kết quả kỳ vọng:** Người dùng thuộc vai trò `Organizer; Participant; Public viewer` thực hiện được hành vi được cấp quyền; hệ thống lưu và trả trạng thái nhất quán, có cơ sở kiểm thử, có dữ liệu phục vụ các bước nghiệp vụ sau.

**Trong phạm vi:** Tổng hợp điểm, xếp hạng và công bố kết quả; xử lý đúng quyền, dữ liệu hợp lệ, trường hợp lỗi và dấu vết kiểm thử.

**Ngoài phạm vi / chỉ làm sau:** Không tự chọn tiêu chí đồng điểm nếu giảng viên chưa thống nhất; không bảo đảm minh bạch tuyệt đối nếu đầu vào thiếu.

## 3. Tiền điều kiện, dữ liệu và hậu điều kiện

### 3.1 Tiền điều kiện (Preconditions)
- Vòng chấm đã đóng; đủ phiếu theo quy tắc; Organizer được phép công bố.

### 3.2 Đầu vào (Inputs)
- contest_id, round_id, award rules, finalization approval
- Tất cả `user_id`, `role`, chủ sở hữu và thời gian thao tác phải được BE đối chiếu từ phiên xác thực / cơ sở dữ liệu, **không tin giá trị giả mạo từ client**.
- Dữ liệu thiếu chính sách bắt buộc hoặc ngưỡng (`TBD`) phải hỏi giảng viên/nhóm trước khi khóa Acceptance Criteria.

### 3.3 Đầu ra (Outputs)
- Thành công: `id` tài nguyên, `status`, `updated_at`, dữ liệu công khai theo quyền; frontend có thông báo hành động thành công.
- Thất bại: mã lỗi ổn định, thông điệp dễ hiểu, HTTP status phù hợp (401/403/404/409/422/5xx tùy tình huống); không rò dữ liệu nhạy cảm.
- Có bản ghi liên kết Entity: **RankingSnapshot; Award; Contest; ScoreSheet; Submission** theo sơ đồ đề xuất tại [Entity & Data Dictionary](../../03-domain/entities.md).

### 3.4 Hậu điều kiện (Postconditions)
- **Thành công:** trạng thái nghiệp vụ được ghi nhận nhất quán; hành động sau có thể sử dụng dữ liệu đúng quyền.
- **Thất bại:** không có tài nguyên “nửa thành công” được coi là hoàn tất; lỗi cần retry/rollback được ghi nhận và phân biệt với bài nộp/điểm số không hợp lệ.

## 4. Luồng nghiệp vụ chính (Main Success Scenario / Happy Case)

1. Hệ thống tổng hợp ScoreSheet FINALIZED thuộc vòng và hạng mục.
2. BE tính tổng hợp đa giám khảo theo công thức đã duyệt, lưu RankingSnapshot có version.
3. Organizer xem bảng xếp hạng nháp, kiểm tra bài thiếu điểm/đồng điểm.
4. Organizer xử lý trường hợp ngoại lệ theo quy tắc và ký duyệt kết quả.
5. Organizer bấm Công bố; BE kiểm tra tính đầy đủ và khóa snapshot.
6. Participant xem kết quả và nhận xét được phép công khai; archive nhận danh sách tác phẩm đạt giải.

**Kết quả quan sát được khi demo:** người phụ trách thao tác theo từng bước; mở dữ liệu kết quả hoặc nhật ký API để chứng minh trạng thái, quan hệ Entity và quyền truy cập đã thay đổi chính xác.

## 5. Các luồng thay thế, ngoại lệ và Unhappy Case

| Mã nhánh | Điều kiện kích hoạt | Xử lý bắt buộc | Kết quả/hậu điều kiện |
| --- | --- | --- | --- |
| EX-FR-11-01 | Thiếu phiếu một giám khảo | Chặn công bố hoặc xử lý exception theo policy đã phê duyệt. | Không tuyên bố kết quả thiếu công bằng. |
| EX-FR-11-02 | Hai ảnh đồng điểm | Áp dụng quy tắc tie-break đã chốt, nếu chưa có thì đánh dấu NEEDS_DECISION. | Không sắp xếp ngẫu nhiên. |
| EX-FR-11-03 | Điểm chưa chốt | Bỏ qua phiếu nháp và báo cảnh báo. | Không cộng điểm nháp. |
| EX-FR-11-04 | Hai Organizer cùng công bố | Version guard tránh công bố lặp. | Một snapshot chính thức. |
| EX-FR-11-05 | Có yêu cầu sửa kết quả sau công bố | Thực hiện phiên bản đính chính kèm lịch sử; không sửa âm thầm. | Có dấu vết kiểm tra. |

**Nguyên tắc kiểm soát lỗi:** Đối với các nhánh trên, nhóm cần có test kiểm chứng cả ở tầng UI và API. FE chỉ giúp người dùng hiểu lỗi; **BE mới là nơi chặn thao tác không hợp lệ**. Nếu có lỗi phụ thuộc bên ngoài (AI/cloud), tuyệt đối không quy kết ngay người dùng gian lận hoặc sai dữ liệu.

## 6. Quy tắc nghiệp vụ và điều kiện còn phải quyết định

1. **[D – đề xuất, cần duyệt]** BR-38: Chỉ phiếu FINALIZED hợp lệ được tính.
2. **[D – đề xuất, cần duyệt]** BR-39: Chính sách trung bình/trọng số/đồng điểm phải có quyết định D-06 trước khi triển khai.
3. **[D – đề xuất, cần duyệt]** BR-40: Không công bố bảng nháp chưa được Organizer phê duyệt.
4. **[D – đề xuất, cần duyệt]** BR-41: Kết quả đã công bố giữ snapshot để tái lập, thay đổi phải có audit.

**Chốt rule:** Mỗi Business Rule (BR) trên phải có người xác nhận. Khi đổi rule, cập nhật file này, [Business Rules](../../03-domain/business-rules.md), Test Case và Jira Story liên quan bằng Pull Request.

## 7. Dữ liệu, quyền và tích hợp với các chức năng khác

- **Entities tham gia:** `RankingSnapshot; Award; Contest; ScoreSheet; Submission`. Xem [Entity Catalogue](../../03-domain/entities.md) và [Data Dictionary](../../03-domain/data-dictionary.md).
- **Dependency trước:** [FR-10](../functional/FR-10-judging-scoring.md); [FR-03](../functional/FR-03-contest-configuration.md); [FR-08](../functional/FR-08-manual-review.md).
- **Dependency sau:** kiểm tra trong [Ma trận truy vết](../../04-traceability/requirements-matrix.md) và [Luồng nghiệp vụ](../../00-overview/end-to-end-flows.md).
- **Quyền:** BE kiểm tra actor role, trạng thái đối tượng, phạm vi contest và owner/assignment trước khi đọc hoặc sửa dữ liệu.
- **Nguyên tắc đồng nhất:** khóa ngoại không được đứt; tránh retry sinh bản ghi trùng; mọi transition quan trọng có thời gian, actor và lịch sử kiểm toán phù hợp.

## 8. Hướng đáp ứng bằng phần mềm — thiết kế đề xuất (không phải đã code)

**API dự kiến:** `POST /rankings/recalculate; POST /contests/{id}/publish-results; GET /contests/{id}/results`.

1. **FE** thể hiện đúng trạng thái tải/trống/thành công/lỗi, disable nút không hợp lệ nhưng không thay thế kiểm tra BE; mapping trường đầu vào vào payload thống nhất.
2. **BE** kiểm tra JWT/RBAC, ràng buộc dữ liệu, state transition, transaction/idempotency khi cần; trả lỗi machine-readable và không ghi secret trong log.
3. **DB / Storage** sử dụng các FK/unique/version và giới hạn truy cập trong [Data Dictionary](../../03-domain/data-dictionary.md). Với ảnh, tách metadata trong DB và binary trong object storage.
4. **Integration** ghi rõ điểm gọi sang module khác, contract request/response và failure fallback; việc nào chưa có endpoint phải ghi `TBD`, không bịa API đã chạy.

**Dấu hiệu đáp ứng thực tế khi hoàn thiện:** link PR merged + commit + CI xanh + bộ test thực chạy + màn hình/demo trên staging + bản ghi bằng chứng. Hiện thời **CHƯA CÓ** các bằng chứng này trong hai tài liệu nguồn.

## 9. Acceptance Criteria (tiêu chí nghiệm thu)

| Mã | Điều kiện đạt (quan sát/kiểm thử được) | Cách chứng minh dự kiến |
| --- | --- | --- |
| AC-FR-11-01 | Bảng xếp hạng chỉ dùng điểm FINALIZED. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-11-02 | Có báo cáo số lượng bài thiếu phiếu. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-11-03 | Không tự công bố khi còn đồng điểm chưa có quy tắc. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-11-04 | Bản kết quả công khai có thời điểm và version. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-11-05 | Participant không xem điểm nháp của Judge nếu chưa được phép. | Test qua UI + API/DB với dữ liệu kiểm soát; chụp log và kết quả mong đợi |
| AC-FR-11-06 | Khi thiếu phiếu một giám khảo, hệ thống phải: Chặn công bố hoặc xử lý exception theo policy đã phê duyệt. Kết quả: Không tuyên bố kết quả thiếu công bằng. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-11-07 | Khi hai ảnh đồng điểm, hệ thống phải: Áp dụng quy tắc tie-break đã chốt, nếu chưa có thì đánh dấu NEEDS_DECISION. Kết quả: Không sắp xếp ngẫu nhiên. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-11-08 | Khi điểm chưa chốt, hệ thống phải: Bỏ qua phiếu nháp và báo cảnh báo. Kết quả: Không cộng điểm nháp. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-11-09 | Khi hai organizer cùng công bố, hệ thống phải: Version guard tránh công bố lặp. Kết quả: Một snapshot chính thức. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |
| AC-FR-11-10 | Khi có yêu cầu sửa kết quả sau công bố, hệ thống phải: Thực hiện phiên bản đính chính kèm lịch sử; không sửa âm thầm. Kết quả: Có dấu vết kiểm tra. | Test unhappy với trigger tương ứng; xác nhận DB không phát sinh trạng thái sai |

> Chưa đánh dấu `Pass` cho bất kỳ AC nào trước khi chạy test thật. Sau khi làm phần mềm, điền `Expected / Actual / Evidence / Pass-Fail / Tester / Date` trong nhật ký test.

## 10. Test Scenarios (cả happy và unhappy)

| Test ID | Nhóm | Dữ liệu / thao tác | AC tham chiếu dự kiến | Kết quả mong đợi |
| --- | --- | --- | --- | --- |
| TC-FR-11-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | AC-FR-11-01 | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ |
| TC-FR-11-02 | Unhappy / alternate | Thiếu phiếu một giám khảo | AC-FR-11-06 | Không tuyên bố kết quả thiếu công bằng. |
| TC-FR-11-03 | Unhappy / alternate | Hai ảnh đồng điểm | AC-FR-11-07 | Không sắp xếp ngẫu nhiên. |
| TC-FR-11-04 | Unhappy / alternate | Điểm chưa chốt | AC-FR-11-08 | Không cộng điểm nháp. |
| TC-FR-11-05 | Unhappy / alternate | Hai Organizer cùng công bố | AC-FR-11-09 | Một snapshot chính thức. |
| TC-FR-11-06 | Unhappy / alternate | Có yêu cầu sửa kết quả sau công bố | AC-FR-11-10 | Có dấu vết kiểm tra. |

**Hướng test:** chuẩn bị seed có ID rõ ràng; chạy happy case, lỗi validation, lỗi phân quyền, lỗi trạng thái và lỗi dịch vụ phụ thuộc; lưu ảnh màn hình/log (ẩn thông tin nhạy cảm). Test Case chi tiết chính thức được theo dõi tại [Test Matrix](../../05-quality/test-matrix.md).

## 11. Khả thi, giới hạn, rủi ro và phương án fallback

- **Đánh giá sơ bộ:** Khả thi trung bình; cần bảng ví dụ tính tay để đối chiếu query thực thi.
- **Giới hạn giải pháp:** Không tự chọn tiêu chí đồng điểm nếu giảng viên chưa thống nhất; không bảo đảm minh bạch tuyệt đối nếu đầu vào thiếu.
- **Ước lượng thử để lập kế hoạch:** 22 giờ/người bao gồm phân tích, triển khai và test tối thiểu; **chưa phải cam kết**. Phải hiệu chỉnh theo năng lực và thời gian rảnh thực tế, xem [Capacity & Estimation](../../06-feasibility/capacity-estimation.md).
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

## 13. Phản biện nghiệp vụ mở rộng — V3 (tình huống đặc thù đồng điểm giải nhất; báo cáo đã công bố rồi phát hiện sai)

> **Nguồn:** [D] Phương án phân tích bổ sung để nhóm phản biện; chưa có bằng chứng triển khai và chưa là quy định được giảng viên duyệt. Với quyết định chưa chốt, xem [Decision Log](../../01-governance/open-questions.md).

### 13.1 Business question / tác nhân kích hoạt

**Thầy có thể hỏi:** “Hai tác phẩm cùng 9.5 tranh một giải; hoặc phát hiện một phiếu chấm thiếu sau công bố — hệ thống có làm sai nghiệp vụ không, giải quyết thế nào?”

**Mục tiêu phải giữ đúng:** Không công bố giải khi thiếu chính sách tie-break; PublishedSnapshot immutable, revision mới thay thế có lịch sử.

### 13.2 Quyết định và quy trình xử lý

Nếu chưa có tie-break được duyệt thì BLOCK_PUBLICATION; dùng ranking snapshot + revision; correction có lý do và quyền phê duyệt, không sửa bản đã công bố.

| Câu hỏi kiểm soát | Phương án phân tích / nguồn sự thật |
|---|---|
| Ai được thực hiện? | Actor hiện hành phải được BE xác thực theo role, quyền sở hữu và trạng thái của đối tượng. |
| Thời điểm quyết định? | Tại backend transaction hoặc checkpoint nghiệp vụ, không dựa vào trạng thái hiển thị cũ của FE. |
| Nếu xử lý không thành công? | Không công nhận trạng thái hoàn tất; trả mã lỗi ổn định; cho retry/đối soát tùy tính idempotent. |
| Ảnh hưởng hệ thống khác? | Phải kiểm tra FR-10; FR-12; phát sự kiện chỉ sau commit của trạng thái gốc. |
| Có thể đảo ngược không? | Chỉ khi rule và người có thẩm quyền cho phép; giữ lịch sử, không xóa sự kiện đã xảy ra. |

### 13.3 Test phản biện có thể tái tạo

| Test ID | Setup / kích hoạt | Expected (không phải Actual) | Actual / evidence |
|---|---|---|---|
| TC-BIZ-FR-11-01 | 2 submissions cùng điểm → trạng thái TIE_UNRESOLVED; chỉnh sau publish → v2 và changelog | Không công bố giải khi thiếu chính sách tie-break; PublishedSnapshot immutable, revision mới thay thế có lịch sử; hệ thống giải thích được từ chối/khôi phục. | **NOT RUN** – bổ sung log/ảnh/API trace khi có code |

### 13.4 Tính khả thi, giới hạn và câu trả lời khi hỏi

- **Đáp ứng dự kiến:** FE hiển thị chính xác; BE kiểm quyền và trạng thái; DB ràng buộc giao dịch; có test tái tạo và đối soát liên module.
- **Phụ thuộc:** FR-10; FR-12. Nếu phụ thuộc chưa sẵn sàng, phải dùng mock API hoặc đánh dấu BLOCKED, không tự báo Done.
- **Giới hạn:** Chưa thể khẳng định thời gian/chi phí đến khi nhóm đo thực tế. Các cơ chế như async retry, optimistic lock, audit, consent có thể bị giới hạn theo MVP nhưng không được hứa đã hoàn thành.
- **Khi thầy hỏi:** Trình bày **bối cảnh → invariant → xử lý BE/DB → hậu quả liên module → test → giới hạn**, và chỉ rõ rule nào còn chờ duyệt.
