# MANIFEST — Hồ sơ được tạo

- 1 README gốc, 1 Check-list và hướng dẫn dẫn đường.
- **18 Functional Requirements** tách file: FR-01..FR-18.
- **8 Non-functional Requirements** tách file: NFR-01..NFR-08.
- Entity Catalogue, Data Dictionary, ERD Mermaid, Business Rules.
- Problem/Context/Flow, Governance/Change, Traceability, Test Strategy/Matrix/E2E, Feasibility/Capacity/Risk.
- BE và FE README.
- 8 Skill có `SKILL.md` trong `Skills/`.
- Bảng CSV 69 FPC Story ước lượng giờ minh họa để nhóm điều chỉnh.

> Trạng thái tất cả yêu cầu: Draft; không có code thực, URL Jira/GitHub thật, test PASS hoặc benchmark được suy ra từ tài liệu nguồn. Cần owner review và cập nhật.

## Thay đổi quan trọng trong V3

- Cập nhật 18 FR, mỗi FR có tình huống phản biện khác nhau, cách xử lý, invariant và test chưa chạy.
- Nâng cấp 8 NFR theo hướng có kịch bản cần chứng minh.
- **Mới:** `Docs/03-domain/business-decision-tables.md`, `state-transitions.md`, `business-invariants.md`.
- **Mới:** `Docs/05-quality/failure-recovery-matrix.md`, `business-scenario-tests.md`.
- **Mới:** `Docs/04-traceability/requirement-coverage.md`, `Docs/06-feasibility/requirement-gap-analysis.md`.
- **Mới:** `Docs/07-review/business-defense-playbook.md`, `upgrade-and-github-guide.md`.
- Bổ sung BR-63..72 và D-13..20, Entity/Data Dictionary, BE/FE contracts, README và checklist.
- Chưa tự sửa các quyết định chưa chốt, chưa tạo Jira/PR/test evidence giả.
