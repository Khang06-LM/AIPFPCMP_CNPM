# FE — Danh mục màn hình và mapping API

> **ID:** FE-02 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Hoàng Duy Khánh; Nguyễn Khánh Hiền; Lê Minh Khang · **Review:** Bùi Đức Hưng và owner nghiệp vụ liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Các route/component/states dưới đây là [D], chưa phải ứng dụng đã chạy.

## 1. Quy ước

SCR là mã màn hình thiết kế. Route là URL đề xuất độc lập framework. Mã A tham chiếu [BE contract](../BE/02-api-contract.md); mọi màn hình phải có permission/loading/empty/error states. “Public” chỉ hoạt động khi visibility được xác nhận.

| SCR | Màn hình / route đề xuất | Người dùng | Input và thao tác | Output / API | FR |
|---|---|---|---|---|---|
| 01 | Đăng ký /register | Khách | Dữ liệu đăng ký đã chốt | Tài khoản Participant; A01 | 01 |
| 02 | Đăng nhập /login | Khách | Credential; return route an toàn | Session; A02..04 | 01 |
| 03 | Danh sách cuộc thi /contests | Theo visibility | Filter/search/page | Contest list; A12 | 03 |
| 04 | Chi tiết /contests/:id | Theo quyền | Xem rule/hạng mục/lịch | A14; action đăng ký theo quyền | 03/04 |
| 05 | Đăng ký của tôi /participant/enrollments | Participant | Đăng ký, đọc trạng thái | A21..22 | 04 |
| 06 | Hồ sơ /participant/profile | Participant | Field whitelist | A56 | 17 |
| 07 | Portfolio /participant/portfolio | Participant | Tác phẩm và visibility đã chốt | A57 | 17 |
| 08 | Lịch sử /participant/history | Participant | Filter cuộc thi | A58/A46 | 17 |
| 09 | Cuộn phim /participant/film-rolls | Owner | Danh sách/tạo/sửa theo policy | A08/A25..26 | 05 |
| 10 | Khung hình /participant/film-rolls/:id/frames | Owner | Frame/scan/location | A27..28 | 05 |
| 11 | Nộp bài /participant/submissions/new | Participant | Contest/frame/file/evidence | Upload A29..30; gửi A31 | 06 |
| 12 | Bài của tôi /participant/submissions | Owner | Filter/status/page | A32 | 06/17 |
| 13 | Chi tiết bài /participant/submissions/:id | Owner | Xem metadata/validation/feedback | A33..34, lịch sử theo quyền | 06/07/17 |
| 14 | Kết quả /participant/results | Participant | Contest/round | A46; feedback được phép | 11/17 |
| 15 | Cấu hình /organizer/contests/:id/settings | Organizer scope | Chủ đề/rule/category/round/criteria/prizes | A13..18 | 03 |
| 16 | Mẫu /organizer/templates | Organizer scope TBD | Lưu/sử dụng mẫu, override | A19..20 | 03 |
| 17 | Danh sách đăng ký /organizer/contests/:id/enrollments | Organizer scope | Filter; decision nếu có | A23..24 | 04 |
| 18 | Hàng đợi duyệt /organizer/contests/:id/reviews | Organizer scope | Filter; xem bài/cảnh báo; quyết định | A33..37/A50..52 | 07/08/13/14 |
| 19 | Phân công /organizer/contests/:id/assignments | Organizer scope | Judge/category/round | A38 | 09 |
| 20 | Bài được giao /judge/assignments | Judge | Round/category; mở phiếu | A39 | 09 |
| 21 | Phiếu chấm /judge/scoresheets/:id | Assigned Judge | Điểm/nhận xét; save/finalize | A40..42 | 10 |
| 22 | Tổng hợp/công bố /organizer/contests/:id/results | Organizer scope | Preview, xử lý blocker, xác nhận publish | A43..45 | 11 |
| 23 | Kết quả cuộc thi /contests/:id/results | Theo quyền công bố | Xem snapshot/awards | A45 | 11 |
| 24 | Kho/triển lãm /archive và /archive/:id | Theo visibility | Filter/xem tác phẩm | A47..48 | 12/15 |
| 25 | Quản lý triển lãm /organizer/contests/:id/archive | Organizer scope | Entry/visibility đã chốt | A49 | 12 |
| 26 | Báo cáo /organizer/contests/:id/analytics | Organizer scope | Metric/filter/export | A54..55 | 16 |
| 27 | Người dùng/quyền /admin/users | Admin | List/update/assign roles | A05..07 | 01/02 |
| 28 | Danh mục /admin/master-data | Admin | Create/update | A08..09 | 02 |
| 29 | Cấu hình/giám sát /admin/settings và /admin/monitoring | Admin | Config whitelist/đọc chỉ số | A10..11 | 02 |
| 30 | Nhật ký /admin/audit | Admin theo policy | Filter an toàn | A59 | 02/18 |
| 31 | Thông báo /me/notifications | Có điều kiện | Xem/đánh dấu đọc | A60 nếu duyệt in-app | 18 |

Nhãn AI/tags có thể là panel ở SCR13/18/24; không cần thêm màn hình quản trị AI riêng khi chưa có yêu cầu. Ẩn danh giám khảo/thí sinh và quyền thấy AI flags phải theo policy.

## 2. Nội dung mỗi đặc tả màn hình

Ghi SCR ID, FR/Jira, actor/scope, entry/exit, field/type/required/source, actions, API/version, states/messages, cache invalidation và testcase. Tên nút chỉ là đề xuất; wireframe không được coi ảnh chụp sản phẩm thực.

Happy: SCR18 chỉ liệt kê bài contest được giao, thao tác A36 cập nhật kết quả rồi refresh.
Unhappy: đổi contest_id URL → BE từ chối, UI xóa dữ liệu không còn quyền; không dùng cache contest trước.
Jira: [GC-52](https://0703032147khang.atlassian.net/browse/GC-52); test [Test Matrix](../Docs/05-quality/test-matrix.md). Actual: NOT RUN.
