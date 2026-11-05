# BE — Dữ liệu và vòng đời nghiệp vụ

> **ID:** BE-03 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Bùi Đức Hưng; các owner · **Review:** owner liên quan và một thành viên khác.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Nội dung kỹ thuật là [D], chưa phải code hoặc bằng chứng kiểm thử.

## 1. Nguồn chuẩn và khoảng trống

Kế thừa [Entity](../Docs/03-domain/entities.md), [Dictionary](../Docs/03-domain/data-dictionary.md), [States](../Docs/03-domain/state-transitions.md), [Invariants](../Docs/03-domain/business-invariants.md). “MVP một ảnh/một vòng” trong tài liệu cũ chưa được duyệt; không dùng để thu hẹp phạm vi.

Template, profile/portfolio, settings, consent và bài nhiều ảnh chưa có model hoàn chỉnh trong repo. Owner cần chọn thiết kế, cập nhật Entity/ERD/DTO trước migration. Các trạng thái cũng chưa phải thể lệ đã chốt.

## 2. Dữ liệu và ràng buộc

| Nhóm | Entity | Ràng buộc thiết kế |
|---|---|---|
| Tài khoản | User/Role; UserRole nếu duyệt | Không tự cấp quyền; không lưu mật khẩu thô |
| Cuộc thi | Contest/Category/ContestRound/Criterion/Prize | Cùng contest, rubric version, nhiều vòng |
| Đăng ký | Enrollment | Người đăng ký là principal; duyệt theo D-03 |
| Nguồn ảnh | FilmRoll/FilmFrame | Roll thuộc người nộp; frame thuộc roll |
| Bài/tệp | Submission/MediaAsset; UploadSession tùy chọn | Media READY; contest/category/enrollment cùng scope |
| Thẩm định | ValidationReport/ReviewDecision | Máy khác quyết định người; giữ lịch sử |
| Chấm | JudgeAssignment/ScoreSheet/CriterionScore | Đúng assignment/round/rubric; điểm theo range |
| Công bố | RankingSnapshot/Award/ArchiveEntry | Kết quả phiên bản; công khai theo quyền |
| AI | AIJob/AIAnalysis/Tag/MediaTag | Kết quả đúng input version; không tự loại bài |
| Hỗ trợ | Notification/AuditLog; Outbox tùy chọn | Theo phần đã duyệt của FR-18; không chứa secret |

## 3. Metadata đầy đủ

| Thông tin đề tài | Vị trí đề xuất | Quyết định còn mở |
|---|---|---|
| Hãng/dòng phim | FilmStock → FilmRoll | Danh mục hay cho nhập tự do |
| ISO | FilmRoll.iso_used | Trường bắt buộc và cách khai báo |
| Máy/ống kính | FilmRoll; override frame nếu duyệt | Một cuộn có đổi ống kính không |
| Khổ phim | FilmRoll.film_format | Enum chưa chốt, không chỉ 35mm/120 |
| Số khung | FilmFrame.frame_number | Ký hiệu, uniqueness theo policy |
| Địa điểm chụp | FilmFrame.shooting_location đề xuất | Field chưa có ở dictionary; độ chi tiết/quyền xem TBD |
| Lab tráng | FilmRoll.lab_id/hồ sơ xử lý | Danh mục hay free text |
| Thông số scan | FilmFrame.scan_info hoặc Scan entity | Nhiều scan/frame cần model phù hợp |
| Bản scan | MediaAsset của bài | Một ảnh/bộ ảnh chưa chốt |
| Negative/contact sheet | Evidence assets liên kết | Đề tài mô tả tùy chọn; không tự ép bắt buộc |

Metadata hỗ trợ truy xuất, không chứng minh chắc ảnh là ảnh phim. Danh sách field bắt buộc cần duyệt, không mặc định mọi trường đều bắt buộc.

## 4. Upload → nộp bài

