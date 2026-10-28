# EST-01 — Ước lượng theo **giờ thực tế** thay vì đồng nhất SP với thời gian

> Giảng viên yêu cầu tính theo giờ vì thời gian học có hạn. File CSV đi kèm là **ước lượng minh họa để re-estimate**, không phải lịch làm việc đã được nhóm cam kết: [Story Hours CSV](story-hours-estimate.csv).

## 1. Phân biệt SP và giờ

Kế hoạch nhóm ghi 284 SP, giả định “1 SP ≈ nửa ngày công”. Nếu hiểu nửa ngày = 4 giờ thì **284 × 4 = 1.136 giờ công**, tương đương trung bình **227,2 giờ/người cho 8 tuần**, khoảng **28,4 giờ/người/tuần** (chưa xét trùng việc). Đây là lý do cần rà lại khả thi: nhóm còn môn khác và SP thực chất phù hợp hơn để so sánh độ khó tương đối.

**Không khuyên giữ tỷ lệ 1 SP = 4 giờ cứng.** Với yêu cầu của thầy, nhóm có thể lưu cột `SP kế hoạch` để truy vết, còn phân công sprint dùng `Estimated Hours` và `Available Hours` đã xác nhận.

## 2. Mẫu bảng capacity nhóm phải điền sau họp

| Thành viên | Giờ/tuần khả dụng | Số tuần còn lại | Giờ dự phòng | Tổng giờ khả dụng |
| --- | --- | --- | --- | --- |
| TV1 – PM/BA/Admin | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận |
| TV2 – Backend | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận |
| TV3 – Participant FE | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận |
| TV4 – Organizer/Judge | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận |
| TV5 – AI | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận | Chưa xác nhận |

**Công thức:** `Available Hours = số tuần × giờ/tuần − giờ nghỉ/thi/đệm`. Tổng giờ của 5 người là **capacity**, so với effort đã tính gồm docs/test/merge/review/AI PoC.

## 3. Dải giờ thử nghiệm trong CSV (không phải cam kết)

- Code/API thường: `3–5 giờ/SP` chỉ để **hiển thị phạm vi bất định**.
- AI/R&D hoặc module upload/chấm điểm: `4–7 giờ/SP` để phản ánh PoC/rework; sai số còn lớn.
- Story viết tài liệu/test: `2–4 giờ/SP`; không đồng nghĩa tài liệu tự động xong.
- Từ dải trên, tổng toàn bộ 69 Story trong CSV có min **836** và max **1465 giờ minh họa**; không nên coi đây là kết quả đo thực tế hoặc estimate chính thức.

## 4. Cách chốt estimate chuẩn

1. Mỗi owner nêu subtask có thể quan sát (ví dụ FR-06: form, upload API, object storage, finalize, lỗi retry, test 7 case).
2. Ước lượng **giờ cho từng subtask** theo kiến thức hiện có, không copy 4h/SP.
3. Thêm `Review/Test/Integration` và **dự phòng rủi ro** riêng.
4. So với capacity cá nhân; phần nào vượt thì giảm scope mở rộng hoặc tăng hỗ trợ và trình GV nếu chạm mục bắt buộc.
5. Mỗi ngày cập nhật `Actual Hours` ở Jira hoặc bảng nhóm; cuối sprint điều chỉnh ước lượng còn lại.

## 5. Ví dụ triển khai FR-06 (chỉ minh họa)

| Phần việc | Giờ dự kiến tham khảo | Kiểm chứng |
|---|---:|---|
| SRS + rule/AC review | 3 | PR docs review |
| Form upload + UX trạng thái | 5 | Happy/Unhappy UI |
| Signed URL / finalize API | 8 | API integration test |
| Metadata FK/state/check | 4 | DB validation test |
| Retry + permission + rollback tests | 6 | Test evidence |
| Review PR + sửa lỗi/README | 3 | CI & reviewer |
| **Tổng ví dụ FR-06** | **29 giờ** | Phải được TV2/TV3 điều chỉnh |

## 6. Sổ rủi ro ước lượng

Nếu nhu cầu thực > capacity, ưu tiên 5 Core Flow vận hành liên thông; Flutter + analytics nâng cao + triển lãm nâng cao chỉ làm khi GV đồng ý và có đủ giờ. Việc AI không đạt benchmark phải được báo cáo trung thực.
