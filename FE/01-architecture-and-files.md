# FE — Kiến trúc và trách nhiệm từng file

> **ID:** FE-01 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Hoàng Duy Khánh; Nguyễn Khánh Hiền · **Review:** Bùi Đức Hưng và owner nghiệp vụ liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Các route/component/states dưới đây là [D], chưa phải ứng dụng đã chạy.

## 1. Cách tổ chức

[D] Route/page xác định người dùng vào đâu; feature điều phối use case; component biểu diễn dữ liệu; API client gọi contract; state quản lý dữ liệu đang sửa hoặc đã tải. **Component** là khối giao diện tái sử dụng. **Hook/composable** là phần logic dùng lại, tên tùy framework.

Bảng là file logic **sẽ tạo khi chọn stack**, không phải source đã tồn tại. Nếu chọn Next.js, page/layout/client-server boundary phải thiết kế theo phiên bản thực; chưa ghi command hoặc API thư viện cụ thể.

## 2. Các file nền chung dự kiến

| File logic | Trách nhiệm | Nhận → trả | Điều không làm |
|---|---|---|---|
| app/router | Khai báo route ở screen catalogue | URL → trang + params | Tự cấp quyền BE |
| app/layout | Shell/header/sidebar theo vai trò | Session + children → khung | Hard-code danh tính/role demo |
| auth/session-provider | Trạng thái khởi tạo/đăng nhập/hết phiên | Session adapter → principal | Lưu signing secret |
| auth/route-guard | Điều hướng theo session/quyền UI | Route + principal → allow/redirect | Cho rằng guard đủ bảo mật |
| api/client | Base URL, transport, request_id, lỗi | Request → data hoặc typed error | Tự retry mọi POST |
| api/contracts | DTO/error code/version hợp đồng | Contract đã review | Định nghĩa enum khác BE |
| api/error-mapper | Lỗi API → UX nhất quán | error.code/details → form/banner | Parse câu message để quyết nghiệp vụ |
| media/upload-client | Upload init/progress/complete | File + purpose → asset READY | Báo Submission thành công |
| shared/form-field | Label/input/help/error/focus | Value/errors → giao diện truy cập được | Luật số ảnh cố định |
| shared/async-state | Loading/empty/error/retry state | Query state → view | Nuốt lỗi thành mảng rỗng |
| shared/data-table | Sort/filter/pagination được phép | DTO list + controls | Export vượt quyền |
| shared/confirmation | Xác nhận thao tác nhạy cảm | Intent → confirm/cancel | Thay quyền server |
| shared/media-viewer | Xem ảnh được cấp quyền, xử lý link hết hạn | URL/metadata an toàn → ảnh | Lộ raw evidence |
| tests/fixtures | DTO giả đúng contract | Scenario → fixture | Dữ liệu private hoặc secret |

## 3. Cấu trúc feature

| Nhóm file mỗi feature | Nội dung cần viết |
|---|---|
| page/container | Load dữ liệu, nối API và điều phối thao tác |
| view/components | Hiển thị, nhận callback; không tự quyết công thức |
| form/schema | Form validation cho UX, dữ liệu bắt buộc từ policy |
| queries/mutations | Endpoint đúng A-code, cache scope, version và invalidate |
| state | Draft, pending, uncertain, conflict; tránh nhiều nguồn sự thật |
| permissions | Điều kiện hiển thị theo capabilities đã nhận |
| tests | Happy/unhappy, quyền, lỗi mạng và giữ dữ liệu |

## 4. Feature đầy đủ

| Feature | Owner chính | FR | Phối hợp BE |
|---|---|---|---|
| auth/profile | TV3; Admin do TV1 | 01/17 | A01..04/A56 |
| participant/contests-enrollments | TV3 | 03/04 | A12/A14/A21/A22 |
| participant/film | TV3 | 05 | A08/A25..28 |
| participant/submission | TV3 | 06/07/17 | A29..34 |
| participant/portfolio-results | TV3 | 11/17 | A46/A56..58 |
| organizer/contest-template | TV4 | 03 | A13..20 |
| organizer/review | TV4 | 04/07/08/13/14 | A23/A24/A34..37/A50..52 |
| organizer/judging-results | TV4 | 09..11 | A38/A43..45 |
| judge/scoring | TV4 | 09/10 | A39..42 |
| archive-exhibition | TV4 + TV3 | 12 | A47..49 |
| analytics | TV5 + TV4 | 16 | A54..55 |
| admin | TV1 | 02 | A05..11/A59 |
| notifications | Owner theo phần FR-18 được duyệt | 18 | A60 có điều kiện |

## 5. Dữ liệu và ranh giới

Form draft nằm local của màn hình; dữ liệu server có key theo user+contest+round/resource. Logout/đổi người dùng phải xóa dữ liệu private khỏi cache. Hai tab không chia sẻ bản nháp điểm nhầm nhau. Không cache trang private qua static/public cache.

Happy: trang scoring tải đúng assignment và rubric, đổi vòng tải đúng query key.
Unhappy: đổi tài khoản cùng trình duyệt → không hiển thị ảnh/phiếu của tài khoản trước.
Jira: [GC-46](https://0703032147khang.atlassian.net/browse/GC-46), [GC-52](https://0703032147khang.atlassian.net/browse/GC-52).
Test: [E2E](../Docs/05-quality/e2e-test-cases.md). Actual: NOT RUN.
