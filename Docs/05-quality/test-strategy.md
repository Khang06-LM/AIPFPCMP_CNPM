# TEST-PLAN-01 — Chiến lược và kế hoạch kiểm thử

## 1. Test scope
- In-scope: 4 role, 5 Core Flow, metadata analog, storage, xác thực/phân quyền, scoring, AI fallback, Archive public safety.
- Out-of-scope trước khi có nguồn lực: kiểm thử sản xuất quy mô lớn, formal penetration test, xác nhận độ chân thật ảnh phim tuyệt đối.

## 2. Tầng kiểm thử

| Tầng | Mục tiêu | Ví dụ |
|---|---|---|
| Unit | Hàm xử lý rule, score, state transition | `score > max` -> lỗi; tính trọng số |
| Integration | FE→BE→DB/Storage/AI | Upload hoàn tất nhưng finalize sau deadline |
| API Security | Auth/role/owner | Judge truy cập bài khác -> 403 |
| E2E | 5 Core Flow trên UI | Organizer tạo → Participant nộp → duyệt → Judge chấm → công bố |
| Reliability | Retry/idempotency/job fallback | Ngắt mạng, duplicate request, AI timeout |
| AI Evaluation | Kết quả trên tập ảnh có nhãn | false positives/false negatives, mô hình version |
| Smoke/Regression | Hệ thống còn hoạt động sau merge | Login 4 vai, tạo contest, submission, publish |

## 3. Môi trường và dữ liệu
- Môi trường `local` + `staging` (chưa có URL xác thực trong nguồn).
- Seed users 4 vai trò; 2 contests (mở/đóng); 2 rolls với frame; ảnh JPEG/TIFF hợp lệ/sai MIME; ảnh trùng/gần giống; sample AI-generated có quyền sử dụng.
- Không sử dụng ảnh không có quyền, không đưa chứng từ riêng tư/token thật vào repo.

## 4. Entry / Exit
- **Entry:** FR/AC Approved; API contract và seed ổn định; code deploy được.
- **Exit:** Case cốt lõi không còn bug blocker/critical; test log rõ Actual/Evidence; mọi gap được ghi, không xóa bug để tạo bảng đẹp.

## 5. Thời điểm và owner
- Sprint 1: test strategy, auth/DB smoke; Sprint 2: nộp/metadata/validation; Sprint 3: E2E duyệt–chấm–công bố; Sprint 4: hồi quy, bảo mật, tính ổn định, báo cáo.
- Module owner viết TC, người khác review và chạy một phần test chéo; TV1 tổng hợp.

[Test Matrix](test-matrix.md) · [E2E Cases](e2e-test-cases.md)
