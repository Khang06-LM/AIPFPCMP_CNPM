# BE — Kiến trúc, mô-đun và trách nhiệm từng file

> **ID:** BE-01 · **Phiên bản:** thiết kế chi tiết 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Bùi Đức Hưng · **Review:** Hoàng Duy Khánh; Nguyễn Khánh Hiền; Đào Thị Thanh Bình.
> Đây là tài liệu thiết kế, chưa phải mã nguồn/API/màn hình đã chạy. [G] là yêu cầu đề tài; [P] là lựa chọn trong kế hoạch; [D] là thiết kế đề xuất; TBD là quyết định chưa chốt. Tên file, route, schema, status và cơ chế kỹ thuật dưới đây thuộc [D] nếu không ghi khác.

## 1. Phân lớp dễ hiểu

**Controller** nhận request và trả response; **service** thực hiện một hành động nghiệp vụ; **repository** đọc/ghi CSDL; **policy** kiểm quy tắc/quyền. **DTO** là cấu trúc dữ liệu trao đổi, không phải nguyên bản toàn bộ bảng CSDL.

[D] Luồng chuẩn: request → xác thực → kiểm quyền theo tài nguyên → validate DTO → service/policy → repository + transaction → DTO response. Việc gọi storage/AI phải có cơ chế ghi nhận lỗi, không giả rằng dịch vụ ngoài cùng rollback được với CSDL.

## 2. File dùng chung dự kiến — chưa tạo source

Tên trong bảng là tên logic, chưa chốt extension hoặc framework.

| File logic | Trách nhiệm | Nhận → trả | Không đặt ở đây |
|---|---|---|---|
| bootstrap / app | Khởi tạo ứng dụng, đăng ký mô-đun và cấu hình | Cấu hình đã kiểm → ứng dụng | Công thức điểm |
| config/environment | Đọc/kiểm biến môi trường, phân local/test/staging | Biến cấu hình → cấu hình kiểu hóa | Giá trị secret trong repo |
| auth/authentication | Kiểm JWT theo thuật toán/key đã cấu hình | Token → principal hoặc lỗi | Tin role từ request body |
| auth/authorization-policy | Kiểm role + ownership + contest + assignment | Principal, resource, action → allow/deny | Chỉ dựa nút ẩn FE |
| common/error-mapper | Chuẩn hóa lỗi thành code/message/details/request_id | Lỗi nghiệp vụ → HTTP response | Stack trace cho người dùng |
| common/request-context | Gắn request_id, principal và dấu thời gian | Request → context | Thông tin mật trong log |
| database/unit-of-work | Biên transaction CSDL | Các thay đổi → commit/rollback | Giả transaction bao được cloud |
| storage/object-storage-adapter | Giao tiếp nhà cung cấp lưu ảnh | Yêu cầu đã cấp quyền → object metadata | Quyết định thẩm định bài |
| jobs/job-runner | Chạy công việc nền nếu chọn kiến trúc này | Job → kết quả/trạng thái lỗi | Quyền phê duyệt của Organizer |
| audit/audit-writer | Ghi lịch sử tối thiểu, đã loại dữ liệu mật | Actor/action/object/version → dấu vết | Password/token/byte ảnh |
| tests/fixtures | Dữ liệu giả và factory phục vụ kiểm tra | Scenario → dữ liệu tái lập | Tài khoản người thật |

## 3. File trong mỗi mô-đun nghiệp vụ

| Nhóm file logic | Nội dung phải viết | Ví dụ |
|---|---|---|
| *.controller | Route, DTO, principal, service gọi, status code | submissions.controller nhận thao tác gửi |
| *.service | Use case và transaction boundary | submit-photo kiểm và tạo bài |
| *.policy | Điều kiện/guard từ BR đã chốt | can-submit, can-finalize-score |
| *.repository | Query giới hạn scope và thay đổi có version | find-assigned-scoresheet |
| *.dto / *.schema | Các field được phép nhận/trả, bắt buộc/kiểu | create-submission, submission-detail |
| *.mapper | Map entity sang DTO theo người xem | public archive không có negative |
| *.tests | Kiểm quy tắc, quyền và lỗi có ý nghĩa | frame sai owner không tạo submission |

