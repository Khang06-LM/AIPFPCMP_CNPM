# TEST-01 — Test Scenario Matrix (Design, chưa chạy)

> **Trạng thái tất cả test:** NOT RUN. Điều này phân biệt **đã phân tích happy/unhappy** với **đã thực sự kiểm thử thành công**. Chi tiết AC và dữ liệu tiền điều kiện nằm trong file FR tương ứng.

| Test ID | FR | Nhóm | Trigger/input | Expected | Execution |
| --- | --- | --- | --- | --- | --- |
| TC-FR-01-01 | FR-01 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-01-02 | FR-01 | Unhappy / alternate | Email không tồn tại/sai mật khẩu | Không tạo phiên. | NOT RUN |
| TC-FR-01-03 | FR-01 | Unhappy / alternate | Tài khoản bị khóa | Không phát token. | NOT RUN |
| TC-FR-01-04 | FR-01 | Unhappy / alternate | Token hết hạn | Không ghi dữ liệu. | NOT RUN |
| TC-FR-01-05 | FR-01 | Unhappy / alternate | Participant gọi API Admin | Không đổi dữ liệu. | NOT RUN |
| TC-FR-01-06 | FR-01 | Unhappy / alternate | User có nhiều vai trò | Quyền đúng tập role được duyệt. | NOT RUN |
| TC-FR-02-01 | FR-02 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-02-02 | FR-02 | Unhappy / alternate | Tên danh mục đã tồn tại | Không tạo bản ghi lặp. | NOT RUN |
| TC-FR-02-03 | FR-02 | Unhappy / alternate | Khóa user đang đăng nhập | Không tiếp tục thao tác đặc quyền. | NOT RUN |
| TC-FR-02-04 | FR-02 | Unhappy / alternate | Xóa film stock đang được dùng | Không mất lịch sử metadata. | NOT RUN |
| TC-FR-02-05 | FR-02 | Unhappy / alternate | Admin thiếu quyền thao tác | Không cập nhật. | NOT RUN |
| TC-FR-02-06 | FR-02 | Unhappy / alternate | Lỗi cơ sở dữ liệu khi ghi audit | Không báo thành công giả. | NOT RUN |
| TC-FR-03-01 | FR-03 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-03-02 | FR-03 | Unhappy / alternate | Ngày đóng trước ngày mở | Không tạo lịch phi logic. | NOT RUN |
| TC-FR-03-03 | FR-03 | Unhappy / alternate | Tổng trọng số không hợp lệ | Không chấm bằng cấu hình sai. | NOT RUN |
| TC-FR-03-04 | FR-03 | Unhappy / alternate | Thiếu hạng mục | Contest ở DRAFT. | NOT RUN |
| TC-FR-03-05 | FR-03 | Unhappy / alternate | Sửa tiêu chí sau khi đã chấm | Dữ liệu chấm lịch sử nhất quán. | NOT RUN |
| TC-FR-03-06 | FR-03 | Unhappy / alternate | Organizer sửa cuộc thi không thuộc quyền | Không thay đổi bản ghi. | NOT RUN |
| TC-FR-04-01 | FR-04 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-04-02 | FR-04 | Unhappy / alternate | Đã đăng ký cuộc thi | Idempotent. | NOT RUN |
| TC-FR-04-03 | FR-04 | Unhappy / alternate | Cuộc thi hết hạn đăng ký | Trạng thái không đổi. | NOT RUN |
| TC-FR-04-04 | FR-04 | Unhappy / alternate | Tài khoản bị khóa | Không có Enrollment. | NOT RUN |
| TC-FR-04-05 | FR-04 | Unhappy / alternate | Organizer từ chối đăng ký | Không cho nộp bài. | NOT RUN |
| TC-FR-04-06 | FR-04 | Unhappy / alternate | Nhiều request cùng lúc | Chỉ một Enrollment. | NOT RUN |
| TC-FR-05-01 | FR-05 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-05-02 | FR-05 | Unhappy / alternate | Thiếu film stock bắt buộc | Không tạo Roll. | NOT RUN |
| TC-FR-05-03 | FR-05 | Unhappy / alternate | Frame number trùng trong Roll | Không phát sinh Frame thứ hai. | NOT RUN |
| TC-FR-05-04 | FR-05 | Unhappy / alternate | Participant sửa Roll của người khác | Không đổi dữ liệu. | NOT RUN |
| TC-FR-05-05 | FR-05 | Unhappy / alternate | Lab không có trong danh mục | Không tự tạo master data trái quyền. | NOT RUN |
| TC-FR-05-06 | FR-05 | Unhappy / alternate | Sửa metadata sau khi nộp | Submission không bị mất nguồn gốc. | NOT RUN |
| TC-FR-06-01 | FR-06 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-06-02 | FR-06 | Unhappy / alternate | Upload thành công nhưng chốt nộp trễ | Không có Submission được duyệt. | NOT RUN |
| TC-FR-06-03 | FR-06 | Unhappy / alternate | File sai MIME/không giải mã được | Không lưu ảnh độc hại. | NOT RUN |
| TC-FR-06-04 | FR-06 | Unhappy / alternate | File vượt dung lượng | Không tạo Submission. | NOT RUN |
| TC-FR-06-05 | FR-06 | Unhappy / alternate | Network ngắt giữa upload | Không tạo bài nộp trùng. | NOT RUN |
| TC-FR-06-06 | FR-06 | Unhappy / alternate | Frame của người khác / enrollment không hợp lệ | Không tạo liên kết sai quyền. | NOT RUN |
| TC-FR-06-07 | FR-06 | Unhappy / alternate | Participant bấm Gửi 2 lần | Một lần chốt hợp lệ chỉ một bài. | NOT RUN |
| TC-FR-06-08 | FR-06 | Unhappy / alternate | Storage thành công nhưng database lỗi | Không báo hoàn tất giả. | NOT RUN |
| TC-FR-07-01 | FR-07 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-07-02 | FR-07 | Unhappy / alternate | Thiếu trường metadata bắt buộc | Không gửi chấm. | NOT RUN |
| TC-FR-07-03 | FR-07 | Unhappy / alternate | File object không tồn tại | Không tự loại thí sinh. | NOT RUN |
| TC-FR-07-04 | FR-07 | Unhappy / alternate | Quy tắc contest đổi sau nộp | Kết quả tái lập. | NOT RUN |
| TC-FR-07-05 | FR-07 | Unhappy / alternate | Job chạy lặp | Không nhân đôi report bất hợp lý. | NOT RUN |
| TC-FR-07-06 | FR-07 | Unhappy / alternate | AI service timeout | Không chặn vô hạn. | NOT RUN |
| TC-FR-08-01 | FR-08 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-08-02 | FR-08 | Unhappy / alternate | Bài đã duyệt bởi Organizer khác | Không ghi đè quyết định. | NOT RUN |
| TC-FR-08-03 | FR-08 | Unhappy / alternate | Chọn Reject không nhập lý do | Có đủ audit. | NOT RUN |
| TC-FR-08-04 | FR-08 | Unhappy / alternate | Nhận AI flag nhưng ảnh vẫn hợp lệ | Không auto reject. | NOT RUN |
| TC-FR-08-05 | FR-08 | Unhappy / alternate | Không tìm được file minh chứng | Không loại nhầm. | NOT RUN |
| TC-FR-08-06 | FR-08 | Unhappy / alternate | Participant nộp lại sau yêu cầu sửa | Bảo toàn quyết định cũ. | NOT RUN |
| TC-FR-09-01 | FR-09 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-09-02 | FR-09 | Unhappy / alternate | Gán người không có vai trò Judge | Không tạo assignment. | NOT RUN |
| TC-FR-09-03 | FR-09 | Unhappy / alternate | Gán trùng | Không tạo bản ghi trùng. | NOT RUN |
| TC-FR-09-04 | FR-09 | Unhappy / alternate | Thu hồi phân công khi đã có điểm | Điểm lịch sử không mất. | NOT RUN |
| TC-FR-09-05 | FR-09 | Unhappy / alternate | Judge vào link bài không được gán | Không lộ nội dung. | NOT RUN |
| TC-FR-09-06 | FR-09 | Unhappy / alternate | Hạng mục chưa có bài được duyệt | Không phát sinh quyền sai. | NOT RUN |
| TC-FR-10-01 | FR-10 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-10-02 | FR-10 | Unhappy / alternate | Điểm vượt thang | Không lưu điểm không hợp lệ. | NOT RUN |
| TC-FR-10-03 | FR-10 | Unhappy / alternate | Thiếu một tiêu chí khi chốt | Không đưa vào tổng kết. | NOT RUN |
| TC-FR-10-04 | FR-10 | Unhappy / alternate | Judge chấm bài không được gán | Không ghi ScoreSheet. | NOT RUN |
| TC-FR-10-05 | FR-10 | Unhappy / alternate | Gửi 2 request chốt liên tiếp | Một trạng thái Finalized. | NOT RUN |
| TC-FR-10-06 | FR-10 | Unhappy / alternate | Organizer đổi trọng số sau khi có điểm | Có thể tái lập điểm. | NOT RUN |
| TC-FR-10-07 | FR-10 | Unhappy / alternate | Judge cố sửa sau Finalized | Bảo toàn kết quả. | NOT RUN |
| TC-FR-11-01 | FR-11 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-11-02 | FR-11 | Unhappy / alternate | Thiếu phiếu một giám khảo | Không tuyên bố kết quả thiếu công bằng. | NOT RUN |
| TC-FR-11-03 | FR-11 | Unhappy / alternate | Hai ảnh đồng điểm | Không sắp xếp ngẫu nhiên. | NOT RUN |
| TC-FR-11-04 | FR-11 | Unhappy / alternate | Điểm chưa chốt | Không cộng điểm nháp. | NOT RUN |
| TC-FR-11-05 | FR-11 | Unhappy / alternate | Hai Organizer cùng công bố | Một snapshot chính thức. | NOT RUN |
| TC-FR-11-06 | FR-11 | Unhappy / alternate | Có yêu cầu sửa kết quả sau công bố | Có dấu vết kiểm tra. | NOT RUN |
| TC-FR-12-01 | FR-12 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-12-02 | FR-12 | Unhappy / alternate | Tác phẩm chưa đạt giải | Không hiển thị nhầm. | NOT RUN |
| TC-FR-12-03 | FR-12 | Unhappy / alternate | Chưa có quyền công khai | Không vi phạm quyền riêng tư. | NOT RUN |
| TC-FR-12-04 | FR-12 | Unhappy / alternate | Ảnh gốc bị mất trên storage | Không làm hỏng toàn bộ trang. | NOT RUN |
| TC-FR-12-05 | FR-12 | Unhappy / alternate | Truy vấn tìm kiếm không có kết quả | Có trải nghiệm rõ. | NOT RUN |
| TC-FR-12-06 | FR-12 | Unhappy / alternate | Metadata riêng tư bị gọi trực tiếp | Không lộ PII. | NOT RUN |
| TC-FR-13-01 | FR-13 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-13-02 | FR-13 | Unhappy / alternate | Ảnh trùng byte hoàn toàn | Có cặp đối chứng. | NOT RUN |
| TC-FR-13-03 | FR-13 | Unhappy / alternate | Ảnh resize/nén nhẹ | Có score, không tự loại. | NOT RUN |
| TC-FR-13-04 | FR-13 | Unhappy / alternate | Ảnh crop nặng/lật | Không khẳng định sạch. | NOT RUN |
| TC-FR-13-05 | FR-13 | Unhappy / alternate | AI job timeout | Không chặn vĩnh viễn duyệt. | NOT RUN |
| TC-FR-13-06 | FR-13 | Unhappy / alternate | Hai ảnh rất giống nhưng hai khung khác nhau | Không tự reject. | NOT RUN |
| TC-FR-14-01 | FR-14 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-14-02 | FR-14 | Unhappy / alternate | Model dự đoán nhầm ảnh phim là AI | Không loại oan tự động. | NOT RUN |
| TC-FR-14-03 | FR-14 | Unhappy / alternate | Ảnh AI bị dự đoán là thật | Giới hạn công khai. | NOT RUN |
| TC-FR-14-04 | FR-14 | Unhappy / alternate | Không có model/API/quota | Luồng chính tiếp tục. | NOT RUN |
| TC-FR-14-05 | FR-14 | Unhappy / alternate | Model chưa được benchmark | Không lạm dụng kết quả. | NOT RUN |
| TC-FR-14-06 | FR-14 | Unhappy / alternate | Ảnh có thông tin riêng tư | Bảo vệ dữ liệu. | NOT RUN |
| TC-FR-15-01 | FR-15 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-15-02 | FR-15 | Unhappy / alternate | AI tạo tag không liên quan | Không khóa người dùng theo AI. | NOT RUN |
| TC-FR-15-03 | FR-15 | Unhappy / alternate | Tag trùng ngôn ngữ khác | Tìm kiếm ổn định. | NOT RUN |
| TC-FR-15-04 | FR-15 | Unhappy / alternate | Không có tag | Không chặn xuất bản. | NOT RUN |
| TC-FR-15-05 | FR-15 | Unhappy / alternate | API trả nội dung không an toàn | Không hiển thị trực tiếp. | NOT RUN |
| TC-FR-15-06 | FR-15 | Unhappy / alternate | Job thất bại | Luồng nộp bài bình thường. | NOT RUN |
| TC-FR-16-01 | FR-16 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-16-02 | FR-16 | Unhappy / alternate | Không có dữ liệu | Trang không lỗi. | NOT RUN |
| TC-FR-16-03 | FR-16 | Unhappy / alternate | Khoảng thời gian ngược | Không query vô nghĩa. | NOT RUN |
| TC-FR-16-04 | FR-16 | Unhappy / alternate | Contest không thuộc Organizer | Không lộ số liệu. | NOT RUN |
| TC-FR-16-05 | FR-16 | Unhappy / alternate | Bài nộp lặp do retry | KPI không sai. | NOT RUN |
| TC-FR-16-06 | FR-16 | Unhappy / alternate | AI chưa trả kết quả | Không đánh tráo trạng thái. | NOT RUN |
| TC-FR-17-01 | FR-17 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-17-02 | FR-17 | Unhappy / alternate | Chưa tham gia cuộc thi | Không lỗi 500. | NOT RUN |
| TC-FR-17-03 | FR-17 | Unhappy / alternate | Submission bị từ chối | Không hiển thị điểm chưa chấm. | NOT RUN |
| TC-FR-17-04 | FR-17 | Unhappy / alternate | Đã có kết quả nhưng chưa publish | Không rò kết quả. | NOT RUN |
| TC-FR-17-05 | FR-17 | Unhappy / alternate | Truy cập /users/{id} của người khác | Không lộ PII. | NOT RUN |
| TC-FR-17-06 | FR-17 | Unhappy / alternate | Ảnh archive bị gỡ quyền công khai | Tôn trọng consent. | NOT RUN |
| TC-FR-18-01 | FR-18 | Happy | Điều kiện đầu đáp ứng; thực hiện đầy đủ main flow | Thao tác thành công, bản ghi đúng quyền, trạng thái hợp lệ | NOT RUN |
| TC-FR-18-02 | FR-18 | Unhappy / alternate | Gửi notification thất bại | Luồng chính không bị chặn. | NOT RUN |
| TC-FR-18-03 | FR-18 | Unhappy / alternate | Người dùng không còn quyền truy cập object | Không lộ dữ liệu. | NOT RUN |
| TC-FR-18-04 | FR-18 | Unhappy / alternate | Sự kiện bị gửi lại | Không spam. | NOT RUN |
| TC-FR-18-05 | FR-18 | Unhappy / alternate | Người dùng đánh dấu đã đọc nhiều lần | Không lỗi. | NOT RUN |
| TC-FR-18-06 | FR-18 | Unhappy / alternate | Thông báo kết quả trước thời gian publish | Không lộ kết quả. | NOT RUN |

## 2. Test execution record (phải điền sau khi chạy)

```markdown
- TC ID:
- Tester / date:
- Commit/tag / environment URL:
- Preconditions / seed IDs:
- Input thực tế:
- Expected:
- Actual:
- Result: PASS | FAIL | BLOCKED
- Evidence: screenshot / API response (đã ẩn token, email, file riêng tư)
- Bug Jira link (nếu FAIL):
```

## 3. Cách review

Một TC không có Expected kiểm tra được là **chưa đủ**. Một Expected rõ nhưng chưa có Actual/Evidence là **NOT RUN**, không phải PASS.
