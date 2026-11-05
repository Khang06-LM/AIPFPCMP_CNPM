# BE — Hợp đồng API dùng chung với frontend

> **ID:** BE-02 · **Phiên bản:** thiết kế chi tiết 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Bùi Đức Hưng · **Review:** Hoàng Duy Khánh; Nguyễn Khánh Hiền; Đào Thị Thanh Bình.
> Đây là tài liệu thiết kế, chưa phải mã nguồn/API/màn hình đã chạy. [G] là yêu cầu đề tài; [P] là lựa chọn trong kế hoạch; [D] là thiết kế đề xuất; TBD là quyết định chưa chốt. Tên file, route, schema, status và cơ chế kỹ thuật dưới đây thuộc [D] nếu không ghi khác.

## 1. Trạng thái và quy ước

**API contract** là thỏa thuận về dữ liệu gửi/nhận và cách xử lý lỗi. Toàn bộ route dưới đây là [D], **chưa triển khai**; base URL/tiền tố version: TBD. Chỉ chuyển sang OpenAPI thực sau review stack/DTO/BR.

Role là điều kiện cần; request còn phải đạt owner/contest/assignment scope. Body không cấp quyền bằng cách gửi user_id hoặc role. Timestamp trả ISO 8601 có timezone; format hiển thị do FE xử lý. ID là chuỗi opaque, không mặc định UUID cho đến khi chốt.

## 2. Một envelope thống nhất

[D] Chuẩn dưới đây thống nhất các ví dụ lỗi từng khác nhau trong README cũ. Reviewer FE/BE phải duyệt trước code.

Thành công:

```json
{
  "data": {"submission_id": "<id>", "status": "SUBMITTED", "version": 1},
  "meta": {"request_id": "<trace-id>"}
}
```

Lỗi:

```json
{
  "error": {
    "code": "CONTEST_CLOSED",
    "message": "Cuộc thi đã hết thời hạn nhận bài.",
    "details": [{"field": "contest_id", "code": "SUBMISSION_NOT_ALLOWED"}]
  },
  "meta": {"request_id": "<trace-id>"}
}
```

Response danh sách: data là mảng, meta có request_id và thông tin phân trang đã thống nhất. Đề xuất cursor/page_size; chưa chốt mức mặc định/trần. Không trả danh sách không giới hạn hoặc lộ total của dữ liệu không được xem.

| HTTP đề xuất | Ý nghĩa | FE xử lý |
|---|---|---|
| 200/201 | Thành công/đã tạo | Dựa data, không suy luận từ tiến trình upload |
| 202 | Đã nhận việc nền | Hiển thị đang xử lý, theo job status |
| 400 | Request hỏng cấu trúc | Giữ form, thông báo phù hợp |
| 401 | Chưa đăng nhập/token không hợp lệ | Tái xác thực theo session policy |
| 403 | Không đủ quyền | Chặn thao tác, không tự chuyển tài khoản |
| 404 | Không tồn tại hoặc chính sách che tài nguyên | Không hiển thị thông tin riêng |
| 409 | Xung đột version/trạng thái/request | Đọc lại dữ liệu, không ghi đè |
| 422 | Dữ liệu/quy tắc chưa đáp ứng | Gắn lỗi trường hoặc thông báo nghiệp vụ |
| 429 | Vượt hạn mức kỹ thuật nếu áp dụng | Theo chỉ dẫn retry server |
| 5xx | Lỗi máy chủ/phụ thuộc | Không kết luận bài sai hoặc thao tác chắc chắn thất bại |

403/404 cho tài nguyên riêng phải chọn chính sách nhất quán; không trả ngẫu nhiên hai kiểu cho cùng trường hợp.

## 3. Danh mục endpoint đề xuất

Dấu **điều kiện** nghĩa là cần quyết định nghiệp vụ trước khi dùng.

