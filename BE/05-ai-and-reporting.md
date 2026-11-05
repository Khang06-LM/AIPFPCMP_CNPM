# BE — Hợp đồng AI và báo cáo

> **ID:** BE-05 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Đào Thị Thanh Bình; Bùi Đức Hưng · **Review:** owner liên quan và một thành viên khác.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Nội dung kỹ thuật là [D], chưa phải code hoặc bằng chứng kiểm thử.

## 1. Bốn năng lực

| Năng lực | FR | Input → output đề xuất | Giới hạn |
|---|---|---|---|
| Trùng/tương đồng | 13 | Ảnh + tập đối chiếu → candidates/score/version | Trùng byte khác tương đồng; scope so sánh TBD |
| Cảnh báo AI-generated | 14 | Scan có quyền → score/label/findings/version | Sai/bỏ sót; không chứng minh chắc nguồn |
| Gắn nhãn/phân loại | 15 | Ảnh + taxonomy → tags/categories | Có thể sai, quyền duyệt/sửa TBD |
| Phân tích/báo cáo | 16 | Dữ liệu scoped + metric definitions → thống kê/xu hướng/export | Số liệu đối chiếu CSDL, không AI bịa số |

Mức demo/tự huấn luyện/tích hợp API phải được xác nhận. Không mặc định gọi API là hoàn thành toàn bộ yêu cầu RBL/huấn luyện.

## 2. Contract nội bộ

| Field vào | Mục đích |
|---|---|
| job_id/submission_id/asset_id/task_type | Tách tác vụ và định danh |
| Quyền đọc tệp ngắn hạn hoặc service access | Không gửi credential cloud vô hạn |
| input checksum/version | Không gắn kết quả vào tệp đã thay |
| model/pipeline/config version | Tái lập |
| request_id/attempt | Theo dõi retry |
| Scope đối chiếu khi similarity | Không lộ ảnh ngoài quyền |

Output: job_id/status/model_version/input_version/processed_at/findings; score/label nullable khi không có kết quả. Cơ chế callback/polling và auth service TBD; client không được tự gửi “AI đã kết luận hợp lệ”.

## 3. Luồng và lỗi

BE tạo job sau input hợp lệ → worker đọc ảnh được cấp quyền → ghi kết quả đúng input version → Organizer xem cảnh báo → người có quyền tạo ReviewDecision.

Callback lặp không nhân đôi analysis; callback cũ không ghi đè phiên mới. Timeout/quota/unavailable là lỗi xử lý, không bằng chứng gian lận. Retry budget/backoff chưa chốt. D-19 quyết định có tiếp tục review tay khi AI lỗi; chưa duyệt thì ghi blocker, không tự thông qua.

AIAnalysis không trực tiếp sửa Submission sang REJECTED.

## 4. Analytics/export

[D] Metric gợi ý: đăng ký; bài theo trạng thái; hồ sơ thiếu; tiến độ chấm từng vòng; phân bố film stock/format; cảnh báo theo loại. Đây là đề xuất cần duyệt.

Mỗi metric có định nghĩa mẫu số, filter, thời điểm/múi giờ, scope và version. Tổng bài khác tổng ảnh khi đơn vị bài còn TBD. Export format/giới hạn/quyền tải chưa chốt; file xuất hết hạn phải cấp lại sau kiểm quyền. Nếu chọn mô hình ngôn ngữ để diễn giải, đầu vào là số đã tính và phần nhận định được ghi rõ.

## 5. Đánh giá

- Manifest ảnh: nguồn/quyền/nhãn đối chiếu; tách train/evaluation nếu huấn luyện.
- Duplicate: tệp giống byte, resize/crop và ảnh khác; chọn ngưỡng sau đo.
- Detector: cảnh báo đúng/sai, bỏ sót trên scan; score chưa hiệu chuẩn không gọi xác suất.
- Tags: đánh giá theo taxonomy đã chốt và người kiểm.
- Reports: seed có kết quả kỳ vọng, query đối chiếu cùng filter.
- Kiểm tra tích hợp thành công khác chứng minh mô hình chất lượng tốt; cả hai cần evidence.

Happy: có candidate tương đồng → lưu cảnh báo, không tự loại bài.
Unhappy: provider hết quota → unavailable/error, giữ bài và lịch sử, UI không ghi “ảnh giả”.
Nguồn: [FR Index](../Docs/02-requirements/README.md), [Feasibility](../Docs/06-feasibility/feasibility-study.md).
Jira: [GC-51](https://0703032147khang.atlassian.net/browse/GC-51), [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-68](https://0703032147khang.atlassian.net/browse/GC-68).
Actual/accuracy/model đã chọn: TBD / NOT RUN.
