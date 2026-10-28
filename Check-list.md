# Check-list.md — Kiểm tra nộp Requirements trước khi giảng viên hỏi

> **Không tích ✅ trước khi nhóm tự kiểm chứng.** Tài liệu này là checklist cần hoàn thành, KHÔNG phải biên bản nghiệm thu đã đạt. Owner ghi bằng chứng/link PR hoặc lý do chưa xong.

| Hạng mục | Owner | Evidence cần lưu | Trạng thái |
|---|---|---|---|
| Hình dạng root Docs/BE/FE/README.md/Check-list.md/Skills | TV1 | GitHub root URL | ☐ Chưa xác nhận trên GitHub |
| 5 Core Flow được ánh xạ đến FR | TV1 | flow + RTM | ☐ Chờ review |
| 18 FR có actor/input/pre/main/exception | Owner module | FR PR | ☐ Chờ review |
| 18 FR có business rules và Entity | Owner + TV2 | BR, Data Dictionary | ☐ Chờ review |
| 18 FR có AC và Test Cases | Owner + reviewer | SRS + Test Matrix | ☐ Chờ review |
| NFR có cách kiểm, số đo TBD minh bạch | TV2 | NFR PR | ☐ Chờ review |
| Entity/ERD/FK/unique/status | TV2 | ERD PR, migration nếu có | ☐ Chờ review |
| Đánh giá feasibility từng nhóm chức năng | TV1/TV5 | FEAS report + PoC | ☐ Đang phân tích |
| Estimate theo giờ và capacity thực | TV1 + cả nhóm | CSV + biên bản họp | ☐ Chưa chốt |
| BE README phù hợp tech stack/code thật | TV2 | README + chạy trên máy khác | ☐ Chưa có xác nhận |
| FE README đúng routes/API/error states | TV3/TV4 | README + mock/API test | ☐ Chưa có xác nhận |
| Skills có SKILL.md, test ví dụ | Cả nhóm | Folder + PR/test outputs | ☐ Chờ review |
| Jira có actual key, owner, due, linked FR | TV1 | Jira board link | ☐ Chưa có bằng chứng |
| GitHub PR và review chéo | TV1/cả nhóm | PR URL | ☐ Chưa có bằng chứng |
| Có bản demo E2E và test Actual/Result | Cả nhóm | staging/evidence | ☐ Chưa chạy |
| Hạn nộp/kênh nộp được GV xác nhận | TV1 | ảnh thông báo/bài tập | ☐ TBD |

## A. Checklist từng file FR (đọc FR-06 trước)
- [ ] Mã ID và nguồn `[G]/[P]/[D]` minh bạch.
- [ ] Vấn đề/goal, actor và ranh giới scope đầy đủ.
- [ ] Precondition, input, output, postcondition kiểm được.
- [ ] Main Flow có từng bước và cập nhật trạng thái dữ liệu.
- [ ] Alternative/exception ít nhất gồm sai input, sai quyền, sai trạng thái hoặc lỗi phụ thuộc.
- [ ] Rule có quyết định hoặc mã TBD.
- [ ] Entity/FK liên kết với FR khác có logic.
- [ ] AC có thể đo/quan sát, TC có expected tương ứng.
- [ ] Khả thi, giới hạn và fallback nói thật, không hứa AI tuyệt đối.
- [ ] Estimate giờ và owner, reviewer xác nhận.

## B. Checklist phản biện trực tiếp
- [ ] TV1 giải thích yêu cầu gốc → phân rã FR → Jira → Test.
- [ ] TV2 mở Entity/ERD từ `FilmRoll → FilmFrame → Submission`.
- [ ] TV3 demo bằng lời FR-06 và 7 lỗi cụ thể.
- [ ] TV4 giải thích chấm điểm/phiếu FINALIZED/tie-break TBD.
- [ ] TV5 giải thích false positives AI và human review.
- [ ] Mở link tới 4 portal theo vai trò nếu đã có UI; chưa có thì nói rõ đang thiết kế.

## C. Nộp
- [ ] Đã giải nén và chỉ đưa **nội dung** vào root repo, không lồng thêm thư mục tên ZIP.
- [ ] Không ghi đè code/tài liệu có sẵn của nhóm.
- [ ] `git status` sạch sau commit, PR vào develop có review.
- [ ] README links hoạt động trên GitHub.
- [ ] Chia sẻ GitHub repo + commit/PR và nộp thêm LMS nếu thầy yêu cầu.

[Requirements Index](Docs/02-requirements/README.md) · [Submission Guide](Docs/07-review/submission-guide.md) · [Review Q&A](Docs/07-review/teacher-qa.md)

## D. Checklist bổ sung V3 — thầy hỏi sâu Business

| Hạng mục | Owner đề xuất | Trạng thái mặc định |
|---|---|---|
| BDT có đủ quy tắc chấp nhận/từ chối nộp, review, score, publish | TV1/TV4 | ☐ Chờ nhóm duyệt |
| State của Submission/ScoreSheet/Publication tách riêng và có guard | TV2/TV4 | ☐ Chờ review |
| Invariants có cơ chế BE/DB enforce và test riêng | TV2 | ☐ Chưa xác minh implementation |
| FR-01..18 có tình huống business phản biện riêng mục 13 | Owner từng module | ☐ Chờ review nội dung |
| Recovery có rõ rollback, retry, reconcile, compensation | TV2/TV3/TV4 | ☐ Chờ PoC |
| Happy/Unhappy có seed, Expected và tình trạng Actual=NOT RUN | Cả nhóm | ☐ Chưa chạy test |
| AI false positive có human review, không tự REJECTED | TV5/TV4 | ☐ Chờ test |
| D-13..20 đã chốt hoặc trình bày minh bạch trước thầy | TV1 | ☐ TBD |
| Effort hours từng người được xác nhận trước khi hứa deadline | TV1/cả nhóm | ☐ Chưa chốt |
| Mọi file mới có link từ README/FR, check link Markdown pass | TV1 | ☐ Chờ kiểm tra GitHub |

**Phân biệt:** `Review complete` ≠ `Implemented` ≠ `Verified`. Không tick khi chỉ có bản thiết kế hoặc AI-generated document.
