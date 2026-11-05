# FE — Chi tiết luồng thí sinh

> **ID:** FE-03 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Hoàng Duy Khánh · **Review:** Bùi Đức Hưng và owner liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa chốt. Các quy trình kỹ thuật là [D]; quyền/ngưỡng/thể lệ chưa xác nhận không được tự áp dụng. Chưa có bằng chứng ứng dụng chạy.

## 1. Hồ sơ và portfolio — SCR06..08

- Đọc A56 để hiển thị hồ sơ chính mình; form chỉ có field được schema cho phép.
- Update giữ input nếu lỗi; không cho field vai trò hoặc trạng thái tài khoản đi cùng request.
- Portfolio A57 có danh sách tác phẩm; model và cách thêm tác phẩm cần chốt với BE, không mặc định chỉ ảnh đã đoạt giải.
- Public/private, quyền rút và lịch sử là D-08/D-18; chưa chốt thì prototype phải ghi TBD, không tự public.
- Lịch sử A58/A46 nối cuộc thi, bài, kết quả và feedback theo quyền; không hiển thị điểm nháp của hội đồng.

## 2. Chọn cuộc thi và đăng ký — SCR03..05

A12 danh sách → A14 chi tiết thể lệ/hạng mục/lịch → A21 đăng ký người hiện tại → A22 trạng thái.
D-03 quyết định cần duyệt hay tự chấp thuận. UI không mặc định REQUESTED luôn được nộp bài, không tự cấp APPROVED. Hết phiên/đóng đăng ký/đã đăng ký phải có lỗi rõ và giữ lựa chọn cuộc thi khi an toàn.

Thời gian hiển thị có múi giờ; timezone cuộc thi cần chốt. Đồng hồ đếm ngược trên máy người dùng chỉ tham khảo, không quyết định hợp lệ.

## 3. Cuộn phim và khung hình — SCR09..10

| Form | Dữ liệu dự kiến | Source và lưu ý |
|---|---|---|
| Roll | film stock, ISO, camera, lens, format, lab | A08/A25..26; required theo rule đã duyệt |
| Frame | frame_number, notes, scan_info, location | A27..28; field location cần bổ sung model có review |
| Source/evidence | Ảnh minh chứng và mô tả | Contract liên kết chưa chốt; mặc định đề tài là tùy chọn |

Không ép enum, ISO, số frame hoặc tất cả field bắt buộc theo sở thích UI. Roll/frame ngoài quyền phải bị BE chặn. Nếu metadata đã dùng để nộp, quyền sửa và tác động snapshot cần được giải thích theo policy; không hứa sửa tự do.

## 4. Wizard nộp bài — SCR11

**Wizard** là biểu mẫu chia bước để người dùng dễ theo dõi; việc chọn wizard là [D].

| Bước | Nội dung UI | API / điều kiện | Kết quả được phép thông báo |
|---|---|---|---|
| 1 | Chọn contest/category và xem rule | A14/A22 | Đủ/chưa đủ điều kiện theo server |
| 2 | Chọn roll/frame và xem metadata | A25..28 | Frame đã chọn, thiếu gì cần bổ sung |
| 3 | Chọn scan/evidence | Validation UX theo cấu hình | Chưa upload; không gọi đã nộp |
| 4 | Truyền tệp | A29 → upload → A30 | Đang truyền/READY hoặc lỗi |
| 5 | Xem lại hồ sơ và Gửi | A31 + request key | Đang gửi/chưa rõ kết quả |
| 6 | Xác nhận | BE trả submission_id/status | “Đã gửi bài”, đúng trạng thái server |

Đơn vị bài một ảnh hay nhiều ảnh còn D-02; UI phải có điểm mở cho quyết định đó, không chốt chỉ một slot như phạm vi cuối. Evidence upload có purpose riêng, không tự public cùng scan.

## 5. Bất thường và phục hồi

| Tình huống | UI cần làm | Không làm |
|---|---|---|
| File không đúng rule | Hiện lỗi theo field và cấu hình hiện hành | Hard-code 10MB/JPEG thành thể lệ |
| Upload mạng lỗi | Giữ metadata/lựa chọn, retry phần tệp theo session còn hiệu lực | Xóa toàn bộ form |
| Tệp READY nhưng A31 lỗi rule | Hiện lý do; cho điều chỉnh hợp lệ | Coi READY là Submission |
| Timeout A31 | Giữ request key, tra cứu bài/trạng thái theo BE contract trước gửi tiếp | Sinh key mới và tạo bài thứ hai |
| Server trả CONTEST_CLOSED | Hiện mốc/theo policy D-14, giữ input nếu phù hợp | Hứa bảo lưu vì upload trước hạn |
| Phiên hết hạn | Tái xác thực theo policy; đọc lại quyền trước tiếp tục | Lưu token/ảnh private vào kho công khai |
| Organizer yêu cầu bổ sung | Chỉ mở thao tác nếu D-13/quy chế cho phép | Tự cho sửa sau deadline |
| Frame khác owner | Thông báo không truy cập được, yêu cầu chọn lại | Hiển thị chi tiết người sở hữu |

Trong catalog A-code hiện chưa có endpoint riêng tra cứu theo request key. Trước code phải bổ sung vào A32 theo query được review hoặc thiết kế operation riêng; không tạo URL query tự phát ở FE. Đây là gap hợp đồng, không phải API đã tồn tại.

## 6. Theo dõi và kết quả — SCR12..14

A32/A33 đọc trạng thái, A34 đọc checks theo quyền, A46 xem kết quả/feedback đã được phép. Phân biệt kiểm tra thể lệ fail với AI unavailable, PENDING_REVIEW với REJECTED; không gọi hai trạng thái là một.

Refresh/polling theo interval/hạn mức được nhóm chọn, chưa có số giây bắt buộc. URL ảnh hết hạn cần xin lại qua BE sau kiểm quyền, không đổi object bucket sang public.

## 7. Ví dụ kiểm tra

Happy: Participant đăng ký hợp lệ → chọn frame của mình → scan READY → A31 xác nhận ID → SCR13 hiển thị đúng bài.
Unhappy: A31 commit nhưng response mất → màn hình “Đang xác minh kết quả gửi”; đọc lại được đúng ID, không tạo duplicate.
Điểm review: field/required khớp DTO, input giữ khi lỗi, không lộ evidence, thao tác sửa chỉ khi policy duyệt.
FR-04/05/06/17; Entity FilmRoll/FilmFrame/MediaAsset/Submission/Enrollment.
Jira: [GC-49](https://0703032147khang.atlassian.net/browse/GC-49), [GC-57](https://0703032147khang.atlassian.net/browse/GC-57), [GC-72](https://0703032147khang.atlassian.net/browse/GC-72).
Test: [Business Scenarios](../Docs/05-quality/business-scenario-tests.md). Actual: NOT RUN.