| Mã | Method / path | Quyền và scope | FR | Payload/kết quả trọng tâm |
|---|---|---|---|---|
| A01 | POST /auth/register | Khách; đăng ký Participant | 01 | Dữ liệu đăng ký; schema xác thực TBD; không tự đăng ký Admin |
| A02 | POST /auth/login | Khách | 01 | Credential → phiên theo transport đã chọn |
| A03 | POST /auth/logout | Phiên hiện tại | 01 | Kết thúc phiên; cơ chế revoke TBD |
| A04 | GET /auth/me | Đã xác thực | 01 | User hiện tại, vai trò/quyền hiệu lực |
| A05 | GET /admin/users | Admin | 02 | Danh sách phân trang, filter hợp lệ |
| A06 | PATCH /admin/users/{id} | Admin theo chính sách quản trị | 02 | Chỉ field whitelist + version |
| A07 | PUT /admin/users/{id}/roles | Admin; đa vai trò TBD | 01–02 | Vai trò được cấp; audit thay đổi |
| A08 | GET /master-data/{type} | Quyền đọc danh mục đã chốt | 02/05 | film-stocks, cameras, lenses, labs |
| A09 | POST/PATCH /admin/master-data/{type}[/{id}] | Admin | 02 | Tạo/cập nhật danh mục đã validate |
| A10 | GET/PATCH /admin/settings | Admin | 02 | Config whitelist; không trả secret |
| A11 | GET /admin/monitoring | Admin | 02 | Chỉ số đã thống nhất, không raw log mật |
| A12 | GET /contests | Quyền xem public/private TBD | 03 | Danh sách theo visibility, lọc/phân trang |
| A13 | POST /contests | Organizer | 03 | Chủ đề, mô tả, cấu hình; owner từ principal |
| A14 | GET/PATCH /contests/{id} | Người có quyền xem/sửa contest | 03 | Detail; PATCH cần version nếu chọn locking |
| A15 | POST/PATCH /contests/{id}/categories[/{category_id}] | Organizer đúng contest | 03 | Hạng mục, không nhận FK ngoài contest |
| A16 | POST/PATCH /contests/{id}/rounds[/{round_id}] | Organizer đúng contest | 03 | Vòng, thứ tự/lịch; nhiều vòng được hỗ trợ |
| A17 | POST/PATCH /contests/{id}/criteria[/{criterion_id}] | Organizer đúng contest | 03 | Rubric, range/trọng số theo policy |
| A18 | POST/PATCH /contests/{id}/prizes[/{prize_id}] | Organizer đúng contest | 03 | Cơ cấu giải thưởng |
| A19 | GET/POST /contest-templates | Organizer theo scope mẫu TBD | 03 | Liệt kê/lưu mẫu tái sử dụng |
| A20 | POST /contests/from-template | Organizer | 03 | Template + override hợp lệ → contest mới |
| A21 | POST /contests/{id}/enrollments | Participant | 04 | Đăng ký người hiện tại, không chọn participant khác |
| A22 | GET /me/enrollments | Participant hiện tại | 04 | Đăng ký và trạng thái của mình |
| A23 | GET /contests/{id}/enrollments | Organizer đúng contest | 04 | Danh sách theo quyền |
| A24 | POST /enrollments/{id}/decisions | Điều kiện: có duyệt đăng ký | 04 | Decision/reason/version; D-03 |
| A25 | GET/POST /film-rolls | Participant, chỉ của mình | 05 | Roll + metadata |
| A26 | GET/PATCH /film-rolls/{id} | Owner; quyền sửa theo giai đoạn TBD | 05 | Detail; update whitelist/version |
| A27 | GET/POST /film-rolls/{id}/frames | Owner của roll | 05 | Khung hình, thông tin chụp/scan |
| A28 | GET/PATCH /film-frames/{id} | Owner; chính sách sửa TBD | 05 | Dữ liệu frame, không đổi owner qua body |
| A29 | POST /uploads/init | Participant | 06 | Mục đích, filename, declared MIME/bytes → upload session |
| A30 | POST /uploads/complete | Owner session | 06 | upload_session_id → media READY sau kiểm thật |
| A31 | POST /submissions | Participant | 06 | contest/category/enrollment/frame/asset/evidence; request key |
| A32 | GET /me/submissions | Participant | 06/17 | Bài của mình và trạng thái |
| A33 | GET /submissions/{id} | Owner/Organizer/Judge đúng assignment; projection riêng | 06–10 | Detail theo quyền, không trả cùng DTO cho mọi role |
| A34 | GET /submissions/{id}/validation-report | Owner/Organizer theo policy | 07 | Từng check pass/fail/error |
| A35 | GET /contests/{id}/review-queue | Organizer đúng contest | 08 | Hàng đợi và cảnh báo theo scope |
| A36 | POST /submissions/{id}/review | Organizer đúng contest | 08 | APPROVE/REJECT; REVISION nếu được duyệt; reason/version |
| A37 | GET /submissions/{id}/review-history | Quyền xem lịch sử TBD | 08 | Quyết định theo projection được phép |
| A38 | GET/POST /judge-assignments | Organizer đúng contest; Judge chỉ xem phần mình | 09 | judge/category/round, scope kiểm tra server |
| A39 | GET /judge/assignments | Judge hiện tại | 09 | Bài/vòng/hạng mục được giao |
| A40 | GET /judge/scoresheets/{id} | Judge đúng assignment | 10 | Rubric + nháp/phiếu của mình |
| A41 | PUT /judge/scoresheets/{id} | Judge đúng assignment | 10 | criteria scores, comments, expected_version |
| A42 | POST /judge/scoresheets/{id}/finalize | Judge đúng assignment | 10 | expected_version + request key |
| A43 | POST /contests/{id}/rankings/preview | Organizer đúng contest | 11 | Round/category + policy version → preview hoặc blocker |
| A44 | POST /contests/{id}/publish-results | Organizer đúng contest | 11 | ranking_snapshot_id/version + request key |
| A45 | GET /contests/{id}/results | Quyền xem sau công bố TBD | 11 | Snapshot công bố, giải và thông tin được phép |
| A46 | GET /me/results | Participant | 11/17 | Kết quả/nhận xét mình được xem |
| A47 | GET /archive | Quyền xem theo visibility đã chốt | 12 | Tác phẩm và metadata công khai được phép |
| A48 | GET /archive/{id} | Theo quyền record | 12 | Hồ sơ tác phẩm; không tự trả evidence |
| A49 | POST/PATCH /contests/{id}/archive-entries[/{entry_id}] | Organizer đúng contest | 12 | Quản lý triển lãm/visibility theo chính sách |
| A50 | GET /submissions/{id}/ai-analyses | Organizer; các role khác theo policy | 13–15 | Phân tích/cảnh báo/model version, không verdict tự động |
| A51 | POST /submissions/{id}/ai-jobs | Organizer/service được cấp quyền | 13–15 | Retry/khởi tạo khi policy cho phép, không public provider key |
| A52 | GET /ai-jobs/{id} | Quyền dựa vào submission liên quan | 13–15 | Job state + failure an toàn |
| A53 | GET/PATCH /submissions/{id}/tags | Quyền đọc/sửa tag TBD | 15 | Nhãn AUTO/MANUAL, nguồn và trạng thái |
| A54 | GET /organizer/contests/{id}/analytics | Organizer đúng contest | 16 | Chỉ số/bộ lọc/định nghĩa được duyệt |
| A55 | POST /organizer/contests/{id}/report-exports | Organizer đúng contest | 16 | Export type/filters đã chốt → job hoặc file có quyền |
| A56 | GET/PATCH /me/profile | Người hiện tại | 17 | Hồ sơ whitelist |
| A57 | GET/POST/PATCH /me/portfolio[/{item_id}] | Participant owner | 17 | Thêm/cập nhật tác phẩm theo quyền đã chốt |
| A58 | GET /me/history | Participant | 17 | Lịch sử tham gia/kết quả, projection phù hợp |
| A59 | GET /admin/audit-logs | Admin theo chính sách | 02/18 | Log đã che dữ liệu nhạy cảm |
| A60 | GET/PATCH /me/notifications[/{id}] | Điều kiện: chọn in-app notification | 18 | Danh sách/đánh dấu đã đọc |

