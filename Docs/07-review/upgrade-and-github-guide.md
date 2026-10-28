# GIT-01 — Hướng dẫn nâng cấp Requirements V2 → V3 trên GitHub

> Áp dụng repository nhóm `Khang06-LM/AIPFPCMP_CNPM`. **Không commit nguyên ZIP thay cho các file Markdown**. Bộ ZIP là phương tiện chuyển file; cần giải nén và upload nội dung bên trong.

## 1. Trước khi upload: xác định nhánh của bộ V2

- **Nếu PR `docs/requirements-initial` vẫn đang OPEN:** có thể đẩy V3 vào **chính nhánh đó** để PR cập nhật, rồi yêu cầu nhóm review; tránh tạo PR trùng cho cùng một bộ tài liệu.
- **Nếu PR V2 đã MERGED:** tạo nhánh `docs/business-requirements-v3` từ `main` (hoặc `develop` nếu nhóm đã thống nhất dùng nhánh đó), sau đó cập nhật các file Markdown, tạo PR mới.
- **Nếu chỉ muốn xem thầy kiểm tra ngay trên main:** chỉ merge sau khi đã review để không đưa tài liệu DRAFT chưa kiểm tra vào nhánh chính như Approved.

## 2. Cách upload chuẩn trên GitHub website

1. Tải `FilmContest_Requirements_V3.zip`, giải nén. Mở **thư mục bên trong** (`FilmContest_Project_Requirements_V3/`).
2. Mở repository → chọn đúng **nhánh làm việc**, không nhầm `main`/`docs/...`.
3. Trong **Code → Add file → Upload files**, kéo các thư mục `Docs/`, `BE/`, `FE/`, `Skills/` cùng `README.md`, `Check-list.md`, `MANIFEST.md` vào vùng upload.
4. GitHub hiển thị đường dẫn từng file, ví dụ `Docs/03-domain/business-decision-tables.md`; không để nguyên một folder lồng sai như `FilmContest_Project_Requirements_V3/Docs` nếu cấu trúc chuẩn yêu cầu `Docs` ở root.
5. Kiểm tra xem repository đã có file nào được sửa riêng bởi thành viên khác; **không ghi đè** khi chưa review khác biệt. Upload V3 là thay thế nội dung V2 theo PR, không xóa công việc code.
6. Commit title: `docs: deepen business requirements, state and recovery scenarios`.
7. Tạo hoặc cập nhật PR để mọi người review; kiểm `Files changed`, comment, sửa và merge theo quy tắc nhóm.

## 3. Nếu GitHub web không tải/ghi đè được nhiều file

Dùng GitHub Desktop hoặc Git CLI:

```bash
# Trong thư mục repository đã clone, trên đúng nhánh tài liệu
# Copy các thư mục bên trong ZIP đã giải nén vào root repo
# Xem thay đổi trước khi commit
git status
git diff --stat
# Tự review file trước khi stage
git add Docs BE FE Skills README.md Check-list.md MANIFEST.md
git commit -m "docs: deepen business requirements, state and recovery scenarios"
git push -u origin docs/business-requirements-v3
```

**Lưu ý:** Chỉ chạy câu lệnh `git push` đúng nhánh đang checkout; không đưa secret/ảnh riêng của thí sinh vào repo Public.

## 4. Checklist để thầy đọc được

- [ ] GitHub root đúng `Docs/`, `BE/`, `FE/`, `Skills/`, `README.md`, `Check-list.md`.
- [ ] `Docs/02-requirements/README.md` liên kết tới đủ 18 FR và 8 NFR.
- [ ] FR có mục V3 về phản biện nghiệp vụ, AC, TC, phương án xử lý và giới hạn.
- [ ] Từ FR-06 mở được BDT → State → Invariant → Test → Traceability.
- [ ] Đã phân biệt `[G]` giảng viên, `[P]` kế hoạch, `[D]` đề xuất, `TBD` chưa chốt.
- [ ] Những lời hứa code/test chưa thực thi ghi `NOT RUN`, chưa gắn PASS giả.
- [ ] PR có reviewer, nhánh đích đúng và không ghi đè code cũ.

## 5. Link mở khi hỏi đáp

1. Root `README.md` → mục **Bản đồ V3**.
2. `Docs/02-requirements/README.md` → chọn FR.
3. `Docs/03-domain/business-decision-tables.md` và `state-transitions.md`.
4. `Docs/05-quality/business-scenario-tests.md`, `failure-recovery-matrix.md`.
5. `Docs/04-traceability/requirement-coverage.md` và `Docs/01-governance/open-questions.md`.

**GitHub không phải kênh nộp chính thức nếu giảng viên yêu cầu nộp LMS/đường link riêng.** Khi đó nộp đúng hướng dẫn thầy và gửi link commit/PR để chứng minh version.
