# PROB-01 — Phân tích vấn đề (Problem Statement)

> **Nguồn:** [G] Bối cảnh và giải pháp đề xuất trong đề tài 6 trang của giảng viên. Những phần mục tiêu đo lường được đặt `[D]` và cần kiểm chứng, không xem là số liệu khảo sát đã thu thập.

## Bối cảnh kinh doanh
Cuộc thi ảnh phim khác cuộc thi ảnh số: mỗi ảnh được gửi bằng **bản scan** nhưng còn mang thông số analog như loại film stock, camera body, lens, ISO, film format, frame number, lab tráng, scan information và chứng cứ negative/contact sheet tùy chọn. [G] mô tả ban tổ chức hiện có thể dùng rời rạc Google Forms, Excel/Sheets, email, cloud drive và mạng xã hội, làm việc kiểm soát hồ sơ, kiểm duyệt, chấm điểm và lưu trữ khó truy vết.

## Problem decomposition — Tách vấn đề

| ID | Vấn đề | Stakeholder ảnh hưởng | Hậu quả dễ quan sát | Requirement liên quan |
|---|---|---|---|---|
| PB-01 | Cấu hình cuộc thi/tiêu chí nằm ở nhiều tài liệu | Organizer | Dễ dùng sai phiên bản quy chế | FR-03 |
| PB-02 | Metadata phim/khung hình không nối với ảnh scan | Participant, Organizer | Khó đối chiếu nguồn gốc bài dự thi | FR-05, FR-06 |
| PB-03 | Hồ sơ nộp bài và duyệt thiếu trạng thái rõ | Participant, Organizer | Người nộp không biết xử lý đến đâu, dễ làm lại | FR-04, FR-07, FR-08, FR-17 |
| PB-04 | Phân công/điểm của Judge không tập trung | Judge, Organizer | Khó tổng hợp, truy vết chấm nhiều vòng | FR-09, FR-10, FR-11 |
| PB-05 | Ảnh trùng/AI-generated có thể lọt qua rà soát thủ công | Organizer | Tăng chi phí kiểm duyệt | FR-13, FR-14, FR-15 |
| PB-06 | Tác phẩm đạt giải và metadata không lưu tập trung | Cộng đồng, Participant | Thiếu hồ sơ lịch sử / triển lãm | FR-12, FR-17 |

## Mục tiêu nghiệp vụ (BG)
- **BG-01:** Một hành trình quản lý cuộc thi liền mạch từ khởi tạo đến kết quả.
- **BG-02:** Nộp bài ảnh phim có quan hệ kiểm chứng được giữa `FilmRoll → FilmFrame → Submission → MediaAsset`.
- **BG-03:** Đánh giá và quyết định của người có thẩm quyền được lưu vết, có thể kiểm tra.
- **BG-04:** AI **hỗ trợ** đối chiếu và gắn nhãn, không thay quyết định con người.
- **BG-05:** Lưu tác phẩm đoạt giải theo quyền công bố và siêu dữ liệu phù hợp.

## Mức đáp ứng, không thổi phồng kết quả

| Mục tiêu | Bằng chứng thiết kế cần tạo | Bằng chứng triển khai cần có | Trạng thái hiện tại |
|---|---|---|---|
| BG-01 | Flow diagram + FR-03→FR-12 | E2E demo 4 role, log và test | **Chưa có trong tài liệu nguồn** |
| BG-02 | ERD, data dictionary, FR-05/06 | Demo frame gắn ảnh và truy FK | **Chưa có** |
| BG-03 | Ma trận RBAC, review/score audit | Test 403, version conflict, lịch sử | **Chưa có** |
| BG-04 | Báo cáo PoC + giới hạn model | Confusion matrix/AI benchmark có mẫu | **Chưa có** |
| BG-05 | Archive schema + quyền public | Trang public, test không lộ negative | **Chưa có** |

## Không nhầm vấn đề với giải pháp
Vấn đề: kiểm duyệt chậm/khó minh bạch. **Giải pháp lựa chọn:** platform tập trung + rules engine + AI warning. Không nói “AI giải quyết hoàn toàn gian lận” hay “dùng cloud nghĩa là lưu vĩnh viễn”.

## Tài liệu liên kết
[Context & Stakeholders](context-stakeholders.md) · [Core Flows](end-to-end-flows.md) · [SRS](../02-requirements/README.md).