POST/PATCH cùng dòng là nhóm endpoint logic cần tách operation khi viết OpenAPI. Không mặc định bổ sung DELETE, reopen-score, withdraw, email/push hoặc public portfolio khi quyền chưa được xác nhận.

## 4. Các DTO cần thống nhất sâu

| DTO | Field chính đề xuất | Kiểm tra và TBD |
|---|---|---|
| ContestWrite | title, description, open_at, close_at, rules_version, expected_version | Thời điểm đóng sau mở; sửa sau có bài/điểm cần policy |
| FilmRollWrite | film_stock_id, camera_id, lens_id, iso_used, film_format, lab_id | Field bắt buộc theo D-04/D-07; owner lấy từ principal |
| FilmFrameWrite | frame_number, notes, scan_info, shooting_location | shooting_location có trong đề tài nhưng model repo chưa chốt vị trí; cần cập nhật data dictionary trước migration |
| UploadInit | purpose, filename, mime_type, bytes, contest_id khi cần | File thật được kiểm lại server; giới hạn từ thể lệ, không từ tên file |
| SubmissionCreate | contest_id, category_id, enrollment_id, frame_id, asset_id, evidence_asset_ids | D-02: một ảnh/bộ ảnh chưa chốt; cấu trúc này minh họa một ảnh chính, không thu hẹp phạm vi |
| ReviewWrite | decision, reason, expected_version | Quyền contest, trạng thái, lý do và revision cần policy |
| ScoreSheetWrite | criterion_scores[{criterion_id,score,comment}], comment, expected_version | ID criterion thuộc đúng rubric/round; không gửi tổng điểm làm chuẩn |
| PublishWrite | ranking_snapshot_id, expected_version | Snapshot đúng contest/round và policy chấm đã duyệt |
| AIAnalysisRead | task_type, model_version, score, label, findings, processed_at | score không được gọi xác suất khi chưa hiệu chuẩn |

