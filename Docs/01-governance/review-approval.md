# REVIEW-01 — Quy trình duyệt Requirements trước khi code

## RACI và người duyệt

| Nhóm artifact | Người viết (Responsible) | Người chịu trách nhiệm cuối (Accountable) | Người phản biện (Consulted) |
|---|---|---|---|
| Problem, Scope, REQM | TV1 | TV1 | Cả nhóm + GV khi cần |
| Auth, DB, API, NFR | TV2 | TV2 | TV1, TV3, TV4 |
| Participant/Metadata/Submission | TV3 | TV3 | TV1, TV2 |
| Contest/Review/Judge | TV4 | TV4 | TV1, TV2 |
| AI/Analytics | TV5 | TV5 | TV1, TV2 |
| Báo cáo chính thức | Cả nhóm | TV1 | GV |

## Checklist PR review yêu cầu

- [ ] Chỉ dẫn nguồn `[G]`, `[P]`, `[D]`; không ghép đề xuất thành quy định giảng viên.
- [ ] Tên/định nghĩa Entity khớp toàn repo.
- [ ] Có input-output/pre-post; happy + unhappy thực tế.
- [ ] Ràng buộc quan trọng có quyết định hay TBD, không mơ hồ.
- [ ] AC kiểm chứng được và có testcase.
- [ ] Estimation giờ và điều kiện khả thi được ghi; không “luôn giải được”.
- [ ] README/Index và Traceability cập nhật; Markdown link hoạt động.
- [ ] Có review của ít nhất một thành viên khác.

## Definition of Done cho tài liệu, KHÔNG phải cho code

`Doc-Done`: PR merge + reviewer duyệt + không còn lỗi nghiêm trọng + source/traceability cập nhật. `Code-Done`: đã hiện thực, CI xanh, test có Actual/Evidence, chạy trong môi trường được thống nhất. Hai loại phải theo dõi riêng.
