# Frontend (FE) — Web UI Contract, UX & Error Handling

> **Trạng thái:** Thiết kế giao diện đề xuất, không có mã nguồn Next.js đính kèm. Kế hoạch [P] dự kiến React/Next.js và Flutter cho Participant; xác nhận MVP Web/mobile theo D-01.

## 1. Mục tiêu FE theo vai trò

| Role | Màn hình tối thiểu | Điều kiện hiển thị | FR |
|---|---|---|---|
| Participant | Login, Contest List, Enrollment, Roll, Frame, Upload, My Submission, My Result | Có token; phần public tùy page | FR-01/04/05/06/17 |
| Organizer | Contest Setup, Registration Review, Submission Review Queue, Judge Assignment, Results Publish | Scope contest được giao | FR-03/04/08/09/11 |
| Judge | My Assignments, Submission Detail, Criteria Scoring, Finalize | Assigned Judge only | FR-09/10 |
| Admin | User/Role, Master Data, Audit/Dashboard | Role Admin | FR-02 |
| Public | Contest listing/Results/Archive | Chỉ công khai sau publish | FR-11/12 |

## 2. Cấu trúc route đề xuất (không có code chạy trong zip)

```text
FE/
├── README.md
└── src/app/
    ├── (public)/contests
    ├── (public)/archive
    ├── (auth)/login
    ├── participant/film-rolls
    ├── participant/submissions/new
    ├── participant/submissions/[id]
    ├── organizer/contests
    ├── organizer/review-queue
    ├── organizer/judge-assignments
    ├── organizer/results
    ├── judge/assignments
    ├── judge/scores/[id]
    └── admin/users
```

## 3. UX state của mọi form, phải có cả happy/unhappy

| State | FE cần hiển thị | API/trạng thái liên quan |
|---|---|---|
| Loading | Spinner/disable nút chống double submit | Request đang chạy |
| Empty | Không có cuộc thi/bài/điểm; CTA hợp lệ | 200 empty list |
| Validation error | Gắn lỗi đúng field và giữ dữ liệu nhập | 422 field errors |
| Authentication error | Yêu cầu login, không lộ dữ liệu nhạy cảm | 401 |
| Forbidden | 403 + dẫn về trang phù hợp | 403 |
| Conflict | “Dữ liệu đã thay đổi, tải lại”, tránh ghi đè | 409 |
| External service failure | Cho retry phù hợp, phân biệt lỗi AI/cloud với bài không hợp lệ | 5xx, timeout |
| Success | Hiển thị mã ID, trạng thái, bước tiếp theo | 200/201 |

## 4. Detailed UI journey — Participant upload

1. Vào Contest đã công bố → đọc rule/time/date. Nếu đóng, không hiện nút nộp (BE vẫn là nguồn chặn cuối).
2. Đăng ký, xem Enrollment APPROVED nếu policy yêu cầu.
3. Tạo FilmRoll và FilmFrame; form có film stock, ISO, camera, lens, frame number, lab và scan metadata theo quy chế.
4. Màn hình nộp: chọn frame + ảnh. Hiện accepted formats/size **từ cấu hình**, không tự ghi 20 MB như sự thật.
5. Upload có progress; khi storage fail phải giữ form và cho retry an toàn.
6. Khi bấm Gửi, BE xác minh deadline và resource ownership lần cuối; thành công trả Submission ID + status.
7. Khi hệ thống yêu cầu sửa, hiển thị lý do và chỉ mở thao tác được phép theo BR.

## 5. Detailed UI journey — Organizer/Judge

- Organizer tạo contest → category/criteria → publish → xem hàng đợi validation/AI flags → accept/reject/revision (lý do nếu reject) → gán Judge.
- Judge chỉ thấy bài được gán, nhập điểm theo criterion (range từ BE), lưu nháp, chốt; khi đã chốt không tự mở lại.
- Organizer kiểm chưa thiếu phiếu/tie-break → duyệt bảng xếp hạng → publish. Chưa publish thì FE public tuyệt đối không gọi API điểm nháp.

## 6. API & contract policy

- Central API client xử lý HTTP error theo mã thống nhất từ BE, không parse message text tự do.
- Dùng mock đúng OpenAPI khi BE chưa xong; thay mock bằng API thật và test integration.
- Ẩn nút theo role chỉ cải thiện UX, **bắt buộc BE kiểm quyền**.
- Không để JWT secret, cloud admin credentials, API Key vào bundle FE.
- Form file upload xử lý timeout và retry; không coi tiến trình upload 100% là Submission được nộp nếu finalize chưa xác nhận.

## 7. Responsive / Accessibility

- Participant dùng được trên viewport điện thoại; bàn phím focus cho form, thông báo lỗi gắn label.
- Trạng thái có text rõ, không chỉ màu; alt cho ảnh công khai.
- Với ảnh private, không dùng `<img>` truy object bucket public trái chính sách.

## 8. README cần TV3/TV4 bổ sung khi có source

`Node version`, package manager, lệnh install/dev/build/test, env sample (`NEXT_PUBLIC_API_BASE_URL`), route guards, screenshot 4 role, vị trí component, test run evidence, known issues.

[FR Index](../Docs/02-requirements/README.md) · [BE API Contract](../BE/README.md) · [E2E Tests](../Docs/05-quality/e2e-test-cases.md)

## V3 — Frontend business states / interaction failures (đề xuất)

| Màn hình | Tình huống bất thường | UX bắt buộc |
|---|---|---|
| Submission | Upload xong nhưng quá hạn finalize | Không báo nộp thành công; hiện `CONTEST_CLOSED` và hướng dẫn |
| Submission | Request timeout sau finalize | Không tự xóa form và không tự gửi request key mới; đọc lại trạng thái trước |
| Review | Organizer khác cập nhật version | Thông báo nội dung đã thay đổi, yêu cầu refresh thay vì ghi đè |
| Scoring | Timeout sau Save / Finalize | Hiện trạng thái chưa rõ, GET lại ScoreSheet; hiển thị version/current status |
| Scoring | Bài bị UNDER_INVESTIGATION | Chặn thao tác tiếp nếu BE xác nhận trạng thái không hợp lệ |
| Publication | Tie chưa được giải quyết | Không cho nút Publish hoạt động, hiển thị lý do bị chặn |
| AI Review | AI unavailable hoặc false-positive | Hiển thị trạng thái `Chờ kiểm tra thủ công`, không kết luận gian lận |
| Archive | Evidence private | Không render link trực tiếp; API phải kiểm quyền kể cả khi user biết URL |

**BE là nguồn sự thật**: disable nút FE chỉ giúp UX, không thay thế xác thực/authorization. Mỗi màn hình có `loading / empty / success / validation error / permission error / conflict / server error / retrying` states theo khả năng.

Reference: [State Machines](../Docs/03-domain/state-transitions.md), [Business Decision Tables](../Docs/03-domain/business-decision-tables.md), [Recovery](../Docs/05-quality/failure-recovery-matrix.md).
