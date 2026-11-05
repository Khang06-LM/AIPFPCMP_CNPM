# BE — Xác thực, phân quyền và bảo vệ dữ liệu

> **ID:** BE-04 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Bùi Đức Hưng; Lê Minh Khang · **Review:** owner liên quan và một thành viên khác.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Nội dung kỹ thuật là [D], chưa phải code hoặc bằng chứng kiểm thử.

## 1. Xác thực khác phân quyền

[G] JWT/RBAC tại 3.2.d. Xác thực xác định người dùng; phân quyền quyết định thao tác được phép. JWT hợp lệ không cho xem mọi bài cùng vai trò.

[D] Kiểm signature, thuật toán/key cấu hình, issuer/audience khi dùng, thời hạn hiệu lực. Token lifetime/refresh/logout/revoke/đa phiên/đa vai trò TBD. Không tin role hoặc user_id trong body. Không tự thêm quy tắc một thiết bị.

## 2. Ma trận quyền để review

| Tài nguyên | Participant | Organizer | Judge | Admin |
|---|---|---|---|---|
| Profile/portfolio | Của mình; public TBD | Không mặc định sửa người khác | Không mặc định sửa người khác | Quyền quản trị cụ thể TBD |
| Roll/frame/evidence | Owner, theo trạng thái | Chỉ hồ sơ phục vụ contest được giao | Chỉ dữ liệu cần chấm theo policy | Không mặc định mọi ảnh private |
| Contest/template | Xem phần được phép | Tạo/sửa trong scope | Theo assignment | Cấu hình chung; override TBD |
| Enrollment/submission | Của mình; sửa/rút TBD | Đúng contest | Bài được phân công | Không mặc định thay quyết định |
| ScoreSheet | Feedback được phép xem | Quyền xem/sửa điểm TBD | Phiếu của mình, đúng assignment | Không mặc định chấm thay |
| Ranking/archive/report | Theo công bố/quyền | Đúng contest | Theo policy | Scope quản trị đã chốt |
| User/role/master-data | Không | Không trừ cấp riêng rõ ràng | Không | Quản lý theo đề tài |

Public viewer là quyền xem cần xác nhận, không mặc định vai trò đăng ký thứ năm. Ẩn danh/xung đột lợi ích cần quyết định.

## 3. Trình tự kiểm quyền server

Xác thực → xác định action/resource → query scope → kiểm role/owner/contest/assignment → kiểm state/version/BR → chỉ trả field cho người xem → audit tối thiểu.

Chặn mass assignment: PATCH profile không nhận role; tạo bài không tự chọn participant khác; ghi điểm không tin tổng/ranking client. Kiểm cả quyền đọc và quyền ghi, kể cả người dùng biết URL/ID.

## 4. Media và gửi ảnh sang AI

- [G] bảo mật và lưu ổn định trên Object Storage; [D] private bucket + truy cập có scope là phương án.
- Kiểm MIME/content thật; bytes theo rule được duyệt, filename không thành đường dẫn storage tùy ý.
- Signed URL là quyền tạm; thời hạn TBD; hết hạn phải kiểm quyền lại trước cấp mới.
- Public ảnh đoạt giải không đồng nghĩa public negative/contact sheet. Không gửi object_key/evidence URL qua DTO public.
- Dịch vụ AI chỉ nhận ảnh được phép; quyền gửi ảnh, retention và điều khoản dịch vụ cần kiểm tra trước dữ liệu thật.
- Log/error không chứa password/token/key/ảnh private hoặc dữ liệu cá nhân không cần.

## 5. Cấu hình và secrets

| Biến đề xuất | Nơi sử dụng | Quy tắc |
|---|---|---|
| DATABASE_URL | BE | Placeholder trong ví dụ |
| JWT_SECRET hoặc key config | BE | Cơ chế key chưa chốt; không đưa FE |
| OBJECT_STORAGE_BUCKET/CREDENTIALS | BE | Không JSON key trong git, không default public |
| AI_SERVICE_URL/AI_PROVIDER_KEY | BE/AI | Provider key không vào bundle FE |
| ALLOWED_ORIGINS | BE | Theo môi trường, không thay xác thực |

Session transport TBD: cookie cần thiết kế HttpOnly/Secure/SameSite và CSRF theo topology; bearer cần phân tích XSS/lưu token/refresh. Không khẳng định localStorage luôn an toàn hoặc CORS thay auth.

Lộ secret cần revoke/rotate, kiểm phạm vi và xử lý incident; xóa file ở commit mới chưa vô hiệu credential đã lộ.

## 6. Kiểm chứng

Happy: Judge đúng assignment xem/chấm phiếu của mình.
Unhappy: thay scoresheet_id người khác → từ chối, không thay điểm hoặc lộ ảnh.
Test thêm: tự cấp role, Organizer khác contest, token hết hạn, URL evidence trực tiếp, dữ liệu riêng trong cache server.
Test ứng dụng: NOT RUN; chỉ tiêu tải/rate limit TBD.
Nguồn: [FR Index](../Docs/02-requirements/README.md), [Decision Log](../Docs/01-governance/open-questions.md).
Jira: [GC-48](https://0703032147khang.atlassian.net/browse/GC-48), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60), [GC-67](https://0703032147khang.atlassian.net/browse/GC-67).