Idempotency đề xuất qua header Idempotency-Key, scope theo principal+operation. Cùng key/body trả cùng kết quả; cùng key/body khác trả conflict; retention TBD. Version dùng expected_version trong các DTO trên; không đồng thời tự áp thêm If-Match mà chưa có quy ước. Ảnh dùng đường truy cập có quyền, không raw object_key cố định.

## 5. Happy/unhappy có expected

- Thành công: A31 với các FK khớp contest và frame đúng owner, media READY, thời điểm được phép → 201, submission_id/status/version, chỉ một bài được tạo.
- Ngoại lệ: A31 dùng category thuộc cuộc thi khác → 422 CATEGORY_CONTEST_MISMATCH theo contract đề xuất; không tạo bài, không gắn asset sang contest sai.
- Lỗi chưa rõ kết quả: A42 timeout sau server commit → FE đọc lại A40; không tự chấm lần hai hoặc hiển thị chắc chắn thất bại.
- Test thiết kế: [Business Scenarios](../Docs/05-quality/business-scenario-tests.md); Actual: NOT RUN.
- Jira: [GC-53](https://0703032147khang.atlassian.net/browse/GC-53), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65).

## 6. Gate trước khi xuất OpenAPI

Chốt DTO và những field bổ sung ở Entity/Dictionary; đủ route cho mẫu cuộc thi/admin/portfolio; kiểm scope từng endpoint; thống nhất error codes với FE; chốt phân trang/session/transport; ghi version hợp đồng. Không tạo servers URL giả hoặc gắn nhãn Swagger chạy được.
