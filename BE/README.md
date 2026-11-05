# BE — Tài liệu thiết kế và hướng dẫn triển khai backend

> **ID:** BE-00 · **Phiên bản:** thiết kế chi tiết 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Bùi Đức Hưng · **Review:** Lê Minh Khang; các owner FE/AI.
> Đây là tài liệu thiết kế, chưa phải mã nguồn/API/màn hình đã chạy. [G] là yêu cầu đề tài; [P] là lựa chọn trong kế hoạch; [D] là thiết kế đề xuất; TBD là quyết định chưa chốt. Tên file, route, schema, status và cơ chế kỹ thuật dưới đây thuộc [D] nếu không ghi khác.

**Backend (BE)** là phần máy chủ nhận yêu cầu từ giao diện, kiểm tra quyền và quy tắc, xử lý dữ liệu rồi trả kết quả. Thư mục này hiện chứa tài liệu; chưa có source backend chạy được hoặc kết quả kiểm thử ứng dụng.

## 1. Đọc file nào để làm việc gì?

| File hiện có sau cập nhật này | Nội dung cần đọc | Liên hệ Sprint 4–5 |
|---|---|---|
| [01 — Kiến trúc và trách nhiệm file](01-architecture-and-files.md) | Mô-đun, phân lớp, từng loại file và trình tự xây dựng | GC-45, GC-46, GC-48..51 |
| [02 — Hợp đồng API](02-api-contract.md) | Route, request/response, quyền, lỗi, định danh và contract thống nhất cho FE | GC-53; GC-56 |
| [03 — Dữ liệu và vòng đời](03-data-and-lifecycle.md) | Entity, metadata, upload/nộp bài, review/chấm/công bố, transaction, version | GC-47; GC-56 |
| [04 — Bảo mật và phân quyền](04-security-and-permissions.md) | JWT, RBAC, owner/contest/assignment scope, media và secrets | GC-39, GC-48; GC-60, GC-67 |
| [05 — AI và báo cáo](05-ai-and-reporting.md) | Bốn năng lực AI, contract tích hợp, lỗi và cách đánh giá | GC-42, GC-51, GC-54; GC-59, GC-68 |
| [06 — Kiểm thử và vận hành](06-testing-and-operation.md) | Kế hoạch test, cấu hình, bằng chứng chạy, deploy và phục hồi | GC-64..71 |

Mã Jira dẫn tới dự án [GC](https://0703032147khang.atlassian.net/browse/GC-37). Các mã nhiệm vụ là công việc đã lập; không biểu thị đã hoàn thành.

## 2. Trách nhiệm của backend

- Là nguồn quyết định quyền, thời gian ghi nhận nộp, trạng thái và kết quả nghiệp vụ; không tin user_id/role/điểm tổng do trình duyệt tự cung cấp.
- Phục vụ đủ Admin, Organizer, Judge, Participant; 5 luồng cấu hình → nộp bài → thẩm định → chấm → công bố/lưu trữ.
- Quản lý metadata và quan hệ FilmRoll → FilmFrame → Submission; ảnh nằm ở dịch vụ lưu tệp, dữ liệu quan hệ lưu trong CSDL.
- Tích hợp phát hiện trùng/tương đồng, cảnh báo ảnh có dấu hiệu AI, gắn nhãn/phân loại, phân tích/báo cáo.
- Giữ riêng kết quả máy, quyết định con người, điểm từng giám khảo và kết quả công bố.
- Phục vụ quản trị, danh mục, portfolio và theo dõi hồ sơ; thông báo/audit chi tiết là phần cần xác nhận theo nguồn FR-18.

## 3. Công nghệ: có căn cứ nào?

| Nội dung | Căn cứ và trạng thái |
|---|---|
| JWT + RBAC; Cloud Object Storage; thiết kế mô-đun | [G] mục 3.2.d |
| ASP.NET Core Web API hoặc Node.js/Express | [P]/bảng công nghệ đề xuất; chưa chọn, D-11 |
| PostgreSQL hoặc MySQL | Đề tài đề xuất lựa chọn; PostgreSQL trong bộ thiết kế repo chưa phải quyết định cuối |
| Firebase Storage hoặc Azure Blob | Đề xuất; nhà cung cấp/chính sách truy cập chưa chốt |
| OpenAPI/Swagger, worker/queue, Python service | Thiết kế tích hợp đề xuất; không đồng nghĩa bắt buộc dùng mọi công cụ |
| Thời hạn token, kích thước ảnh, công thức điểm, số vòng/giám khảo | TBD; không đặt giá trị mặc định thay thể lệ |

Chỉ khởi tạo **một** backend sau khi chọn stack; không viết song song hai hệ thống Express và ASP.NET. Không tự dùng công nghệ của báo cáo mẫu nha khoa.

## 4. Quy ước dùng bộ tài liệu

Hợp đồng ở [02](02-api-contract.md) là **một bản dự thảo thống nhất** cho FE–BE; thay đổi cần review cả hai phía. Danh sách endpoint không phải danh sách API đã deploy. Các trạng thái tiếp tục là đề xuất ở [State Transitions](../Docs/03-domain/state-transitions.md); quyết định nghiệp vụ chưa chốt không được biến thành luật trong code.

Phần tên source trong tài liệu 01 chỉ hướng dẫn **sẽ tạo file gì**. Không có thư mục src, migration hoặc command build giả được thêm vào lần cập nhật này.

## 5. Khi bắt đầu hiện thực

1. Chốt các blocker ở [Decision Log](../Docs/01-governance/open-questions.md).
2. Review FR/Entity/State/AC, chọn stack và phiên bản; cập nhật contract rồi mới migration.
3. Tạo source và môi trường cấu hình; triển khai từng luồng có kiểm tra quyền.
4. Mỗi PR liên kết Jira/FR, có reviewer và kiểm tra phù hợp; ghi actual/evidence.
5. Thêm lệnh install/run/build/test **đã thử thật**, API docs URL và staging URL khi có; hiện chưa có các giá trị này.

## 6. Nguồn và truy vết

[FR-01..18](../Docs/02-requirements/README.md) · [Entity Catalogue](../Docs/03-domain/entities.md) · [Data Dictionary](../Docs/03-domain/data-dictionary.md) · [Business Invariants](../Docs/03-domain/business-invariants.md) · [Failure Recovery](../Docs/05-quality/failure-recovery-matrix.md) · [FE](../FE/README.md).

## 7. Ví dụ nghiệm thu thiết kế

- Thành công: một Participant nộp bản scan gắn frame thuộc cuộn của mình; bản thiết kế chỉ ra API, kiểm quyền, transaction và dữ liệu trả về.
- Ngoại lệ: thay frame_id bằng frame của người khác; BE phải từ chối theo contract mà không tạo bài hoặc lộ thông tin riêng.
- Trạng thái kiểm tra ứng dụng: **NOT RUN**. Reviewer còn phải xác nhận khả năng hiện thực và các TBD.
