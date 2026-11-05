# FE — Tài liệu thiết kế giao diện và tích hợp

> **ID:** FE-00 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Hoàng Duy Khánh; Nguyễn Khánh Hiền; Lê Minh Khang · **Review:** Bùi Đức Hưng và owner nghiệp vụ liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Các route/component/states dưới đây là [D], chưa phải ứng dụng đã chạy.

**Frontend (FE)** là giao diện người dùng: màn hình, biểu mẫu, điều hướng, hiển thị dữ liệu và gọi API. Thư mục hiện chứa tài liệu, chưa có source ứng dụng chạy được.

## 1. Mục lục file

| File | Nội dung | Jira |
|---|---|---|
| [01 — Kiến trúc và từng file](01-architecture-and-files.md) | App shell, route, feature, API client, form/state/test | GC-46, GC-52 |
| [02 — Danh mục màn hình](02-screen-catalogue.md) | Mã màn hình, vai trò, route, input/output, FR/API | GC-40..42, GC-52 |
| [03 — Luồng Participant](03-participant-flow.md) | Hồ sơ/portfolio, đăng ký, roll/frame, nộp ảnh, kết quả | GC-49, GC-57, GC-72 |
| [04 — Luồng quản lý](04-management-flow.md) | Organizer, Judge, Admin, công bố/triển lãm/report | GC-48, GC-50, GC-58, GC-60, GC-73 |
| [05 — API và trạng thái UI](05-api-and-ui-states.md) | Session, request, lỗi, retry, cache và bảo mật | GC-53, GC-65 |
| [06 — Kiểm thử và vận hành](06-testing-and-operation.md) | Test UI/E2E, setup có điều kiện và bằng chứng | GC-66, GC-71..73 |

Đọc [API contract chung](../BE/02-api-contract.md) trước khi viết request. A01..A60 là mã nhóm endpoint thiết kế, chưa phải API triển khai.

## 2. Phạm vi theo vai trò

| Vai trò | Trách nhiệm giao diện |
|---|---|
| Participant | Profile/portfolio, cuộc thi/đăng ký, phim/khung hình, nộp bài, trạng thái/kết quả/feedback |
| Organizer | Cấu hình/mẫu cuộc thi, đăng ký/duyệt bài, cảnh báo AI, phân công, quản lý chấm, công bố/giải thưởng/triển lãm/report |
| Judge | Assignment, xem tác phẩm theo quyền, chấm nhiều vòng/tiêu chí, nhận xét và chốt |
| Admin | Người dùng/quyền, danh mục, cấu hình chung và giám sát |

Trang xem công khai chỉ hiển thị dữ liệu đã được cấp quyền công bố; không mặc định có role đăng ký Public.

## 3. Công nghệ và những quyết định chưa chốt

React/Next.js và Flutter là đề xuất trong tài liệu, không phải quyết định đã chốt. D-01 xác định phải có cả Web/Mobile hay được chọn; Web responsive không tự thay yêu cầu ứng dụng di động. Cấu trúc file 01 trung lập framework; tên .tsx nếu dùng sau này chỉ áp dụng sau chọn stack.

Session transport, quyền xem thông tin giám khảo/thí sinh, quyền sửa bài/điểm, formula/tie-break, limits ảnh, visibility portfolio và kênh thông báo đều chưa được tự đặt.

## 4. Nguyên tắc UI

- BE là nơi kiểm quyền/điều kiện cuối; ẩn nút không bảo vệ API.
- Upload 100% chỉ là truyền tệp xong; phải có xác nhận gửi bài từ BE.
- Lấy thể lệ/giới hạn/tiêu chí từ cấu hình; không viết cứng 10MB, số ảnh, deadline hoặc công thức.
- Hiển thị đầy đủ loading/empty/error/conflict/success; giữ dữ liệu nhập khi lỗi có thể khôi phục.
- AI cảnh báo là tín hiệu hỗ trợ, không ghi chắc “ảnh giả” hay tự loại.
- Không đưa provider key, JWT signing key hoặc cloud credential vào frontend.

## 5. Nguồn và nghiệm thu thiết kế

[FR Index](../Docs/02-requirements/README.md), [Decision Log](../Docs/01-governance/open-questions.md), [Entity](../Docs/03-domain/entities.md), [State](../Docs/03-domain/state-transitions.md), [E2E cases](../Docs/05-quality/e2e-test-cases.md).

Happy: người nộp thấy mã bài và trạng thái sau BE xác nhận.
Unhappy: mất phản hồi sau bấm Gửi → hiện chưa rõ kết quả, đọc lại trạng thái trước gửi tiếp; không tạo key mới tùy tiện.
Actual UI/E2E: NOT RUN. Chưa có screenshots sản phẩm thật hoặc URL chạy; không dùng ảnh mẫu nhóm khác làm bằng chứng.
