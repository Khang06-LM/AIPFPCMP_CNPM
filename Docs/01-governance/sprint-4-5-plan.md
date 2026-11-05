# Kế hoạch Sprint 4–5: hoàn thiện toàn bài CNPM

- Ngày lập: 05/11/2026 (Asia/Saigon).
- Trạng thái: **PLANNED / REVIEW REQUIRED**; đã tạo công việc trên Jira, chưa cam kết lịch/capacity hoặc chứng nhận phần mềm hoàn thành.
- Dự án: AI-powered Film Photography Contest Management Platform.
- Jira: [GC board](https://0703032147khang.atlassian.net/jira/software/projects/GC/boards/2/backlog); [Epic GC-37](https://0703032147khang.atlassian.net/browse/GC-37).
- Repository: [Khang06-LM/AIPFPCMP_CNPM](https://github.com/Khang06-LM/AIPFPCMP_CNPM).
- Sprint 4: ID **4**, **Sprint 4 - Chương III-IV**, future, 18 task.
- Sprint 5: ID **5**, **Sprint 5 - Chương V-VI**, future, 20 task.
- Ngày bắt đầu/kết thúc: **TBD**. Hạn môn học và giờ khả dụng chưa được xác nhận; không dùng lại ngày cũ ở Sprint 1–3.

## 1. Căn cứ và ranh giới

1. **[G]** Đề tài CNPM tiếng Việt: giữ 4 vai trò, 5 Core Flow, metadata ảnh phim, 4 năng lực AI, JWT/RBAC, cloud storage, kiến trúc mô-đun và các sản phẩm bàn giao.
2. **[M]** Mẫu “Copy of Final_Project_Report_PDCMS.docx.pdf” (419 trang PDF): chỉ tham khảo cấu trúc I–VI; III.3.2, III.4–5; IV kiến trúc/package/database/detailed design; V scope/strategy/plan/cases/reports; VI package/install/user guides. Không chuyển chức năng nha khoa, công nghệ, 10MB, công thức, thời hạn hoặc chỉ tiêu chất lượng của mẫu thành yêu cầu dự án.
3. **[R]** Repo tại main được kiểm tra ngày lập: gồm bộ tài liệu 18 FR/8 NFR, BE/FE README và Skills. BE/FE README ghi rõ chưa có source chạy trong bộ hồ sơ; chưa có cơ sở kết luận đã triển khai hoặc kiểm thử đạt.
4. **[J]** Jira GC-1..GC-36 đang Done theo dữ liệu đọc được. Các Sprint 1–3 đang active; kế hoạch này không đóng/mở lại hoặc đổi trạng thái công việc cũ. Done trên Jira không thay bằng chứng nội dung báo cáo/code.
5. **[D]** Phân rã, ước lượng, reviewer và thứ tự ở đây là đề xuất lập kế hoạch. Các quy tắc chưa được giảng viên/nhóm duyệt giữ TBD. Không dùng kế hoạch này để tự bỏ Mobile, nhiều vòng hoặc năng lực AI.

Kế thừa [FR Index](../02-requirements/README.md), [Open Questions](open-questions.md), [Report Map](../07-review/report-map.md). Giữ mã **FR-01..18/NFR-01..08** của repo; mã FPC trong hồ sơ cũ là tham chiếu kế hoạch, không giả làm issue Jira GC tồn tại.

## 2. Kết quả hai sprint

| Sprint | Phạm vi | Điều kiện ra |
|---|---|---|
| 4 | Bổ sung III.3.2, III.4–5; hoàn thiện IV.1–3; hợp đồng API, UI, nghiên cứu khả thi và review | Đủ coverage nguồn→FR→BR→dữ liệu→UI/API→thiết kế→test dự kiến; blocker được chốt hoặc ghi rõ; PR review |
| 5 | Hoàn thiện implementation còn thiếu làm tiền đề kiểm thử; Chương V; Chương VI; tổng hợp I–VI, slide/demo | Code/môi trường/bằng chứng thật; test report trung thực; gói bàn giao/guide tái lập; gap được trình bày |

**Đây là gói phạm vi dự kiến, chưa phải cam kết hoàn tất trong một khoảng thời gian cố định.** Sprint 5 chứa khối lượng xây dựng lớn vì chưa thấy source ứng dụng trong nhánh main. S4-01 phải kiểm tra có source ở nơi khác, cập nhật baseline và chia nhỏ gói triển khai trước khi bắt đầu; không làm lại phần đã có bằng chứng. Nếu capacity không đủ, nhóm cần điều chỉnh lịch/chia đợt được chấp thuận; không âm thầm cắt yêu cầu. Phiên lập kế hoạch này không viết code, chạy test hay deploy.

## 3. Thành viên và ước lượng sơ bộ

Assignee đã đối chiếu danh sách được phép nhận việc của project GC với phân công trong README. Reviewer ghi trong mô tả task; không tự gửi yêu cầu review tới tài khoản GitHub chưa được xác minh.

| Thành viên | Vai trò chính | Sprint 4: task / giờ đề xuất | Sprint 5: task / giờ đề xuất |
|---|---|---|---|
| Lê Minh Khang | PM/BA + Admin + báo cáo | 5 / 20–30 | 4 / 31–59 |
| Bùi Đức Hưng | Backend/Tech Lead + dữ liệu | 5 / 22–36 | 5 / 54–108 |
| Hoàng Duy Khánh | Participant FE | 3 / 18–28 | 4 / 34–68 |
| Nguyễn Khánh Hiền | Organizer/Judge | 2 / 14–22 | 3 / 36–72 |
| Đào Thị Thanh Bình | AI/Analytics | 3 / 16–28 | 4 / 42–84 |

Các khoảng giờ là ước lượng sơ bộ chưa đo, không là hạn hoặc timesheet. Giờ rảnh thực tế: **TBD cho từng người**, ghi nhận trong [GC-38](https://0703032147khang.atlassian.net/browse/GC-38). Backend đang có nguy cơ quá tải; owner FE/AI/Admin phải cùng làm phần tích hợp/API của module, TV2 chủ trì review và phần dùng chung. Gói lớn phải tách subtask trước cam kết. Không quy đổi 284 SP trong hồ sơ cũ thành giờ thực tế.

## 4. Backlog Sprint 4

| Jira | Công việc | Chủ trì | Reviewer | Giờ đề xuất | Phụ thuộc |
|---|---|---|---|---|---|
| [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) | Chốt phạm vi, quyết định còn mở và kế hoạch toàn bài | Lê Minh Khang | Nguyễn Khánh Hiền | 4–6 | [GC-36](https://0703032147khang.atlassian.net/browse/GC-36) |
| [GC-39](https://0703032147khang.atlassian.net/browse/GC-39) | III.3.2 Đặc tả xác thực và quản trị hệ thống | Lê Minh Khang | Bùi Đức Hưng | 4–6 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) |
| [GC-40](https://0703032147khang.atlassian.net/browse/GC-40) | III.3.2 Đặc tả toàn bộ nghiệp vụ thí sinh | Hoàng Duy Khánh | Nguyễn Khánh Hiền | 6–8 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) |
| [GC-41](https://0703032147khang.atlassian.net/browse/GC-41) | III.3.2 Đặc tả tổ chức, duyệt bài, chấm và công bố | Nguyễn Khánh Hiền | Bùi Đức Hưng | 6–10 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) |
| [GC-42](https://0703032147khang.atlassian.net/browse/GC-42) | III.3.2 Đặc tả bốn năng lực AI và báo cáo | Đào Thị Thanh Bình | Bùi Đức Hưng | 4–8 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) |
| [GC-43](https://0703032147khang.atlassian.net/browse/GC-43) | III.4 Yêu cầu phi chức năng và kế hoạch đo | Bùi Đức Hưng | Đào Thị Thanh Bình | 3–5 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) |
| [GC-44](https://0703032147khang.atlassian.net/browse/GC-44) | III.5 Business Rules, Common Requirements và Messages | Lê Minh Khang | Nguyễn Khánh Hiền | 4–6 | [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-42](https://0703032147khang.atlassian.net/browse/GC-42) |
| [GC-45](https://0703032147khang.atlassian.net/browse/GC-45) | IV.1.1 Thiết kế kiến trúc toàn hệ thống | Bùi Đức Hưng | Đào Thị Thanh Bình | 4–6 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38), [GC-43](https://0703032147khang.atlassian.net/browse/GC-43) |
| [GC-46](https://0703032147khang.atlassian.net/browse/GC-46) | IV.1.2 Package Diagram và trách nhiệm mô-đun | Bùi Đức Hưng | Hoàng Duy Khánh | 3–5 | [GC-45](https://0703032147khang.atlassian.net/browse/GC-45) |
| [GC-47](https://0703032147khang.atlassian.net/browse/GC-47) | IV.2 Thiết kế CSDL, ERD và từ điển dữ liệu | Bùi Đức Hưng | Nguyễn Khánh Hiền | 6–10 | [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-42](https://0703032147khang.atlassian.net/browse/GC-42), [GC-44](https://0703032147khang.atlassian.net/browse/GC-44) |
| [GC-48](https://0703032147khang.atlassian.net/browse/GC-48) | IV.3 Thiết kế chi tiết xác thực và Admin | Lê Minh Khang | Bùi Đức Hưng | 4–6 | [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47) |
| [GC-49](https://0703032147khang.atlassian.net/browse/GC-49) | IV.3 Thiết kế chi tiết Participant và nộp ảnh | Hoàng Duy Khánh | Bùi Đức Hưng | 6–10 | [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47) |
| [GC-50](https://0703032147khang.atlassian.net/browse/GC-50) | IV.3 Thiết kế chi tiết Organizer, Judge và archive | Nguyễn Khánh Hiền | Bùi Đức Hưng | 8–12 | [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-44](https://0703032147khang.atlassian.net/browse/GC-44), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47) |
| [GC-51](https://0703032147khang.atlassian.net/browse/GC-51) | IV.3 Thiết kế chi tiết AI, analytics và lỗi tích hợp | Đào Thị Thanh Bình | Bùi Đức Hưng | 6–10 | [GC-42](https://0703032147khang.atlassian.net/browse/GC-42), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-54](https://0703032147khang.atlassian.net/browse/GC-54) |
| [GC-52](https://0703032147khang.atlassian.net/browse/GC-52) | Screen flow, giao diện và trạng thái cho đủ vai trò | Hoàng Duy Khánh | Nguyễn Khánh Hiền | 6–10 | [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-42](https://0703032147khang.atlassian.net/browse/GC-42) |
| [GC-53](https://0703032147khang.atlassian.net/browse/GC-53) | Contract API, quyền và hợp đồng tích hợp FE–BE–AI | Bùi Đức Hưng | Hoàng Duy Khánh | 6–10 | [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-48](https://0703032147khang.atlassian.net/browse/GC-48), [GC-49](https://0703032147khang.atlassian.net/browse/GC-49), [GC-50](https://0703032147khang.atlassian.net/browse/GC-50), [GC-51](https://0703032147khang.atlassian.net/browse/GC-51) |
| [GC-54](https://0703032147khang.atlassian.net/browse/GC-54) | Nghiên cứu khả thi và phương án đánh giá AI | Đào Thị Thanh Bình | Lê Minh Khang | 6–10 | [GC-38](https://0703032147khang.atlassian.net/browse/GC-38) |
| [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) | Review III–IV, truy vết toàn bộ yêu cầu và duyệt thiết kế | Lê Minh Khang | Đào Thị Thanh Bình | 4–6 | [GC-43](https://0703032147khang.atlassian.net/browse/GC-43), [GC-44](https://0703032147khang.atlassian.net/browse/GC-44), [GC-46](https://0703032147khang.atlassian.net/browse/GC-46), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-48](https://0703032147khang.atlassian.net/browse/GC-48), [GC-49](https://0703032147khang.atlassian.net/browse/GC-49), [GC-50](https://0703032147khang.atlassian.net/browse/GC-50), [GC-51](https://0703032147khang.atlassian.net/browse/GC-51), [GC-52](https://0703032147khang.atlassian.net/browse/GC-52), [GC-53](https://0703032147khang.atlassian.net/browse/GC-53) |

## 5. Backlog Sprint 5

| Jira | Công việc | Chủ trì | Reviewer | Giờ đề xuất | Phụ thuộc |
|---|---|---|---|---|---|
| [GC-56](https://0703032147khang.atlassian.net/browse/GC-56) | Hoàn thiện BE, dữ liệu, JWT/RBAC và API 5 luồng | Bùi Đức Hưng | Nguyễn Khánh Hiền | 24–48 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) |
| [GC-57](https://0703032147khang.atlassian.net/browse/GC-57) | Hoàn thiện ứng dụng Participant và portfolio | Hoàng Duy Khánh | Nguyễn Khánh Hiền | 20–40 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) |
| [GC-58](https://0703032147khang.atlassian.net/browse/GC-58) | Hoàn thiện cổng Organizer/Judge, kết quả và archive | Nguyễn Khánh Hiền | Bùi Đức Hưng | 24–48 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) |
| [GC-59](https://0703032147khang.atlassian.net/browse/GC-59) | Hoàn thiện AI duplicate, detector, tagging và analytics | Đào Thị Thanh Bình | Bùi Đức Hưng | 24–48 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) |
| [GC-60](https://0703032147khang.atlassian.net/browse/GC-60) | Hoàn thiện Admin, danh mục và giám sát vận hành | Lê Minh Khang | Bùi Đức Hưng | 16–32 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) |
| [GC-61](https://0703032147khang.atlassian.net/browse/GC-61) | Tích hợp toàn hệ thống và smoke demo 5 Core Flow | Bùi Đức Hưng | Lê Minh Khang | 12–24 | [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-57](https://0703032147khang.atlassian.net/browse/GC-57), [GC-58](https://0703032147khang.atlassian.net/browse/GC-58), [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60) |
| [GC-62](https://0703032147khang.atlassian.net/browse/GC-62) | V.1–3 Phạm vi, chiến lược và kế hoạch kiểm thử | Lê Minh Khang | Nguyễn Khánh Hiền | 3–5 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55) |
| [GC-63](https://0703032147khang.atlassian.net/browse/GC-63) | Chuẩn bị môi trường test, seed và tài khoản 4 vai trò | Bùi Đức Hưng | Hoàng Duy Khánh | 4–8 | [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-62](https://0703032147khang.atlassian.net/browse/GC-62) |
| [GC-64](https://0703032147khang.atlassian.net/browse/GC-64) | V.4 Unit tests cho quy tắc và tính toàn vẹn | Bùi Đức Hưng | Đào Thị Thanh Bình | 6–12 | [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60), [GC-62](https://0703032147khang.atlassian.net/browse/GC-62) |
| [GC-65](https://0703032147khang.atlassian.net/browse/GC-65) | V.4 Integration/API tests FE–BE–DB–Storage–AI | Hoàng Duy Khánh | Bùi Đức Hưng | 6–12 | [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63), [GC-64](https://0703032147khang.atlassian.net/browse/GC-64) |
| [GC-66](https://0703032147khang.atlassian.net/browse/GC-66) | V.4 System/E2E/UAT đủ 4 vai trò và 5 luồng | Nguyễn Khánh Hiền | Hoàng Duy Khánh | 8–16 | [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65) |
| [GC-67](https://0703032147khang.atlassian.net/browse/GC-67) | Kiểm thử bảo mật, tải đồng thời, lưu trữ và phục hồi | Đào Thị Thanh Bình | Bùi Đức Hưng | 6–12 | [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-62](https://0703032147khang.atlassian.net/browse/GC-62), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63) |
| [GC-68](https://0703032147khang.atlassian.net/browse/GC-68) | Đánh giá độc lập AI và đối chiếu analytics | Đào Thị Thanh Bình | Nguyễn Khánh Hiền | 8–16 | [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63) |
| [GC-69](https://0703032147khang.atlassian.net/browse/GC-69) | V.5 Test Reports, defect log và kiểm thử lại | Nguyễn Khánh Hiền | Lê Minh Khang | 4–8 | [GC-64](https://0703032147khang.atlassian.net/browse/GC-64), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65), [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-67](https://0703032147khang.atlassian.net/browse/GC-67), [GC-68](https://0703032147khang.atlassian.net/browse/GC-68) |
| [GC-70](https://0703032147khang.atlassian.net/browse/GC-70) | VI.2 Triển khai đám mây, smoke và backup/rollback | Bùi Đức Hưng | Đào Thị Thanh Bình | 8–16 | [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-67](https://0703032147khang.atlassian.net/browse/GC-67), [GC-69](https://0703032147khang.atlassian.net/browse/GC-69) |
| [GC-71](https://0703032147khang.atlassian.net/browse/GC-71) | VI.2 Installation Guides tái lập từ máy sạch | Hoàng Duy Khánh | Bùi Đức Hưng | 4–8 | [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70) |
| [GC-72](https://0703032147khang.atlassian.net/browse/GC-72) | VI.3 User Manual cho Participant | Hoàng Duy Khánh | Nguyễn Khánh Hiền | 4–8 | [GC-57](https://0703032147khang.atlassian.net/browse/GC-57), [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70) |
| [GC-73](https://0703032147khang.atlassian.net/browse/GC-73) | VI.3 User Manual Admin, Organizer và Judge | Lê Minh Khang | Nguyễn Khánh Hiền | 6–10 | [GC-58](https://0703032147khang.atlassian.net/browse/GC-58), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60), [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70) |
| [GC-74](https://0703032147khang.atlassian.net/browse/GC-74) | VI.1 Deliverable Package và liên kết bàn giao | Đào Thị Thanh Bình | Lê Minh Khang | 4–8 | [GC-69](https://0703032147khang.atlassian.net/browse/GC-69), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70), [GC-71](https://0703032147khang.atlassian.net/browse/GC-71), [GC-72](https://0703032147khang.atlassian.net/browse/GC-72), [GC-73](https://0703032147khang.atlassian.net/browse/GC-73) |
| [GC-75](https://0703032147khang.atlassian.net/browse/GC-75) | Tổng hợp I–VI, slide, demo và nghiệm thu toàn bài | Lê Minh Khang | Đào Thị Thanh Bình | 6–12 | [GC-55](https://0703032147khang.atlassian.net/browse/GC-55), [GC-69](https://0703032147khang.atlassian.net/browse/GC-69), [GC-74](https://0703032147khang.atlassian.net/browse/GC-74) |

## 6. Truy vết chương báo cáo và toàn bộ phạm vi

| Phần mẫu | Công việc GC | Kế thừa / đầu ra |
|---|---|---|
| I–II và III.1–3.1 | GC-2..36; review tổng thể GC-75 | Giữ nội dung đã có, chỉ sửa gap có nguồn qua review |
| III.3.2 | GC-39..42 | Đặc tả chi tiết đủ 4 vai trò, 5 luồng và AI |
| III.4 | GC-43 | NFR, nguồn, thước đo và kế hoạch xác minh |
| III.5.1–5.3 | GC-44 | Business Rules, Common Requirements, Messages |
| IV.1.1–1.2 | GC-45..46 | Kiến trúc và package diagram |
| IV.2 | GC-47 | ERD, entity và data dictionary |
| IV.3 | GC-48..51; GC-52..53 | Class/sequence/bảng lớp; UI/API contract |
| Review thiết kế | GC-54..55 | Khả thi AI và duyệt coverage |
| Tiền đề sản phẩm cho V–VI | GC-56..61 | Code/integration còn thiếu; không giả là đã hoàn thành |
| V.1–3 | GC-62..63 | Scope, strategy, plan, environment |
| V.4 | GC-64..68 | Unit, integration, E2E/UAT, NFR và AI evaluation |
| V.5 | GC-69 | Actual results, defects, retest và limitations |
| VI.1 | GC-74 | Deliverable manifest/liên kết thật |
| VI.2 | GC-70..71 | Deploy, backup/rollback, installation |
| VI.3 | GC-72..73 | User Manual đủ 4 vai trò |
| Tổng hợp I–VI | GC-75 | Báo cáo, slide, demo và nghiệm thu nội bộ |

| Yêu cầu hiện hành | Đặc tả/thiết kế | Implementation dự kiến | Kiểm tra/bàn giao |
|---|---|---|---|
| FR-01 Authentication/RBAC | GC-39,48,53 | GC-56,57,58,60 | GC-64..67,70..73 |
| FR-02 Admin/master data | GC-39,48 | GC-56,60 | GC-64..67,73 |
| FR-03 Contest/template | GC-41,50 | GC-56,58 | GC-64..67,73 |
| FR-04 Enrollment | GC-40,49 | GC-56..58 | GC-64..67,72..73 |
| FR-05 FilmRoll/Frame/metadata | GC-40,47,49 | GC-56..57 | GC-64..67,72 |
| FR-06 Photo submission | GC-40,49,53 | GC-56..57 | GC-64..67,72 |
| FR-07 Validation | GC-41,50 | GC-56,58 | GC-64..67,73 |
| FR-08 Manual review | GC-41,50 | GC-56,58 | GC-64..67,73 |
| FR-09 Judge assignment | GC-41,50 | GC-56,58 | GC-64..67,73 |
| FR-10 Multi-round scoring | GC-41,50 | GC-56,58 | GC-64..67,73 |
| FR-11 Ranking/publication | GC-41,50 | GC-56,58 | GC-64..67,72..73 |
| FR-12 Archive/exhibition | GC-41,50 | GC-56,58 | GC-65..67,70,73 |
| FR-13 Duplicate/similarity | GC-42,51,54 | GC-59 | GC-65,66,68,73 |
| FR-14 AI-generated warning | GC-42,51,54 | GC-59 | GC-65,66,68,73 |
| FR-15 Tagging/categorization | GC-42,51,54 | GC-59 | GC-65,66,68 |
| FR-16 Analytics/report | GC-42,51,54 | GC-56,58,59 | GC-65,66,68,73 |
| FR-17 Profile/portfolio/history | GC-40,49 | GC-56,57 | GC-65..66,72 |
| FR-18 Notifications/audit (phần được duyệt) | GC-39,42,44,48,51 | GC-56,58,60 | GC-64..67,73 |
| NFR-01..08 | GC-43,45..47,53,55 | Chủ module liên quan | GC-62..63,67,69..71 |

Bảng là **planned coverage**, không khẳng định Implemented/Verified. FR-18 và các quyền/BR được nhóm suy luận phải giữ nhãn nguồn và quyết định phê duyệt. Không dùng bảng để tự tạo thêm kênh email/push hoặc yêu cầu không có trong đề tài.

## 7. Definition of Ready / Done

**Trước khi bắt đầu:** có input/nguồn, owner/reviewer, tiêu chí nghiệm thu, phụ thuộc và giờ khả dụng; luật blocker như công thức điểm/Web-Mobile/AI đã được chốt hoặc có cách xử lý được duyệt. Có thể chuẩn bị nháp trong khi chờ input, nhưng không chốt tài liệu hoặc nghiệm thu phần phụ thuộc.

**Hoàn thành tài liệu:** đối chiếu nguồn, link FR/BR/Entity/UI/API/TC/Jira, output nhất quán, ví dụ thành công và lỗi có expected, PR peer review; TBD blocker xử lý hoặc ghi rõ chưa đạt. Không đồng nhất có file với đã duyệt.

**Hoàn thành phần mềm:** code/commit thật, build/run tái lập, kiểm tra phù hợp và evidence. Test run có expected/actual, environment, commit, thời gian và link lỗi. PASS/FAIL/BLOCKED/NOT RUN đúng thực tế. Deploy cần URL thật, version và smoke; guide dùng ảnh thật của phiên bản bàn giao, prototype phải ghi nhãn.

**Bảo mật:** không commit .env, token, API key, tài khoản dùng thật, ảnh riêng tư hoặc credentials cloud; dữ liệu ảnh phải có quyền sử dụng. AI chỉ gắn cờ/hỗ trợ; không tự loại bài chỉ bằng detector. Không giả accuracy hoặc test/deploy evidence.

## 8. Chi tiết đầu ra và tiêu chí từng task

### GC-38 — [S4] Chốt phạm vi, quyết định còn mở và kế hoạch toàn bài

- Mã kế hoạch: S4-01; requirement: FR-01..18; NFR-01..08.
- Chủ trì: Lê Minh Khang; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 4–6 giờ.
- Phụ thuộc: [GC-36](https://0703032147khang.atlassian.net/browse/GC-36).
- Vị trí đầu ra: `Docs/01-governance/open-questions.md; kế hoạch Sprint 4–5`.

Đối chiếu đề tài gốc, mẫu PDCMS và repo; xác nhận Web/Mobile, stack, đơn vị bài nộp, metadata, công thức điểm/đồng điểm, nhiều vòng, quyền sửa, AI, công khai/lưu trữ; rà giới hạn đề xuất chưa được duyệt. Ghi owner, nguồn xác nhận và tác động. Xác nhận giờ rảnh từng thành viên, chia nhỏ gói triển khai và điều chỉnh tải trước khi bắt đầu sprint.

**Tiêu chí:** Quyết định có bằng chứng hoặc giữ TBD và chỉ rõ phần bị chặn; không tự cắt Mobile/multi-round/AI. Có bảng giờ khả dụng, ước lượng và tải từng người; chưa có lịch nộp thì không điền ngày giả.

### GC-39 — [S4] III.3.2 Đặc tả xác thực và quản trị hệ thống

- Mã kế hoạch: S4-02; requirement: FR-01, FR-02.
- Chủ trì: Lê Minh Khang; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 4–6 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38).
- Vị trí đầu ra: `Docs/02-requirements/functional/FR-01-authentication-rbac.md; FR-02-admin-masterdata-audit.md`.

Kế thừa GC-26/27/30; hoàn thiện đặc tả đăng ký/xác thực, quản lý người dùng, quyền, danh mục, cấu hình và giám sát. Mỗi chức năng có actor, tiền/hậu điều kiện, dữ liệu, màn hình, luồng chính, lỗi và tiêu chí nghiệm thu.

**Tiêu chí:** Bao phủ Admin và kiểm tra quyền phía server, theo chủ sở hữu/cuộc thi; có ví dụ đúng quyền và sai quyền. Chính sách token và quyền quản trị chưa rõ được ghi TBD.

### GC-40 — [S4] III.3.2 Đặc tả toàn bộ nghiệp vụ thí sinh

- Mã kế hoạch: S4-03; requirement: FR-04,05,06,17.
- Chủ trì: Hoàng Duy Khánh; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 6–8 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38).
- Vị trí đầu ra: `Docs/02-requirements/functional/; FE/README.md`.

Kế thừa GC-28/34; đặc tả hồ sơ/portfolio, đăng ký, cuộn phim, khung hình, toàn bộ metadata, bản scan, minh chứng tùy chọn, theo dõi trạng thái/kết quả/phản hồi. Nêu quyền chỉnh sửa/rút/nộp lại là quyết định cần xác nhận.

**Tiêu chí:** Có trường dữ liệu, quy tắc, luồng chính và lỗi upload/deadline/khác chủ sở hữu; giữ liên kết Roll–Frame–Submission. Không tự đặt số ảnh, định dạng hoặc dung lượng.

### GC-41 — [S4] III.3.2 Đặc tả tổ chức, duyệt bài, chấm và công bố

- Mã kế hoạch: S4-04; requirement: FR-03,07,08,09,10,11,12.
- Chủ trì: Nguyễn Khánh Hiền; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38).
- Vị trí đầu ra: `Docs/02-requirements/functional/; Docs/00-overview/end-to-end-flows.md`.

Kế thừa GC-29/33/35; bao phủ cấu hình và mẫu tái sử dụng, kiểm tra tự động, duyệt tay, phân công theo vòng/hạng mục, chấm đa tiêu chí nhiều vòng, chốt, xếp hạng, giải thưởng, công bố và triển lãm/kho lưu trữ.

**Tiêu chí:** Mỗi chức năng có tiền/hậu điều kiện, quyền, trạng thái, ngoại lệ và AC; không mặc định điểm trung bình, tie-break, mở lại điểm hoặc một vòng duy nhất khi chưa duyệt.

### GC-42 — [S4] III.3.2 Đặc tả bốn năng lực AI và báo cáo

- Mã kế hoạch: S4-05; requirement: FR-13,14,15,16; phần hỗ trợ FR-18.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 4–8 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38).
- Vị trí đầu ra: `Docs/02-requirements/functional/FR-13..16; Docs/06-feasibility/`.

Đặc tả duplicate/similarity, cảnh báo AI-generated, tagging/categorization và analytics/report export: input/output, người dùng, lỗi, chi phí/dữ liệu, quyền và cách đánh giá. Rà thông báo/audit của FR-18 để tách đề tài với đề xuất, không tự bắt buộc email/push.

**Tiêu chí:** Đủ bốn năng lực; AI chỉ hỗ trợ và có đường kiểm duyệt người. Làm rõ giới hạn và trạng thái lỗi; chưa có số đo thì không công bố accuracy.

### GC-43 — [S4] III.4 Yêu cầu phi chức năng và kế hoạch đo

- Mã kế hoạch: S4-06; requirement: NFR-01..08; JWT/RBAC/cloud/modularity/concurrency.
- Chủ trì: Bùi Đức Hưng; reviewer: Đào Thị Thanh Bình; ước lượng đề xuất: 3–5 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38).
- Vị trí đầu ra: `Docs/02-requirements/non-functional/`.

Phân loại ràng buộc trực tiếp ở 3.2.d với chất lượng do nhóm đề xuất; mô tả bảo mật, lưu ảnh, mô-đun, nhiều cuộc thi/chấm đồng thời; lập cách đo, môi trường và dữ liệu.

**Tiêu chí:** Mỗi NFR có nguồn, thước đo, cách xác minh và owner. Các con số chưa xác nhận được ghi Proposed/TBD; không sao chép SLA/stack/chỉ tiêu của hệ thống nha khoa.

### GC-44 — [S4] III.5 Business Rules, Common Requirements và Messages

- Mã kế hoạch: S4-07; requirement: FR-01..18; BR và decision log.
- Chủ trì: Lê Minh Khang; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 4–6 giờ.
- Phụ thuộc: [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-42](https://0703032147khang.atlassian.net/browse/GC-42).
- Vị trí đầu ra: `Docs/03-domain/business-rules.md; Docs/03-domain/business-decision-tables.md; phụ lục messages`.

Lập ba phần III.5.1–5.3 theo cấu trúc mẫu: quy tắc có nguồn, quy tắc cần chốt, yêu cầu chung và danh mục thông báo cho 4 vai trò/5 luồng. Liên kết mã thông báo với tình huống và cách phục hồi.

**Tiêu chí:** Không nhập luật nha khoa hoặc giới hạn 10MB từ mẫu. Mỗi BR có nguồn/trạng thái; thông báo thân thiện, không lộ token/chi tiết hệ thống; có tình huống thành công và lỗi.

### GC-45 — [S4] IV.1.1 Thiết kế kiến trúc toàn hệ thống

- Mã kế hoạch: S4-08; requirement: FR-01..18; NFR-01..08.
- Chủ trì: Bùi Đức Hưng; reviewer: Đào Thị Thanh Bình; ước lượng đề xuất: 4–6 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38), [GC-43](https://0703032147khang.atlassian.net/browse/GC-43).
- Vị trí đầu ra: `BE/README.md; FE/README.md; Docs/03-domain/architecture.md (đề xuất bổ sung)`.

Vẽ kiến trúc tổng thể, FE/BE/DB/Object Storage/AI, ranh giới tin cậy, luồng ảnh và metadata; mô tả xác thực, phân quyền, xử lý đồng thời, lỗi dịch vụ, sao lưu. Thiết kế Mobile theo phạm vi được xác nhận.

**Tiêu chí:** Sơ đồ và mô tả khớp; có lý do chọn công nghệ và phương án lỗi. Không áp stack Spring Boot/Redis/Cloudinary của mẫu vào dự án nếu nhóm chưa chọn.

### GC-46 — [S4] IV.1.2 Package Diagram và trách nhiệm mô-đun

- Mã kế hoạch: S4-09; requirement: FR-01..18; NFR modularity.
- Chủ trì: Bùi Đức Hưng; reviewer: Hoàng Duy Khánh; ước lượng đề xuất: 3–5 giờ.
- Phụ thuộc: [GC-45](https://0703032147khang.atlassian.net/browse/GC-45).
- Vị trí đầu ra: `BE/README.md; FE/README.md; sơ đồ package dưới Docs/03-domain/`.

Mô tả các gói FE, BE, AI và phụ thuộc; giữ cấu trúc gốc Docs/BE/FE/Skills của repo. Tách xác thực, cuộc thi, bài nộp, review, judging, archive và AI.

**Tiêu chí:** Có sơ đồ package, trách nhiệm, giao diện và quy tắc phụ thuộc; tên gói nhất quán với kiến trúc và kế hoạch source. Không tạo code giả để minh họa triển khai.

### GC-47 — [S4] IV.2 Thiết kế CSDL, ERD và từ điển dữ liệu

- Mã kế hoạch: S4-10; requirement: FR-01..18; Roll–Frame–Submission.
- Chủ trì: Bùi Đức Hưng; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-42](https://0703032147khang.atlassian.net/browse/GC-42), [GC-44](https://0703032147khang.atlassian.net/browse/GC-44).
- Vị trí đầu ra: `Docs/03-domain/erd.md; entities.md; data-dictionary.md`.

Review các entity đã có; ERD, khóa chính/ngoại, cardinality, ràng buộc, indexes đề xuất, lịch sử chấm, metadata và lưu tài nguyên. Phân biệt thiết kế đề xuất với migration đã thực thi.

**Tiêu chí:** Đủ dữ liệu cho 5 luồng/AI/report/portfolio/admin; quan hệ không mâu thuẫn FR. Có bảng thuộc tính/kiểu/null/constraint; dữ liệu chưa chốt giữ TBD; không tuyên bố đã tạo DB.

### GC-48 — [S4] IV.3 Thiết kế chi tiết xác thực và Admin

- Mã kế hoạch: S4-11; requirement: FR-01,02.
- Chủ trì: Lê Minh Khang; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 4–6 giờ.
- Phụ thuộc: [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47).
- Vị trí đầu ra: `Docs/03-domain/; BE/README.md; FE/README.md`.

Lập class diagram, sequence diagram và bảng lớp/phương thức cho đăng nhập, quyền, quản lý người dùng/danh mục/cấu hình/giám sát; mô tả lỗi không đủ quyền hoặc phiên không hợp lệ.

**Tiêu chí:** Mỗi use case trong phạm vi Admin/auth được thiết kế hoặc liên kết tới mẫu dùng chung với lý do; sơ đồ chứa cả đường thành công và từ chối quyền; tên lớp khớp data/API.

### GC-49 — [S4] IV.3 Thiết kế chi tiết Participant và nộp ảnh

- Mã kế hoạch: S4-12; requirement: FR-04,05,06,17.
- Chủ trì: Hoàng Duy Khánh; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47).
- Vị trí đầu ra: `Docs/03-domain/; FE/README.md; BE/README.md`.

Class/sequence và bảng mô tả cho hồ sơ, portfolio, đăng ký, roll/frame, upload/finalize, trạng thái/kết quả. Tách lưu ảnh với tạo bài, mô tả timeout/retry và quyền sở hữu theo quyết định đã duyệt.

**Tiêu chí:** Mỗi chức năng Participant có liên kết thiết kế; upload thành công không bị nhầm với nộp bài thành công. Có lỗi storage, mạng, metadata, deadline và truy cập bài người khác.

### GC-50 — [S4] IV.3 Thiết kế chi tiết Organizer, Judge và archive

- Mã kế hoạch: S4-13; requirement: FR-03,07,08,09,10,11,12.
- Chủ trì: Nguyễn Khánh Hiền; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 8–12 giờ.
- Phụ thuộc: [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-44](https://0703032147khang.atlassian.net/browse/GC-44), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47).
- Vị trí đầu ra: `Docs/03-domain/state-transitions.md; business-invariants.md; BE/FE README`.

Class/sequence/bảng lớp cho cấu hình+mẫu, validation/review, assignment, chấm nhiều vòng, tổng hợp/xếp hạng, công bố, giải thưởng/triển lãm/archive. Mô tả đồng thời, lịch sử và quyết định còn mở.

**Tiêu chí:** Các use case liên quan đều được map; công thức chưa chốt không được giả thành chính thức. Có đường lỗi thiếu phiếu, xung đột cập nhật, sai phân công và xem kết quả chưa công bố.

### GC-51 — [S4] IV.3 Thiết kế chi tiết AI, analytics và lỗi tích hợp

- Mã kế hoạch: S4-14; requirement: FR-13,14,15,16; FR-18 hỗ trợ.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-42](https://0703032147khang.atlassian.net/browse/GC-42), [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-54](https://0703032147khang.atlassian.net/browse/GC-54).
- Vị trí đầu ra: `Docs/03-domain/; Docs/05-quality/failure-recovery-matrix.md`.

Class/sequence và contract xử lý 4 năng lực AI, dữ liệu đầu vào, kết quả/phiên bản, thời gian chờ, retry và truy vết. Đề xuất xử lý nền/fallback, đánh dấu phần cần duyệt.

**Tiêu chí:** Sơ đồ và contract có trạng thái unavailable/failed; không có nhánh tự loại bài chỉ vì detector. Báo cáo thống kê đối chiếu nguồn dữ liệu; thiết kế bảo vệ ảnh chưa công khai.

### GC-52 — [S4] Screen flow, giao diện và trạng thái cho đủ vai trò

- Mã kế hoạch: S4-15; requirement: FR-01..18.
- Chủ trì: Hoàng Duy Khánh; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-39](https://0703032147khang.atlassian.net/browse/GC-39), [GC-40](https://0703032147khang.atlassian.net/browse/GC-40), [GC-41](https://0703032147khang.atlassian.net/browse/GC-41), [GC-42](https://0703032147khang.atlassian.net/browse/GC-42).
- Vị trí đầu ra: `FE/README.md; danh sách màn hình/flow/wireframe dưới Docs/`.

Kế thừa screen overview ở III.3.1; hoàn thiện màn hình, điều hướng và dữ liệu cho Participant/Organizer/Judge/Admin, triển lãm và kết quả; trạng thái loading/empty/error/conflict/success.

**Tiêu chí:** Màn hình map tới FR/quyền/API; có mockup có nhãn thiết kế, không dùng screenshot của nhóm khác làm sản phẩm mình. Phạm vi Mobile theo D-01, chưa có quyết định thì ghi nhánh thiết kế TBD.

### GC-53 — [S4] Contract API, quyền và hợp đồng tích hợp FE–BE–AI

- Mã kế hoạch: S4-16; requirement: FR-01..18.
- Chủ trì: Bùi Đức Hưng; reviewer: Hoàng Duy Khánh; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-45](https://0703032147khang.atlassian.net/browse/GC-45), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-48](https://0703032147khang.atlassian.net/browse/GC-48), [GC-49](https://0703032147khang.atlassian.net/browse/GC-49), [GC-50](https://0703032147khang.atlassian.net/browse/GC-50), [GC-51](https://0703032147khang.atlassian.net/browse/GC-51).
- Vị trí đầu ra: `BE/README.md; FE/README.md; OpenAPI đề xuất dưới Docs/`.

Chuẩn hóa endpoint/request/response, role/owner/assignment scope, validation, lỗi, file access và AI contract. Lập kế hoạch mock rồi chuyển sang API thật; đánh dấu mock và endpoint dự kiến.

**Tiêu chí:** Đủ hợp đồng cho 5 luồng/4 role/AI/admin/portfolio/report; payload nhất quán ERD và sequence. Swagger/URL chỉ ghi đã chạy khi có nguồn thật.

### GC-54 — [S4] Nghiên cứu khả thi và phương án đánh giá AI

- Mã kế hoạch: S4-17; requirement: FR-13,14,15,16; RBL ở đề tài.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Lê Minh Khang; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-38](https://0703032147khang.atlassian.net/browse/GC-38).
- Vị trí đầu ra: `Docs/06-feasibility/feasibility-study.md; capacity-estimation.md; risk-register.md`.

Khảo sát phương pháp, dữ liệu có quyền sử dụng, chi phí, hạn mức và cách đo cho 4 năng lực AI. Xác nhận có bắt buộc huấn luyện. Đề xuất thử nghiệm nhỏ có nhãn nguồn rõ nếu được phép.

**Tiêu chí:** Có phương án khả thi, giới hạn, dữ liệu/chi phí/đánh giá và rủi ro; chưa chạy thì chỉ ghi kế hoạch. Không hứa xác định chắc ảnh AI hoặc tự bỏ một năng lực.

### GC-55 — [S4] Review III–IV, truy vết toàn bộ yêu cầu và duyệt thiết kế

- Mã kế hoạch: S4-18; requirement: FR-01..18; NFR-01..08; GC-25..36.
- Chủ trì: Lê Minh Khang; reviewer: Đào Thị Thanh Bình; ước lượng đề xuất: 4–6 giờ.
- Phụ thuộc: [GC-43](https://0703032147khang.atlassian.net/browse/GC-43), [GC-44](https://0703032147khang.atlassian.net/browse/GC-44), [GC-46](https://0703032147khang.atlassian.net/browse/GC-46), [GC-47](https://0703032147khang.atlassian.net/browse/GC-47), [GC-48](https://0703032147khang.atlassian.net/browse/GC-48), [GC-49](https://0703032147khang.atlassian.net/browse/GC-49), [GC-50](https://0703032147khang.atlassian.net/browse/GC-50), [GC-51](https://0703032147khang.atlassian.net/browse/GC-51), [GC-52](https://0703032147khang.atlassian.net/browse/GC-52), [GC-53](https://0703032147khang.atlassian.net/browse/GC-53).
- Vị trí đầu ra: `Docs/04-traceability/; Docs/07-review/report-map.md; bản báo cáo III–IV`.

Review chéo đặc tả/thiết kế, link nguồn→FR→BR→Entity→UI/API→test dự kiến→Jira. Hoàn chỉnh phần III.3.2, III.4–5 và IV.1–3, không làm lại I–III.3.1 đã Done nếu không có gap cụ thể.

**Tiêu chí:** 18 FR/8 NFR đều có coverage hoặc gap/blocked rõ; không đổi mã FR hiện có sang mã phân tích khác. Có PR và bằng chứng review, báo cáo đọc được; chưa chứng nhận phần mềm hoạt động.

### GC-56 — [S5] Hoàn thiện BE, dữ liệu, JWT/RBAC và API 5 luồng

- Mã kế hoạch: S5-01; requirement: FR-01..12,16..18.
- Chủ trì: Bùi Đức Hưng; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 24–48 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55).
- Vị trí đầu ra: `BE/; migrations/seed/API/test theo thiết kế đã duyệt`.

Xác minh source hiện có; bổ sung phần còn thiếu của backend, metadata, kiểm tra thể lệ, phân quyền scope, scoring nhiều vòng, archive và report. Chủ trì TV2, các owner module cùng thực hiện; tách subtask theo capacity trước coding.

**Tiêu chí:** Có code thật, migration/seed tái lập, lệnh run/build và kiểm tra nghiệp vụ/quyền; API khớp contract. Nếu stack/BR chưa chốt thì giữ blocked, không cài mặc định theo mẫu.

### GC-57 — [S5] Hoàn thiện ứng dụng Participant và portfolio

- Mã kế hoạch: S5-02; requirement: FR-01,04,05,06,17.
- Chủ trì: Hoàng Duy Khánh; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 20–40 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55).
- Vị trí đầu ra: `FE/; ứng dụng Mobile nếu được yêu cầu`.

Xác minh và hoàn thiện giao diện hồ sơ/portfolio, đăng ký, roll/frame/scan/minh chứng, gửi bài, trạng thái và phản hồi. Làm đúng Web/Mobile đã chốt; mock chỉ phục vụ phát triển và phải được thay khi nghiệm thu.

**Tiêu chí:** PR và bản chạy được; đi qua hành trình Participant với API thật; xử lý upload fail, sai quyền, lỗi trường, hết hạn. Không ghi mobile hoàn thành nếu chỉ có web.

### GC-58 — [S5] Hoàn thiện cổng Organizer/Judge, kết quả và archive

- Mã kế hoạch: S5-03; requirement: FR-03,07,08,09,10,11,12.
- Chủ trì: Nguyễn Khánh Hiền; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 24–48 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55).
- Vị trí đầu ra: `FE/; phần API module phối hợp TV2`.

Hoàn thiện cấu hình+mẫu, danh sách đăng ký/duyệt bài, AI flags, phân công, chấm nhiều vòng, chốt, xếp hạng/giải thưởng/công bố/triển lãm/archive. Review chính sách điểm và sửa trạng thái trước làm.

**Tiêu chí:** Cổng chạy API thật; 5 Core Flow bao phủ; giới hạn quyền và kết quả chưa công bố được giữ đúng. Có PR, demo và lỗi được ghi nhận, không tự giả công thức.

### GC-59 — [S5] Hoàn thiện AI duplicate, detector, tagging và analytics

- Mã kế hoạch: S5-04; requirement: FR-13,14,15,16.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 24–48 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55).
- Vị trí đầu ra: `Mô-đun AI trong cấu trúc được nhóm thống nhất; Docs/06-feasibility/`.

Tích hợp đủ bốn năng lực đã xác nhận với BE/FE; ghi phiên bản/phương pháp, môi trường, chi phí và xử lý lỗi. Thu thập dữ liệu có quyền và nguồn đối chiếu; huấn luyện nếu thuộc yêu cầu đã chốt.

**Tiêu chí:** Có code/dịch vụ thật, đầu vào/đầu ra, log và đánh giá tái lập; bản thử nghiệm phải ghi rõ và được chấp thuận nếu thay cho triển khai. Không tự loại bài theo kết quả AI.

### GC-60 — [S5] Hoàn thiện Admin, danh mục và giám sát vận hành

- Mã kế hoạch: S5-05; requirement: FR-01,02; phần được duyệt của FR-18.
- Chủ trì: Lê Minh Khang; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 16–32 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55).
- Vị trí đầu ra: `FE/; BE/ phần quản trị phối hợp TV2`.

Hoàn thiện trang quản trị người dùng/quyền, cấu hình/danh mục, giám sát và truy vết theo phạm vi đã xác nhận. Cùng TV2 thực hiện API quản trị, tránh chỉ có UI.

**Tiêu chí:** Admin quản lý đúng dữ liệu/quyền, thao tác nhạy cảm có kiểm tra và lịch sử theo đặc tả; tài khoản khác không dùng được API Admin. Có PR/bản chạy và review.

### GC-61 — [S5] Tích hợp toàn hệ thống và smoke demo 5 Core Flow

- Mã kế hoạch: S5-06; requirement: FR-01..18; 4 vai trò; 5 Core Flow.
- Chủ trì: Bùi Đức Hưng; reviewer: Lê Minh Khang; ước lượng đề xuất: 12–24 giờ.
- Phụ thuộc: [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-57](https://0703032147khang.atlassian.net/browse/GC-57), [GC-58](https://0703032147khang.atlassian.net/browse/GC-58), [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60).
- Vị trí đầu ra: `BE/; FE/; AI; Docs/04-traceability/`.

Tích hợp giao diện, backend, DB, storage và AI; dùng tài khoản đủ 4 vai trò, tạo cuộc thi→đăng ký/nộp→kiểm duyệt→chấm→công bố/archive. Xác minh các module hỗ trợ admin/portfolio/report.

**Tiêu chí:** Có build/commit/môi trường và log smoke cho đủ 5 luồng. Các lỗi tạo bug; thiếu source/phụ thuộc thì báo blocked, không dùng bản mô phỏng làm evidence tích hợp thật.

### GC-62 — [S5] V.1–3 Phạm vi, chiến lược và kế hoạch kiểm thử

- Mã kế hoạch: S5-07; requirement: FR-01..18; NFR-01..08.
- Chủ trì: Lê Minh Khang; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 3–5 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55).
- Vị trí đầu ra: `Docs/05-quality/test-strategy.md; test plan`.

Viết In-Scope, Out-of-Scope có căn cứ, assumptions, test types/levels/tools, nhân sự, môi trường và mốc theo phụ thuộc. Tách unit/integration/system/acceptance và tiêu chí vào/ra.

**Tiêu chí:** Không tự bỏ AI/Mobile/nhiều vòng ra khỏi kiểm thử. Chỉ tiêu Proposed/TBD được duyệt trước đánh giá; không sao chép ngày, 70% coverage hoặc kiểm thử production từ mẫu.

### GC-63 — [S5] Chuẩn bị môi trường test, seed và tài khoản 4 vai trò

- Mã kế hoạch: S5-08; requirement: FR-01..18; privacy/storage.
- Chủ trì: Bùi Đức Hưng; reviewer: Hoàng Duy Khánh; ước lượng đề xuất: 4–8 giờ.
- Phụ thuộc: [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-62](https://0703032147khang.atlassian.net/browse/GC-62).
- Vị trí đầu ra: `Môi trường test riêng; seed có quyền sử dụng; Docs/05-quality/`.

Cấu hình môi trường tái lập, tài khoản 4 vai trò, dữ liệu nhiều cuộc thi/round/category/roll/frame và tập ảnh có nguồn. Không dùng dữ liệu cá nhân thật hoặc secrets công khai.

**Tiêu chí:** Có hướng dẫn reset và seed, commit cấu hình mẫu không chứa secret; kiểm thử sai chủ sở hữu/sai cuộc thi có dữ liệu chuẩn. URL/credentials riêng tư được phân phối ngoài repo public.

### GC-64 — [S5] V.4 Unit tests cho quy tắc và tính toàn vẹn

- Mã kế hoạch: S5-09; requirement: FR-01,05..11; BR; NFR dữ liệu.
- Chủ trì: Bùi Đức Hưng; reviewer: Đào Thị Thanh Bình; ước lượng đề xuất: 6–12 giờ.
- Phụ thuộc: [GC-56](https://0703032147khang.atlassian.net/browse/GC-56), [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60), [GC-62](https://0703032147khang.atlassian.net/browse/GC-62).
- Vị trí đầu ra: `BE/tests và kiểm thử module FE/AI tương ứng; Docs/05-quality/`.

Cả nhóm viết/chạy test phần mình: metadata/điều kiện nộp, quyền, state transitions, công thức điểm đã duyệt, retry/idempotency nếu có trong thiết kế. Không viết test chỉ sao lại implementation.

**Tiêu chí:** Mỗi test có input/expected/actual; log gắn commit và môi trường; pass/fail/not-run đúng thực tế. Công thức chưa duyệt là blocker, không dùng đáp án tự nghĩ.

### GC-65 — [S5] V.4 Integration/API tests FE–BE–DB–Storage–AI

- Mã kế hoạch: S5-10; requirement: FR-01..18.
- Chủ trì: Hoàng Duy Khánh; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 6–12 giờ.
- Phụ thuộc: [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63), [GC-64](https://0703032147khang.atlassian.net/browse/GC-64).
- Vị trí đầu ra: `Docs/05-quality/test-matrix.md; tập request/test scripts thực`.

Chạy kiểm thử liên kết thật, response schema, lưu/đọc ảnh+metadata, phân quyền giữa cuộc thi, AI timeout, lỗi storage/DB, retry và khôi phục. Mô phỏng lỗi phải ghi cách gây lỗi.

**Tiêu chí:** Có expected/actual, commit/env/time và evidence; phân biệt mock test với live integration. Không có lỗi mất dữ liệu/quyền nghiêm trọng bị giấu khỏi báo cáo.

### GC-66 — [S5] V.4 System/E2E/UAT đủ 4 vai trò và 5 luồng

- Mã kế hoạch: S5-11; requirement: FR-01..18; toàn bộ 5 Core Flow.
- Chủ trì: Nguyễn Khánh Hiền; reviewer: Hoàng Duy Khánh; ước lượng đề xuất: 8–16 giờ.
- Phụ thuộc: [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65).
- Vị trí đầu ra: `Docs/05-quality/e2e-test-cases.md; business-scenario-tests.md`.

Chạy hành trình thành công và ngoại lệ: cấu hình mẫu, đăng ký/nộp, thẩm định, nhiều vòng/điểm, kết quả/archive; kiểm thêm Admin/portfolio/report. UAT là kiểm tra chấp nhận theo tiêu chí đã duyệt.

**Tiêu chí:** Bao phủ từng FR, vai trò và luồng; có dữ liệu/steps/expected/actual/evidence. Không tuyên bố giảng viên chấp nhận nếu chỉ nhóm tự chạy.

### GC-67 — [S5] Kiểm thử bảo mật, tải đồng thời, lưu trữ và phục hồi

- Mã kế hoạch: S5-12; requirement: NFR-01..08; FR-01/06/10/11/12.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 6–12 giờ.
- Phụ thuộc: [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-62](https://0703032147khang.atlassian.net/browse/GC-62), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63).
- Vị trí đầu ra: `Docs/02-requirements/non-functional/; Docs/05-quality/`.

Đo theo chỉ tiêu đã chốt: JWT/RBAC/owner/assignment, ảnh private, upload, chấm đồng thời, tài nguyên, nhiều cuộc thi; thử backup/restore. Dùng dữ liệu giả, môi trường được phép.

**Tiêu chí:** Có kịch bản, công cụ/cấu hình, số đo thật và giới hạn; thiếu mục tiêu ghi chưa kết luận. Không áp số 50 users/99.9% từ mẫu thành yêu cầu dự án.

### GC-68 — [S5] Đánh giá độc lập AI và đối chiếu analytics

- Mã kế hoạch: S5-13; requirement: FR-13,14,15,16.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 8–16 giờ.
- Phụ thuộc: [GC-59](https://0703032147khang.atlassian.net/browse/GC-59), [GC-63](https://0703032147khang.atlassian.net/browse/GC-63).
- Vị trí đầu ra: `Docs/06-feasibility/; Docs/05-quality/; dataset manifest`.

Đánh giá ảnh trùng/tương đồng, ảnh có/không do AI tạo, tagging, báo cáo thống kê. Tách dữ liệu đánh giá khỏi huấn luyện nếu có; phân tích cảnh báo sai/bỏ sót và hiệu ứng bản scan ảnh phim.

**Tiêu chí:** Có nguồn/quyền dữ liệu, phương pháp, kết quả tái lập và lỗi mẫu; detector không được tuyên bố chắc chắn. Số liệu report khớp dữ liệu nguồn; không bịa accuracy.

### GC-69 — [S5] V.5 Test Reports, defect log và kiểm thử lại

- Mã kế hoạch: S5-14; requirement: FR-01..18; NFR-01..08.
- Chủ trì: Nguyễn Khánh Hiền; reviewer: Lê Minh Khang; ước lượng đề xuất: 4–8 giờ.
- Phụ thuộc: [GC-64](https://0703032147khang.atlassian.net/browse/GC-64), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65), [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-67](https://0703032147khang.atlassian.net/browse/GC-67), [GC-68](https://0703032147khang.atlassian.net/browse/GC-68).
- Vị trí đầu ra: `Docs/05-quality/; bug issues Jira GC; báo cáo Chương V`.

Tổng hợp test run, coverage, PASS/FAIL/BLOCKED/NOT RUN, bug severity, người sửa và retest. Mỗi bug có steps, expected/actual, commit/env/evidence; owner module sửa lỗi.

**Tiêu chí:** Bug được xử lý hoặc ghi hạn chế được chấp nhận; kết quả thống nhất số liệu. Không điền sẵn 100% PASS; chưa chạy giữ Not Run. Chương V có bằng chứng và known issues.

### GC-70 — [S5] VI.2 Triển khai đám mây, smoke và backup/rollback

- Mã kế hoạch: S5-15; requirement: Đề tài 3.2.d/f/g; NFR; 5 Core Flow.
- Chủ trì: Bùi Đức Hưng; reviewer: Đào Thị Thanh Bình; ước lượng đề xuất: 8–16 giờ.
- Phụ thuộc: [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-67](https://0703032147khang.atlassian.net/browse/GC-67), [GC-69](https://0703032147khang.atlassian.net/browse/GC-69).
- Vị trí đầu ra: `BE/README.md; FE/README.md; deployment runbook`.

Triển khai môi trường được nhóm chọn, cấu hình DB/storage/AI/secrets, kiểm tra quyền và sao lưu. Ghi commit/tag, phiên bản, rollback và diễn tập smoke sau deploy.

**Tiêu chí:** Có URL thật, log deploy/smoke, phiên bản và hướng dẫn phục hồi. Không công khai credentials; chưa deploy ghi Blocked/Not Deployed, không ghi URL mẫu như thật.

### GC-71 — [S5] VI.2 Installation Guides tái lập từ máy sạch

- Mã kế hoạch: S5-16; requirement: Source bàn giao; BE/FE/AI/DB.
- Chủ trì: Hoàng Duy Khánh; reviewer: Bùi Đức Hưng; ước lượng đề xuất: 4–8 giờ.
- Phụ thuộc: [GC-61](https://0703032147khang.atlassian.net/browse/GC-61), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70).
- Vị trí đầu ra: `BE/README.md; FE/README.md; hướng dẫn AI/DB/deployment`.

Viết yêu cầu môi trường/phiên bản, clone, cài dependencies, env placeholder, migrate/seed, run/test/build và triển khai; kiểm trên môi trường sạch. Ghi khác biệt Web/Mobile nếu có.

**Tiêu chí:** Một người khác làm theo được với log/commit; lệnh là lệnh đã thử, không sao chép mvn/Spring của mẫu khi stack khác. Không đưa khóa/mật khẩu vào hướng dẫn public.

### GC-72 — [S5] VI.3 User Manual cho Participant

- Mã kế hoạch: S5-17; requirement: FR-01,04,05,06,17; kết quả/phản hồi.
- Chủ trì: Hoàng Duy Khánh; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 4–8 giờ.
- Phụ thuộc: [GC-57](https://0703032147khang.atlassian.net/browse/GC-57), [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70).
- Vị trí đầu ra: `Docs/07-review/; hướng dẫn người dùng Participant`.

Hướng dẫn từng bước hồ sơ/portfolio, đăng ký, roll/frame, upload/nộp, theo dõi/đọc phản hồi/kết quả; mô tả lỗi thường gặp và phạm vi sửa bài đã chốt.

**Tiêu chí:** Dùng ảnh chụp sản phẩm thực và dữ liệu có quyền; nếu chỉ thiết kế phải gắn nhãn prototype. Steps khớp phiên bản bàn giao và kiểm bởi người khác.

### GC-73 — [S5] VI.3 User Manual Admin, Organizer và Judge

- Mã kế hoạch: S5-18; requirement: FR-01,02,03,07..12,16.
- Chủ trì: Lê Minh Khang; reviewer: Nguyễn Khánh Hiền; ước lượng đề xuất: 6–10 giờ.
- Phụ thuộc: [GC-58](https://0703032147khang.atlassian.net/browse/GC-58), [GC-60](https://0703032147khang.atlassian.net/browse/GC-60), [GC-66](https://0703032147khang.atlassian.net/browse/GC-66), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70).
- Vị trí đầu ra: `Docs/07-review/; hướng dẫn 3 vai trò quản lý`.

Hướng dẫn quản trị, cấu hình+mẫu, duyệt và đọc AI flags, phân công, chấm nhiều vòng/chốt, công bố/giải thưởng/triển lãm/archive/report; giải thích quyền và hạn chế AI.

**Tiêu chí:** Đủ 3 vai trò, có steps/ảnh thật/kết quả mong đợi/xử lý lỗi; không chứa thao tác ngoài quyền hoặc chính sách chưa duyệt. Reviewer thử theo tài liệu.

### GC-74 — [S5] VI.1 Deliverable Package và liên kết bàn giao

- Mã kế hoạch: S5-19; requirement: Đề tài 3.2.f/g; chương VI mẫu.
- Chủ trì: Đào Thị Thanh Bình; reviewer: Lê Minh Khang; ước lượng đề xuất: 4–8 giờ.
- Phụ thuộc: [GC-69](https://0703032147khang.atlassian.net/browse/GC-69), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70), [GC-71](https://0703032147khang.atlassian.net/browse/GC-71), [GC-72](https://0703032147khang.atlassian.net/browse/GC-72), [GC-73](https://0703032147khang.atlassian.net/browse/GC-73).
- Vị trí đầu ra: `Docs/07-review/submission-guide.md; release manifest`.

Lập danh mục source, DB scripts, requirements/design, testcases/results/bugs, install/user guides, báo cáo/slide/demo; link Jira/GitHub commit/tag và URL thật. Đánh dấu thiếu hoặc chỉ thử nghiệm.

**Tiêu chí:** Mọi link truy cập được theo quyền, không có placeholder giả; gói không chứa secret/dữ liệu riêng. Tag/release chỉ tạo sau review và đủ điều kiện, không tự coi kế hoạch là release.

### GC-75 — [S5] Tổng hợp I–VI, slide, demo và nghiệm thu toàn bài

- Mã kế hoạch: S5-20; requirement: FR-01..18; NFR-01..08; chương I–VI.
- Chủ trì: Lê Minh Khang; reviewer: Đào Thị Thanh Bình; ước lượng đề xuất: 6–12 giờ.
- Phụ thuộc: [GC-55](https://0703032147khang.atlassian.net/browse/GC-55), [GC-69](https://0703032147khang.atlassian.net/browse/GC-69), [GC-74](https://0703032147khang.atlassian.net/browse/GC-74).
- Vị trí đầu ra: `Docs/07-review/report-map.md; báo cáo cuối, slide, checklist`.

Giữ nội dung I–III.3.1 đã có, ghép phần mới và sửa mâu thuẫn bằng review; thống nhất thuật ngữ/ID/hình/bảng/mục lục/tài liệu tham khảo. Chuẩn bị demo 4 vai trò/5 luồng, đóng góp nhóm, AI limitations và Q&A.

**Tiêu chí:** Truy vết đủ đề tài→yêu cầu→thiết kế→code→test→bàn giao; ghi rõ gap/known issues, không giả tuyên bố hoàn thành. Có review chéo và biên bản chấp nhận nội bộ; lịch nộp thật do nhóm xác nhận.

## 9. Quyết định chờ nhóm trưởng

1. Xác nhận ngày nộp, lịch bắt đầu/kết thúc và giờ khả dụng trước khi start Sprint 4/5; kế hoạch hiện để trống các ngày.
2. Cập nhật các quyết định [D-01..D-20](open-questions.md) có bằng chứng: Web/Mobile; stack; giới hạn bài/ảnh; metadata/minh chứng; công thức/đồng điểm/nhiều vòng; sửa bài/điểm; AI và công khai/lưu trữ.
3. Xác nhận mức code thực tế ở các nhánh/repo khác nếu có và chuyển/tách các gói GC-56..60 cho đúng capacity; không lặp phần đã làm.
4. Review PR kế hoạch; không tự đánh dấu các công việc mới là Done. Việc tạo task không là phê duyệt nghiệp vụ từ giảng viên.

Nhật ký lần lập: tạo 2 sprint future và Epic GC-37; 38 task GC-38..75, tất cả To Do, đủ 5 assignee; giữ nguyên Sprint 1–3. Các liên kết dependency được xác minh sau khi tạo; đường dẫn trong kế hoạch dùng issue thật.
