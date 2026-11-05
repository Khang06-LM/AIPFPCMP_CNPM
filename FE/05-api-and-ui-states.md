# FE — API client, trạng thái giao diện và phục hồi

> **ID:** FE-05 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Hoàng Duy Khánh; Nguyễn Khánh Hiền · **Review:** Bùi Đức Hưng và owner liên quan.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa chốt. Các quy trình kỹ thuật là [D]; quyền/ngưỡng/thể lệ chưa xác nhận không được tự áp dụng. Chưa có bằng chứng ứng dụng chạy.

## 1. Contract duy nhất

Dùng envelope/error ở [BE-02](../BE/02-api-contract.md): data hoặc error{code,message,details}, meta.request_id.
DTO/status/code phải khớp phiên bản BE; không dùng message tự do để quyết nghiệp vụ. Base URL/runtime/session transport TBD. Không có endpoint thật trong lần cập nhật tài liệu.

## 2. Trạng thái UI chuẩn

| State | Hiển thị | Thao tác | Dữ liệu giữ lại |
|---|---|---|---|
| Initializing | Đang xác định phiên/quyền | Chưa cho mutation | Không lộ dữ liệu cache cũ |
| Loading | Skeleton/progress có nhãn | Chặn thao tác lặp khi cần | Input/dữ liệu trước nếu vẫn đúng scope |
| Empty | Chưa có dữ liệu | Action tạo nếu đủ quyền | Filter hiện tại |
| Ready | Nội dung và actions được phép | Theo state/policy | Server version |
| Submitting | Đang lưu/gửi/chốt | Chặn cùng mutation lặp | Draft và request key |
| Uncertain | Chưa rõ server đã xử lý | Đọc lại, hướng dẫn chờ | Key/version/draft an toàn |
| Validation error | Lỗi trường hoặc rule | Cho sửa phần hợp lệ | Không xóa form |
| Unauthorized | Yêu cầu đăng nhập lại | Theo session policy | Chỉ nháp có thể giữ an toàn |
| Forbidden/not found | Không có quyền/không truy cập được | Quay về danh sách hợp lệ | Không giữ ảnh ngoài quyền |
| Conflict | Dữ liệu đã thay đổi | Refetch/đối chiếu | Draft để người dùng so sánh |
| Dependency error | Storage/AI/report chưa sẵn sàng | Retry theo chỉ dẫn | Trạng thái nghiệp vụ chưa được suy diễn |
| Success | ACK và bước tiếp theo | Điều hướng/refetch đúng scope | ID/version từ server |

Lỗi 5xx/timeout không khẳng định mutation đã rollback. Empty chỉ khi request thành công với danh sách rỗng, không thay mọi lỗi bằng [].

## 3. Quy tắc request/cache

- Query key có user/scope/contest/round/resource/filter cần thiết. Logout/đổi người dùng xóa cache private và hủy request cũ.
- Bảo vệ response đến muộn: không cho dữ liệu contest A ghi đè màn hình contest B.
- Mutation thành công invalidate đúng dữ liệu liên quan: review→queue/detail/status; score→sheet/progress; publish→results/archive theo trạng thái thực.
- Không optimistic update kết quả review/chốt/công bố trước khi server xác nhận.
- Không tự retry POST/PUT quyết định chỉ vì gặp mạng lỗi; idempotency và lookup phải được contract hỗ trợ.
- Polling interval/backoff/stop condition TBD; không polling vô hạn hoặc mọi tab nhân request không kiểm soát.
- 409 không “thử lại đến thành công”; phải đọc lại version và người dùng xác nhận nội dung phù hợp.

## 4. Session và dữ liệu nhạy cảm

Session adapter theo thiết kế BE: cookie hoặc bearer chưa chốt. Không ghi token vào URL/log/screenshot; không nhúng signing key/provider key vào bundle.
Nếu dùng cookie: review CSRF/SameSite/Secure/HttpOnly theo deployment. Nếu bearer: review XSS và chỗ lưu token. Guard UI không thay authorization API.

Không cache ảnh private như public static. Link ảnh hết hạn xin mới qua BE theo quyền; error message không lộ signed URL. Draft metadata/điểm giữ local memory theo mức an toàn; persistent draft cần policy TTL/cleanup/đổi tài khoản trước khi dùng.

## 5. Form validation và messages

Field required/range/format lấy từ schema/rule; FE validate cho UX, BE vẫn kiểm lại. Render lỗi theo error.details.field và lỗi chung theo code. Input text có label, focus vào lỗi đầu; thông báo trạng thái bằng chữ, không chỉ màu.

Message gợi ý [D]:
- CONTEST_CLOSED: “Cuộc thi không nhận bài tại thời điểm gửi.”
- VERSION_CONFLICT: “Dữ liệu đã thay đổi. Hãy tải lại trước khi tiếp tục.”
- AI_UNAVAILABLE: “Chưa có kết quả phân tích AI.”
- SUBMISSION_RESULT_UNKNOWN (trạng thái UI, không mặc định server code): “Đang xác minh kết quả gửi bài.”

Không chuyển unavailable thành “bài không hợp lệ”; không nói “đã duyệt” khi chỉ job validation xong.

## 6. Mock và tích hợp

Mock dùng cùng A-code/DTO/error để làm prototype, gắn nhãn rõ. Khi nối BE thật kiểm schema, quyền và thời gian; không dùng mock response làm actual evidence.
Cần bổ sung lookup theo request key trước khi hiện thực recovery của A31; không đoán URL ở client.
Mọi thay đổi contract cần review BE+FE và cập nhật màn hình/test cùng PR.

Happy: A41 ACK version mới → cập nhật sheet và progress đúng scope.
Unhappy: A41 trả 409 → giữ draft, tải phiên mới, không ghi đè tự động.
FR-01..18; test [Failure Recovery](../Docs/05-quality/failure-recovery-matrix.md).
Jira: [GC-53](https://0703032147khang.atlassian.net/browse/GC-53), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65). Actual: NOT RUN.
