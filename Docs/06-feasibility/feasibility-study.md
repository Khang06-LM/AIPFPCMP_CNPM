# FEAS-01 — Feasibility Study & Solution Limitations

> Đánh giá **sơ bộ từ 2 tài liệu nguồn**, không phải kết quả benchmark hay nghiên cứu định lượng ngoài đời thật. Mức độ khả thi phụ thuộc năng lực nhóm, phạm vi, chi phí và deadline được giảng viên xác nhận.

## 1. Phương pháp phân tích

Mỗi requirement có 6 phép kiểm: **Technical** (stack/PoC), **Schedule** (giờ còn lại), **Human** (người biết làm), **Economic** (cloud/API quota), **Operational** (user workflow), **Evidence** (test và demo). Không dùng câu “dùng AI/cloud là giải quyết được” làm bằng chứng.

## 2. Ma trận khả thi theo mô-đun

| Hạng mục | Khả thi sơ bộ | Cách triển khai tối thiểu | Rủi ro | Bằng chứng cần tạo | Owner |
| --- | --- | --- | --- | --- | --- |
| Đăng nhập và RBAC (FR-01/02) | Cao | Thư viện JWT/hash, DB quan hệ | Auth bypass, quyền owner | PoC 4 role, thử 401/403 | BE/TV2 |
| Contest/Enrollment (FR-03/04) | Trung bình-cao | CRUD + state machine | Thay đổi rule giữa kỳ | 1 contest, 2 category, due date validate | TV4 |
| Roll/Frame metadata (FR-05) | Cao | FK/unique/owned records | Trường bắt buộc chưa chốt | 2 rolls/frames, constraint & owner test | TV3 |
| Upload ảnh/private storage (FR-06) | Trung bình | Object storage + signed URL + transaction | Quota, TIFF lớn, orphan files | PoC JPEG và file lỗi, no public negative | TV2/3 |
| Validation/Review (FR-07/08) | Trung bình | Rule runner & state lock | Race condition, false reject | validation report + concurrent review 409 | TV2/4 |
| Judging/ranking (FR-09/10/11) | Trung bình | Criteria/score version + SQL | Tie-break, multi-round, late changes | 1 round final + tính tay đối chiếu | TV2/4 |
| Archive (FR-12) | Cao cho gallery đơn giản | Metadata/award + private/public policy | Consent và lưu trữ dài hạn | Public album nhưng private asset 403 | TV3 |
| AI duplicate (FR-13) | Trung bình | Checksum/pHash + candidate list | Scan/crop làm sai | Tập 20+ cặp minh họa có nhãn, ghi sai số | TV5 |
| AI-generated (FR-14) | Thấp-trung bình về chất lượng | Mô hình/API có sẵn | False positive/false negative ảnh film | Spike có benchmark, không auto reject | TV5 |
| AI tagging (FR-15) | Trung bình | Vision model/API & taxonomy | Tag sai/hallucination | Bộ ảnh có tag đối chiếu, sửa thủ công | TV5 |
| Analytics (FR-16) | Cao cơ bản | SQL grouped counts | Đếm trùng/mẫu số 0 | Đối chiếu query với seed data | TV5 |
| Mobile Flutter (đề tài đề xuất) | Cao rủi ro thời gian | Flutter + API reuse | Hai UI stack, gấp đôi test | Phải hỏi GV; Web responsive trước | TV1/3 |

## 3. Phân lớp mức ưu tiên (đề xuất để hỏi giảng viên)

**MVP cần hoàn thành trước:** Web 4 role, auth/RBAC, contest, enrollment, roll/frame, upload, validation, manual review, judge scoring, ranking/publish, archive cơ bản; AI duplicate thử nghiệm và gắn cờ hỗ trợ; auto-tagging đơn giản nếu đủ tài nguyên.

**R&D có giới hạn:** AI-generated detection chỉ thể hiện cảnh báo từ một PoC có benchmark; không tự reject; nếu chưa đạt, báo rõ độ tin cậy và demo fallback.

**Mở rộng có điều kiện:** Flutter riêng, template contest, triển lãm đẹp, dashboard phân tích sâu, near-duplicate chống crop nặng. **Không tự ý cắt yêu cầu gốc nếu GV chưa đồng ý**.

## 4. Ngưỡng ra quyết định Go/No-Go

| Khi nào | Câu hỏi đánh giá | Nếu không đạt |
|---|---|---|
| Trước coding sâu | SRS/ERD/API được review, D-02/04/06 được chốt? | Tạo spike/CR, không cho module dựa rule chưa có bắt đầu đồng loạt |
| Sau PoC storage | Upload + finalize + private read chạy được? | Giảm format hỗ trợ theo GV, không chuyển sang chấm thi |
| Sau luồng xét duyệt | Có E2E nộp → duyệt → điểm → publish? | Ưu tiên sửa blocking path, đóng băng tính năng mở rộng |
| Sau AI benchmark | Detector có sai số được ghi trên dataset? | Dùng nhãn Experimental hoặc chỉ báo cáo nghiên cứu |
| Trước nộp | Core Flow test pass? Bằng chứng đầy đủ? | Công khai gap/bug, nộp minh bạch; không ghi Pass giả |

## 5. Limitations cần trình bày thẳng với thầy

1. **AI-generated:** Không thể chứng minh tuyệt đối một scan ảnh có phải ảnh phim thật; detector có thể sai. EXIF/metadata tự khai báo cũng không chứng minh nguồn gốc.
2. **Duplicate:** pHash/checksum không đảm bảo phát hiện cùng cảnh dưới góc khác, crop mạnh hoặc scan lại thay màu; kết quả chỉ là gợi ý review.
3. **Cloud:** Gói miễn phí có quota/chi phí/retention, không cam kết “lưu vĩnh viễn” theo nghĩa đảm bảo nhiều thập niên.
4. **Multi-round:** nghiệp vụ phức tạp, nên chốt chính sách score trước; demo 1 round đúng và mô hình mở rộng nếu thiếu giờ.
5. **Privacy:** việc upload chứng từ âm bản cần hạn chế truy cập và quyền sử dụng; công khai chỉ theo consent/contest policy.
6. **Concurrency:** không thể tuyên bố chịu tải lớn nếu chưa xác định user/load target và chạy benchmark.
7. **Flutter:** yêu cầu đầu vào nói Web + Mobile nhưng deliverable có cách viết Web/Mobile; cần hỏi GV phương án MVP.

## 6. Phân biệt “cách đáp ứng” và “đã đáp ứng”

- **Planned:** Có mô tả giải pháp, Entity, AC, TC. Tất cả file trong gói này đang ở mức này.
- **Implemented:** Có PR/commit/CI, chạy local/staging.
- **Verified:** Có test Expected/Actual/Evidence và reviewer xác nhận.
- **Accepted:** GV hoặc nhóm theo quy trình duyệt. **Chưa có bằng chứng triển khai thực tế từ nguồn**.

[Capacity Estimation](capacity-estimation.md) · [Risk Register](risk-register.md) · [Traceability](../04-traceability/requirements-matrix.md)
