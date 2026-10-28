---
name: test-case-design
description: Thiết kế Happy, Unhappy, boundary, API security, E2E, đối chiếu AC.
---

# Skill — test-case-design

## Project Context

AI-powered Film Photography Contest Management Platform; 4 role; 5 Core Flow; analog metadata FilmRoll/FilmFrame/Scan; phân tích `[G]` (đề tài), `[P]` (kế hoạch), `[D]` (quyết định suy luận).

## Mục tiêu

Thiết kế Happy, Unhappy, boundary, API security, E2E, đối chiếu AC.

## Quy trình bắt buộc

Từ AC tạo dữ liệu seed và expected; kiểm happy + invalid input + bad role + bad state + timeout + concurrency; viết testcase; ghi NOT RUN; sau chạy mới ghi actual/evidence.

## Inputs

- `Docs/00-overview/problem-statement.md`
- `Docs/02-requirements/README.md` và FR được giao.
- `Docs/03-domain/entities.md`, `Docs/01-governance/open-questions.md`.
- Tài liệu kế hoạch/jira/story được owner cung cấp; không bịa status hay GitHub URL.

## Outputs & Quality Gate

1. Tệp Markdown có ID, nguồn, owner, status, link requirement/Entity/Test/Jira; giữ đúng tree repo.
2. Bảng gap/assumption cần quyết định (`TBD`) khi thiếu nguồn; không che giấu vấn đề.
3. Một ví dụ happy và một ví dụ unhappy tối thiểu có expected cụ thể.
4. Người phụ trách kiểm tra thủ công, reviewer chéo kiểm logic/format trước merge.

## Guardrails

- Không đánh dấu pass nếu chỉ viết expected; không bỏ unhappy case vì FE đã validate.
- Không tự sửa yêu cầu được duyệt mà không tạo Change Request và review PR.
- Không tuyên bố đã kiểm thử/deploy khi chưa có actual/evidence.

## Cách thử skill trên lớp

Áp dụng vào `FR-06` hoặc module owner; kiểm 10 mục trong file, so kết quả trước/sau và đọc reasoning bằng mô tả công khai được. Skill là hướng dẫn, **không thay người phụ trách phê duyệt**.
