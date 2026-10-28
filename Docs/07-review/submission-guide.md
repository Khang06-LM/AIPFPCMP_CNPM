> **V3:** Để thay thế tài liệu V2 trên GitHub, ưu tiên xem [Hướng dẫn cập nhật V3](upgrade-and-github-guide.md); hướng dẫn bên dưới thuộc quy trình upload ban đầu và cần điều chỉnh theo tình trạng PR của nhóm.

# SUBMIT-01 — Cách đưa Requirements lên GitHub và trình cho giảng viên

> Trên bảng, thầy minh họa thư mục `Project/Docs/BE/FE/README.MD/Check-list.md/Skills` và ghi `Markdown push/git`. Đây là cơ sở để nhóm **nộp tài liệu bằng repository**. **Chưa có thông tin chắc chắn về kênh nộp chính thức (LMS/Classroom/email)**; nếu có, vẫn phải nộp theo hướng dẫn của thầy.

## 1. Gói này ứng với sơ đồ bảng như thế nào?

| Trên bảng | Tại repo | Dùng để thầy xem |
|---|---|---|
| Project | root repository | Toàn bộ sản phẩm nhóm |
| Docs → nhiều `.md` | `Docs/` | Requirements, context, entity, business rules, test, feasibility |
| BE | `BE/README.md` | Backend contract/setup dự kiến |
| FE | `FE/README.md` | Frontend route/UX/error handling |
| README.MD | `README.md` | Trang dẫn đường chính |
| Check-list.md | `Check-list.md` | Kiểm tra từng hạng mục/bằng chứng |
| Skills | `Skills/` | Quy trình hỗ trợ tạo/review tài liệu/code |

**Lưu ý:** Repo mới có thể không có source BE/FE trong zip. Nếu nhóm đã có code ở `backend/`, `web/`, **không xóa/đổi tên code**: ghép README vào thư mục thực đang dùng, sau đó cập nhật link root cho khớp.

## 2. Cách nộp khuyến nghị qua Git (Windows + VS Code)

### Bước A: Kiểm tra trước khi ghi file
1. Mở GitHub repository của nhóm, xác nhận người có quyền push/PR.
2. Clone repo về máy nếu chưa có: `git clone <URL_REPOSITORY_CUA_NHOM>`.
3. Mở thư mục clone bằng VS Code. Chạy `git status` để biết đang có thay đổi chưa commit.
4. **Không kéo nguyên folder `FilmContest_Project_Requirements_V2` tạo tầng lồng sai**; mở folder đó và copy **nội dung bên trong** vào root repo.
5. Nếu repo đã có `README.md`/`Docs`/`BE`/`FE`, so sánh và merge thủ công; **không overwrite tài liệu/code đã có**.

### Bước B: Tạo branch tài liệu và commit

```bash
git checkout develop
git pull origin develop
git checkout -b docs/requirements-review-v2
# Copy nội dung vào root repository, rồi:
git status
git add README.md Check-list.md Docs BE FE Skills
git commit -m "docs(requirements): add decomposed SRS and traceability baseline"
git push -u origin docs/requirements-review-v2
```

Nếu `develop` chưa tồn tại: hỏi nhóm trưởng về quy tắc branch, không tự tạo main mới hoặc force push.

### Bước C: Pull Request
1. GitHub → repository → Pull requests → New pull request (hoặc nút Compare & pull request).
2. Base branch: `develop`, compare branch: `docs/requirements-review-v2`.
3. Title gợi ý: `[Docs] Requirements baseline v0.9 — 5 Core Flows, 18 FR, 8 NFR`.
4. Description: nguồn G/P, điểm mới, file Docs quan trọng, quyết định còn TBD, những việc **chưa được triển khai**.
5. Request review tối thiểu TV2 + một member module; không merge khi link hỏng hoặc trái source.
6. Sau review, merge theo quy định nhóm; mở README trực tiếp trên `develop` và kiểm link.

### Bước D: Chia sẻ cho giảng viên
- Chuẩn bị link GitHub đến **README root** và **`Docs/02-requirements/README.md`** tại nhánh đã merge.
- Nếu cần bằng chứng thời điểm cố định, tạo tag chỉ sau khi được review và ghi commit SHA.
- Nếu thầy có hệ thống nộp riêng, nộp **link repo + commit/branch** hoặc tệp theo yêu cầu; không giả định GitHub thay LMS.
- Nếu repo private, mời giảng viên theo quyền cho phép hoặc trình chiếu màn hình; đừng công khai private chỉ để “nộp được”.

## 3. Trình tự 5 phút bảo vệ trên lớp

1. **30s:** root README → nêu 4 role, 5 Core Flow, mục lục.
2. **60s:** Docs Problem Statement → từ vấn đề phân rã thành 18 FR/8 NFR.
3. **90s:** mở FR-06 → mục 4 happy, mục 5 seven unhappy, mục 9 AC, mục 10 Test.
4. **45s:** mở Entity Catalogue / ERD → cho thấy `FilmRoll → Frame → Submission`.
5. **45s:** mở Traceability → FR-06 ↔ FPC-23 ↔ TC.
6. **30s:** mở Feasibility → chỉ rõ AI cảnh báo và Web/Mobile TBD.

## 4. Câu thầy có thể hỏi

**Q: Sao có 18 FR, đề tài chỉ có 5 Core Flow?** 5 là quy trình cấp cao; 18 là phân rã module có thể nghiệm thu, ánh xạ trong Core Flow table.

**Q: Đáp ứng được chưa?** Bản này mô tả cách đáp ứng và AC/TC; chưa có code/test bằng chứng thì trạng thái là Draft/Not Run. Không đánh đồng viết tài liệu với đã code.

**Q: Nếu AI báo giả nhưng là ảnh thật?** AI chỉ flag, Organizer kiểm chứng và ra quyết định; có fallback khi AI unavailable, ghi false positive trong nghiên cứu.

**Q: Vì sao không dùng số SP theo giờ cứng?** SP đo phức tạp tương đối; giờ để kiểm capacity; kế hoạch cũ 284 SP x 4h rất nặng nên cần estimate lại 69 Story theo lịch học.

**Q: Đã chốt quy tắc điểm/đồng điểm chưa?** Đang nằm D-06; cần phê duyệt trước khi khoá thuật toán/chạy điểm thật.

## 5. Kiểm tra nội dung nộp
- [ ] README trên GitHub mở được và links không lỗi.
- [ ] Từng FR có Happy, Unhappy, AC, TC, owner, source, feasibility.
- [ ] Entity/Data Dictionary/RBAC nhất quán với FR.
- [ ] Quy tắc chưa duyệt đánh dấu TBD và có owner.
- [ ] Có PR/review; không tự gắn `Implemented/Verified` khi chưa có chứng cứ.
- [ ] File `Check-list.md` cập nhật tình hình thật.