1. A29 tạo upload session thuộc principal và có purpose.
2. Client tải tệp; 100% upload chưa phải Submission.
3. A30 kiểm object thực, MIME/bytes/checksum theo thiết kế; READY hoặc lỗi.
4. A31 kiểm lại ownership, contest/category/enrollment/frame, media và rule thời gian. D-14 phải chốt; submitted_at do server ghi.
5. Transaction CSDL tạo bài, snapshot, liên kết asset và dấu vết; request key chống tác dụng lặp.
6. Sau commit, lên lịch validation/AI theo cơ chế được chọn; có đối soát nếu phát job lỗi.
7. Object đã lưu nhưng DB lỗi: giữ tệp tạm để đối soát; cleanup theo D-17. Không xóa ngay khi chưa biết request commit chưa.

Cloud không cùng transaction CSDL. Không hứa rollback cloud tự động. TTL upload/request key TBD.

## 5. Lifecycle độc lập

| Entity | Trạng thái thiết kế trong repo | Gate |
|---|---|---|
| Enrollment | REQUESTED/APPROVED/REJECTED/CANCELLED | D-03/D-15 |
| Submission | DRAFT/SUBMITTED/VALIDATING/PENDING_REVIEW/APPROVED/REJECTED; REVISION_REQUIRED, UNDER_INVESTIGATION có điều kiện | D-13/D-14/D-16/D-19 |
| ScoreSheet | DRAFT/FINALIZED; REOPENED nếu được phép | D-16/D-20 |
| Publication | DRAFT/READY_FOR_APPROVAL/BLOCKED/PUBLISHED; correction có điều kiện | D-06/D-16 |
| AIJob | QUEUED/RUNNING/SUCCEEDED/FAILED_RETRYABLE/UNAVAILABLE | Retry budget và lỗi vĩnh viễn cần thiết kế thêm |

Không dùng enum để tự cấp quyền rút/sửa/nộp lại/mở điểm. Cảnh báo AI không đổi bài sang REJECTED. Phiếu FINALIZED không sửa như nháp.

## 6. Chấm và công bố

Lưu điểm từng criterion đúng rubric version; tổng điểm client không làm chuẩn. D-06 phải chốt công thức/trọng số/làm tròn/điểm thiếu/đồng điểm trước tính chính thức. Round_id và điều kiện qua vòng phải rõ; không tự lấy top N.

Preview tạo snapshot gắn nguồn phiếu và thuật toán. Công bố kiểm điều kiện và quyền trong transaction; nguồn thay đổi cần xác minh lại. Giữ lịch sử kết quả, không ghi đè bản đã công bố. Nếu tạo archive bất đồng bộ, theo dõi trạng thái và đối soát, không báo đã lưu xong khi chỉ mới công bố điểm.

## 7. Đồng thời và khôi phục

**Optimistic locking**: chỉ ghi nếu version hiện tại bằng version đã đọc. Hai tab cùng version 3: lần đầu ghi thành version 4; lần sau conflict, đọc lại thay vì ghi đè. Đây là đề xuất D-20.

- Cùng request key/body không tạo hai bài hoặc hai quyết định.
- Cùng key nhưng body khác cần conflict theo contract.
- Frame khác owner hoặc category khác contest: từ chối trước ghi.
- Callback AI lặp không nhân đôi analysis; callback cũ không ghi đè tệp phiên mới.
- Metadata sửa sau nộp không được âm thầm đổi snapshot đang chấm.

Happy: bài hợp lệ tạo đúng một Submission, frame/asset/snapshot truy vết được.
Unhappy: storage thành công nhưng DB fail → không có bài giả; object tạm được đối soát.
Test: [Failure Recovery](../Docs/05-quality/failure-recovery-matrix.md), [Business Tests](../Docs/05-quality/business-scenario-tests.md). Actual: NOT RUN.
Jira: [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-64](https://0703032147khang.atlassian.net/browse/GC-64).