Không bắt mỗi màn hình phải có một bảng hoặc một service riêng. Chia theo trách nhiệm và use case; tránh controller chứa toàn bộ nghiệp vụ.

## 4. Danh sách mô-đun phải bao phủ

| Mô-đun | FR | File/use case trọng tâm | Entity |
|---|---|---|---|
| auth | 01 | register/login/logout/me; token handling; authorization | User, Role; UserRole nếu duyệt đa vai trò |
| admin | 02 | users/roles; cấu hình; master-data; giám sát | User, FilmStock, Camera, Lens, FilmLab; AuditLog |
| contests | 03 | create/update; category/round/criterion/prize; template | Contest, Category, ContestRound, Criterion, Prize |
| enrollments | 04 | enroll/list/mine; approve nếu policy chọn | Enrollment |
| film | 05 | roll/frame; metadata; kiểm owner | FilmRoll, FilmFrame |
| submissions | 06 | upload/init/complete; submit/detail/status | MediaAsset, Submission; UploadSession tùy chọn |
| validation | 07 | kiểm rule; report; lỗi kỹ thuật riêng | ValidationReport |
| reviews | 08 | hàng đợi, decision, lịch sử | ReviewDecision |
| judging | 09–10 | assignment; score save/finalize, round | JudgeAssignment, ScoreSheet, CriterionScore |
| results | 11 | aggregate/preview/publish; snapshot; award | RankingSnapshot, Award |
| archive | 12 | đọc kho/triển lãm và hồ sơ được công khai | ArchiveEntry, MediaAsset |
| ai | 13–15 | dispatch; lấy trạng thái/phân tích; tagging | AIJob, AIAnalysis, Tag, MediaTag |
| analytics | 16 | tổng hợp, phân tích, xuất báo cáo | Dữ liệu cuộc thi theo scope |
| profiles | 17 | hồ sơ, portfolio, lịch sử, phản hồi | User + dữ liệu liên kết; model bổ sung cần review |
| notification/audit | 18 | thông báo/historical events nếu phê duyệt | Notification/AuditLog; outbox nếu chọn |

**Khoảng trống thiết kế cần giải quyết:** repo chưa định nghĩa riêng entity Template, Portfolio hoặc cấu hình hệ thống. Không tự thêm bảng cố định; reviewer phải chọn bảng riêng, cấu hình dạng tài liệu hoặc quan hệ hiện có rồi cập nhật Entity/ERD/DTO cùng PR.

## 5. Thứ tự thực hiện và phối hợp

- Nền chung/auth → contest + danh mục → film/enrollment → upload/submission.
- Submission đủ điều kiện → validation/review → assignment/scoring → ranking/publication/archive.
- AI có thể phát triển theo contract song song, nhưng tích hợp cần media/quyền; analytics cần thống nhất chỉ tiêu và trạng thái dữ liệu.
- TV2 chủ trì nền chung và CSDL; owner Participant/Organizer/Admin/AI cùng làm use case tương ứng, tránh dồn toàn backend cho một người.
- Mock contract không được đưa vào báo cáo như backend thật.

## 6. Điểm review và ví dụ

- Happy: request create contest qua controller → service → repository; chỉ lưu trường được phép và trả DTO.
- Unhappy: Organizer của contest A gọi review contest B; authorization/policy từ chối **trước ghi dữ liệu**.
- Review: DTO không lộ object_key hoặc secret, truy vấn có scope, transaction rõ, command và test thật được bổ sung khi hiện thực.
- Source: [FR Index](../Docs/02-requirements/README.md), [Entity](../Docs/03-domain/entities.md).
- Jira: [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-46](https://0703032147khang.atlassian.net/browse/GC-46), [GC-56](https://0703032147khang.atlassian.net/browse/GC-56).
- Test tham chiếu: [Business Scenario Tests](../Docs/05-quality/business-scenario-tests.md). Actual: NOT RUN.
