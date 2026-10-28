# DEC-01 — Open Questions / Decision Log (phải hỏi/duyệt)

> Danh sách câu hỏi **chưa có câu trả lời chính thức trong hai tài liệu nguồn**. Không được tự biến câu trả lời đề xuất thành một “requirement thầy giao”. Mỗi quyết định cập nhật file này + FR liên quan.

| ID | Quyết định cần chốt | Tác động | Phương án đề xuất để thảo luận | Owner | Trạng thái |
|---|---|---|---|---|---|
| D-01 | Mobile Flutter bắt buộc hay Web responsive đủ? | Scope, TV3, estimate | Ưu tiên Web responsive; hỏi GV trước khi bỏ Mobile | TV1 | TBD |
| D-02 | Số bài tối đa mỗi cuộc thi/hạng mục? Có tái sử dụng frame không? | Submission unique/DB | Cấu hình theo ContestRule, chưa cố định số | TV1/TV4 | TBD |
| D-03 | Enrollment auto-approve hay organizer duyệt? | FR-04 flow | Cho cờ `requires_approval` | TV4 | TBD |
| D-04 | Loại file/cỡ ảnh tối đa/tối thiểu? | FR-06/07, storage quota | JPEG trước, TIFF cân nhắc; giới hạn theo rule | TV2/TV3 | TBD |
| D-05 | Ít nhất mấy Judge/vòng, multi-round đến đâu? | FR-09/10/11 | MVP một round chuẩn, thêm multi-round nếu đủ giờ | TV4 | TBD |
| D-06 | Công thức tổng hợp điểm, hòa điểm, thiếu phiếu? | FR-10/11 | Trung bình có trọng số, tie-break cần quy chế | TV2/TV4 | TBD |
| D-07 | Negative/contact sheet lúc nào bắt buộc? | FR-05/06/08 | Mặc định tùy chọn, contest có thể yêu cầu | TV1 | TBD |
| D-08 | Ai cho phép công khai và lưu bao lâu? | Archive/AI privacy | Quyền công bố riêng; retention do GV/nhóm duyệt | TV1/TV3 | TBD |
| D-09 | AI-generated detector có phải demo chạy thực tế? | AI nghiên cứu, chi phí | Làm PoC, chỉ cảnh báo, fallback manual | TV5 | TBD |
| D-10 | Hạn nộp chính thức môn học? | Sprint 4 / cut scope | Không xem 06/12 là được xác nhận nếu GV chưa duyệt | TV1 | TBD |
| D-11 | Backend cuối cùng Express hay ASP.NET Core? | README/api setup | Theo năng lực TV2, chốt trước code sâu | TV2 | TBD |
| D-12 | Tên chính thức và link Jira/GitHub repo? | Traceability | Điền sau khi tạo dự án | TV1 | TBD |

## Quy trình đóng quyết định
1. Ghi bằng chứng từ giảng viên/nhóm (buổi học/tin nhắn), người xác nhận, thời gian.
2. Cập nhật ô trạng thái `APPROVED / REJECTED / TBD`.
3. Sửa cùng PR: FR, BR, Entity, AC/TC, scope/hours và Story liên quan.
4. Không “xóa câu hỏi khỏi lịch sử”; lưu lý do ở change log.

## B. Quyết định bổ sung sau phản biện nghiệp vụ V3

> Toàn bộ dòng sau là **đề nghị thảo luận**, KHÔNG có nghĩa thầy đã duyệt. Cần chốt **trước** khi code transition/score/recovery liên quan.

| ID | Câu hỏi nghiệp vụ phải quyết định | Đề xuất cho MVP | Tác động đến FR/Entity | Owner | Trạng thái |
|---|---|---|---|---|---|
| D-13 | Có cho sửa/nộp lại sau yêu cầu bổ sung khi deadline đã qua? | Mặc định không, chỉ cho nếu rule contest ghi rõ ngoại lệ | FR-06/08; Submission, ReviewDecision | TV1+TV4 | TBD |
| D-14 | Nếu upload trước hạn nhưng finalize sau hạn, tính giờ nào? | Theo giờ Backend finalize, không bảo lưu upload start | FR-06; submitted_at | TV1+TV2 | TBD |
| D-15 | Hủy Enrollment khi đã có Submission xử lý ra sao? | Chặn tự hủy hoặc bắt buộc Organizer xét; không cascade xóa | FR-04/06; Enrollment | TV1+TV4 | TBD |
| D-16 | Quyền mở lại bài APPROVED hoặc FINALIZED, khi đã chấm/công bố? | Incident/correction workflow, không sửa lịch sử tại chỗ | FR-08/10/11; ReviewDecision/RankingSnapshot | TV1+TV4 | TBD |
| D-17 | Retry và thời gian giữ upload tạm? | Idempotent request + cleanup job; thời gian grace TBD | FR-06; UploadSession/MediaAsset | TV2 | TBD |
| D-18 | Công khai portfolio/ảnh đạt giải có cơ chế consent/rút quyền? | Mặc định private evidence, ảnh public khi có quyền | FR-12/17; ArchiveEntry/Consent | TV1+TV3 | TBD |
| D-19 | Khi AI tạm lỗi, có cho review thủ công tiếp tục? | Cho phép manual nếu không vi phạm rule bắt buộc; UI ghi AI unavailable | FR-07/08/14; AIJob | TV5+TV4 | TBD |
| D-20 | Khi có xung đột giữa hai Organizer/Judge sửa cùng lúc, policy? | Dùng version lock, trả 409 và yêu cầu đọc lại | FR-08/10; ScoreSheet/ReviewDecision | TV2+TV4 | TBD |

**Cách ghi nhận quyết định đã chốt**: người xác nhận, nguồn bằng chứng, thời gian, phương án được chọn, ảnh hưởng scope/giờ, PR sửa `Docs/`. Nếu nhóm chưa hỏi kịp trước giờ kiểm tra, nói rõ thầy đang được mời xác nhận chính sách; không đặt ra số giả làm quy định chính thức.
