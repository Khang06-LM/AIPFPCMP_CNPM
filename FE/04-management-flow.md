# FE — Chi tiết Organizer, Judge và Admin

> **ID:** FE-04 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Nguyễn Khánh Hiền; Lê Minh Khang; Đào Thị Thanh Bình · **Review:** Bùi Đức Hưng và owner liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa chốt. Các quy trình kỹ thuật là [D]; quyền/ngưỡng/thể lệ chưa xác nhận không được tự áp dụng. Chưa có bằng chứng ứng dụng chạy.

## 1. Organizer: cấu hình và mẫu — SCR15..17

A13..18 quản lý chủ đề, thể lệ, hạn nhận, category, round/lịch chấm, criteria/range/trọng số và prizes. Mỗi phần có nháp, pending save, error và conflict state. Quyền sửa cấu hình sau có bài/điểm chưa chốt; không để form thay đổi rubric đang dùng một cách im lặng.

A19..20 hỗ trợ mẫu tái sử dụng: chọn mẫu → xem cấu hình → override hợp lệ → tạo contest mới. Không tự copy thí sinh, bài, điểm hoặc kết quả mùa trước. Quyền chia sẻ mẫu và snapshot/version cần review.

A23 danh sách đăng ký; A24 quyết định chỉ khi D-03 chọn quy trình duyệt. Quản lý đăng ký không tự đồng nghĩa thẩm định tác phẩm.

## 2. Organizer: kiểm tra/duyệt bài — SCR18

| Khối giao diện | Dữ liệu | Action và điều kiện |
|---|---|---|
| Danh sách hàng đợi | A35, filter trạng thái | Chỉ contest được giao |
| Tác phẩm/metadata | A33 theo projection | Evidence chỉ người có quyền |
| Checks thể lệ | A34 pass/fail/error | Giải thích lỗi dữ liệu khác lỗi hệ thống |
| AI findings | A50..52; loại/phương pháp/version | Nhãn hỗ trợ; score chưa hiệu chuẩn không gọi xác suất |
| Lịch sử | A37 theo quyền | Không tự xóa quyết định cũ |
| Decision form | A36, reason/version | Chỉ action policy cho phép; revision có điều kiện |

AI flag không tự chọn Reject. UI không viết “phát hiện chắc chắn ảnh AI”. Quyền tiếp tục review khi AI unavailable theo D-19.

Hai Organizer mở cùng bài: request version cũ conflict → hiển thị dữ liệu mới và giữ bản nháp lý do để đối chiếu; không tự resend quyết định.

## 3. Phân công và chấm nhiều vòng — SCR19..21

Organizer chọn contest/category/round/Judge qua A38. BE kiểm role/scope/assignment; không tự suy ra Admin hay mọi Organizer được chấm. Số Judge, ẩn danh và xung đột lợi ích phải chốt.

Judge A39 chỉ thấy việc được giao → A40 phiếu/rubric → A41 lưu điểm/nhận xét → A42 chốt.
- Điểm range/criteria từ BE, không hard-code thang 10 hoặc 100.
- UI phân biệt lưu nháp và chốt; chưa có ACK không nói “đã chốt”.
- Không tự bật autosave khi chưa thiết kế version/concurrency.
- FINALIZED chỉ đọc; quyền reopen D-16 chưa chốt, không thêm nút hoạt động mặc định.
- Nếu backend xác nhận bài đang bị hậu kiểm/không còn đủ điều kiện, dừng thao tác theo policy được duyệt; không tự xóa điểm lịch sử.
- Timeout save/finalize: đọc lại A40 trước quyết định retry, giữ dữ liệu nhập và version.

## 4. Tổng hợp và công bố — SCR22..25

A43 preview gửi round/category hợp lệ, hiển thị snapshot/source version và blocker. Không tự tính bảng hạng phía FE làm kết quả chuẩn.
D-06 quyết định formula/trọng số/làm tròn/đồng điểm/điểm thiếu; không có policy thì hiển thị chưa thể xác nhận, không tự chọn người thắng.

A44 publish cần xác nhận snapshot/version. Thành công chỉ khi server ACK; timeout đọc lại A45 trước bấm tiếp. A45 chỉ công bố trường được cho phép, không lấy phiếu nháp vào trang public.

A49 quản lý entry triển lãm và visibility; A47/A48 cho người xem theo quyền. Không public negative/contact sheet cùng metadata. Cơ chế đính chính/consent/xóa/lưu dài hạn D-08/16/18 chưa chốt.

## 5. Analytics — SCR26

A54 metric định nghĩa rõ, scope/filter/mốc thời gian; A55 export có trạng thái xử lý/lỗi/link hết hạn. Đổi filter phải đổi query/export params tương ứng. Không export toàn hệ thống từ quyền một contest. Phân tích AI ghi rõ nhận định, không tự bịa số liệu.
Khối UI này do TV5 thiết kế dữ liệu phối hợp TV4.

## 6. Admin — SCR27..30

| Chức năng | API | Kiểm tra UI |
|---|---|---|
| Users/roles | A05..07 | Field whitelist, xác nhận thao tác nhạy cảm, refetch quyền sau thay đổi |
| Danh mục | A08..09 | Trường/uniqueness theo BE; chính sách ngừng dùng chưa chốt |
| Settings | A10 | Không nhập/hiển thị credentials cloud hoặc JWT signing key |
| Monitoring | A11 | Chỉ số đã thống nhất; empty/error khác số 0 |
| Audit | A59 | Filter/phân trang, không lộ secrets hoặc ảnh private |

Admin không mặc định sửa bài, chấm thay Judge hay thay kết quả của Organizer. Thông báo in-app A60 chỉ nếu phần FR-18 được duyệt.

## 7. Expected kiểm tra

Happy: assignment đúng → Judge chấm/chốt → Organizer preview hợp lệ → publish → archive hiển thị đúng dữ liệu được phép.
Unhappy: phiếu chưa đủ/đồng điểm chưa có policy → server trả blocker; UI giải thích rõ, không tự làm tròn/chọn thứ tự.
FR-02/03/07..16/18; Entity Contest/ReviewDecision/JudgeAssignment/ScoreSheet/RankingSnapshot/ArchiveEntry.
Jira: [GC-50](https://0703032147khang.atlassian.net/browse/GC-50), [GC-58](https://0703032147khang.atlassian.net/browse/GC-58), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60), [GC-73](https://0703032147khang.atlassian.net/browse/GC-73).
Test: [E2E](../Docs/05-quality/e2e-test-cases.md). Actual: NOT RUN.
