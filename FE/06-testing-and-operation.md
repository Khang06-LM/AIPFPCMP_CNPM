# FE — Kiểm thử, hướng dẫn chạy và bằng chứng

> **ID:** FE-06 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Hoàng Duy Khánh; Nguyễn Khánh Hiền; cả nhóm · **Review:** Bùi Đức Hưng và owner liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa chốt. Các quy trình kỹ thuật là [D]; quyền/ngưỡng/thể lệ chưa xác nhận không được tự áp dụng. Chưa có bằng chứng ứng dụng chạy.

## 1. Kế hoạch kiểm tra

| Nhóm | Tình huống | Expected |
|---|---|---|
| Form | Required/range/missing field theo schema | Error đúng trường, giữ input |
| Session | Load trang private khi chưa có phiên | Không flash ảnh/điểm cache cũ |
| Permission | Biết ID bài/phiếu ngoài quyền | UI xử lý denial, BE không lộ dữ liệu |
| Upload | 100% nhưng finalize chưa ACK | Chưa hiển thị “đã nộp” |
| Recovery | Commit xong nhưng mất response | Lookup, không tạo hai bài/phiếu |
| Concurrency | Hai tab sửa cùng phiếu | Conflict; draft còn để đối chiếu |
| AI | Score cảnh báo hoặc unavailable | Tín hiệu hỗ trợ, không kết luận ảnh giả |
| Publication | Điểm thiếu/đồng điểm chưa chốt | Hiện blocker, không tự chọn người thắng |
| Cache | Đổi user/contest/round lúc request đang chạy | Không lẫn dữ liệu scope cũ |
| Accessibility | Tab/keyboard/focus/labels/error | Thao tác rõ, không chỉ màu |
| Responsive | Màn hình nhỏ/lớn và ảnh | Form/nút không mất; phạm vi Mobile theo D-01 |
| E2E | 4 vai trò/5 Core Flow + admin/portfolio/report | Đủ hành trình và evidence |

Kích thước viewport/trình duyệt mục tiêu và chỉ tiêu đo TBD; không mặc định test responsive chứng minh đã có Flutter. Automated UI test là lựa chọn công cụ cần chốt, không tự loại vì mẫu khác làm thủ công.

## 2. Traceability và evidence

Mỗi run có SCR/FR/TC/Jira, commit/contract version, browser/device/viewport, môi trường/seed, steps, expected/actual, PASS/FAIL/BLOCKED/NOT RUN, screenshot/log an toàn, bug và người chạy.

Không chụp token/key/ảnh private; chỉ dùng dữ liệu có quyền. Không dùng screenshot của mẫu làm sản phẩm nhóm. Wireframe/prototype phải có nhãn và không được ghi test live PASS.

Nguồn kiểm tra: [Test Matrix](../Docs/05-quality/test-matrix.md), [E2E](../Docs/05-quality/e2e-test-cases.md), [Business Scenarios](../Docs/05-quality/business-scenario-tests.md).

## 3. Cài đặt và cấu hình

Chưa có source/package manifest/stack đã chọn, nên chưa có command install/dev/build/test được xác minh.
Khi hiện thực, bổ sung: runtime/version, package manager+lock, cấu hình API origin/session, lệnh install/dev/build/test, mock mode, start production, routes và cách nối BE/AI.

Nếu chọn Next.js, biến public như NEXT_PUBLIC_API_BASE_URL chỉ chứa endpoint công khai, không credential. Tên này phụ thuộc framework và là đề xuất; chưa tạo .env.example/code giả. Chạy trên máy sạch phải có người khác làm theo được.

## 4. Hướng dẫn người dùng sau này

User Manual theo vai trò, ảnh thật của đúng release, entry conditions, từng bước, kết quả mong đợi, lỗi thường gặp và giới hạn. Bao phủ đăng ký/phim/nộp/kết quả; cấu hình/duyệt/phân công/chấm/công bố; admin; archive/report.
Tài liệu thiết kế hiện tại không được gọi hướng dẫn sử dụng sản phẩm đã phát hành.

## 5. Gate bàn giao

- Contract/BR được duyệt; endpoint thật khớp DTO; không còn mock trộn trong live flow.
- Có code/commit/build thật; lệnh thực hiện tái lập.
- Trạng thái test trung thực, bug/known issues có link; không điền sẵn 100% PASS.
- Deploy cần URL thật, session/origin đúng môi trường và smoke qua BE; không bịa link staging.
- Chưa chốt Mobile, chấm, metadata/limits hoặc public quyền thì ghi blocker cho phần tương ứng.

Happy: người kiểm tra khác làm theo user guide, dùng dữ liệu giả để gửi/chấm/công bố đúng release.
Unhappy: BE unavailable → hiển thị lỗi kết nối và giữ input, không lấy mock để báo đã xong.
Jira: [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-71](https://0703032147khang.atlassian.net/browse/GC-71), [GC-72](https://0703032147khang.atlassian.net/browse/GC-72), [GC-73](https://0703032147khang.atlassian.net/browse/GC-73).
Trạng thái test/deploy ứng dụng: **NOT RUN / NOT DEPLOYED trong bộ tài liệu này**; không suy ra tình trạng các repo/nhánh chưa kiểm tra.
