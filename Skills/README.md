# Bộ Project Skills — Quy ước sử dụng

Đây là **bộ Skill ở thư mục `Skills/` theo hình thầy vẽ trên bảng**: quy trình/tiêu chuẩn tái sử dụng để phân tích, thiết kế, test, quản lý dự án và hỗ trợ AI. Mỗi Skill có `SKILL.md` riêng. Đây là hướng dẫn; chỉ tự động được agent áp dụng nếu công cụ AI hỗ trợ và được cấu hình theo quy định công cụ.

| Skill | Owner đề xuất | Tình huống áp dụng |
|---|---|---|
| [requirements-analysis](requirements-analysis/SKILL.md) | TV1 | Viết/review một FR |
| [entity-modeling](entity-modeling/SKILL.md) | TV2 | ERD, keys, Data Dictionary |
| [test-case-design](test-case-design/SKILL.md) | TV4 | happy/unhappy/test evidence |
| [feasibility-analysis](feasibility-analysis/SKILL.md) | TV5/TV1 | PoC, giới hạn, capacity |
| [backend-development](backend-development/SKILL.md) | TV2 | API, JWT/RBAC, storage |
| [frontend-development](frontend-development/SKILL.md) | TV3 | FE routes, validation, states |
| [ai-integration](ai-integration/SKILL.md) | TV5 | AI model, metrics, fallback |
| [project-management](project-management/SKILL.md) | TV1 | Jira, docs review, sprint |

## Lưu ý vị trí và cách dùng

Thầy minh họa `Skills/` ở root. Một số agent (ví dụ công cụ hỗ trợ Agent Skills trong GitHub) cần thư mục đặc biệt như `.github/skills/` để **tự động phát hiện**. Nếu nhóm dùng agent đó, copy nội dung từng Skill sang đúng vị trí theo tài liệu công cụ và cập nhật đồng bộ; không giả định tất cả AI đều tự đọc `Skills/`.

## Trình diễn nhanh

Chọn `requirements-analysis` → yêu cầu AI rà `FR-06`, tìm các thiếu sót về happy/unhappy, entity, AC, giới hạn → con người so lại đề tài nguồn → tạo PR docs khi thay đổi. Lưu diff/trạng thái để thầy xem được hiệu quả.
