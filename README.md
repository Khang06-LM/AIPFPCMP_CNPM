# AI-powered Film Photography Contest Management Platform

**Đề tài tiếng Việt:** Nền tảng tổ chức và quản lý cuộc thi nhiếp ảnh phim tích hợp trí tuệ nhân tạo  
**Bộ hồ sơ:** Requirements / Domain / Quality / Feasibility / Project Skills — v1.0-review / V3 (28/10/2026)  
**Trạng thái:** **DRAFT / REVIEW REQUIRED**, chưa có bằng chứng rằng sản phẩm đã triển khai hay test thành công.

> **Trình bày đúng hình giảng viên vẽ:** thư mục gốc gồm `Docs/`, `BE/`, `FE/`, `README.md`, `Check-list.md`, `Skills/`. File trên bảng có thể ghi `README.MD`, ở repo dùng `README.md` theo tên phổ biến được GitHub nhận diện. Không đính kèm code giả.

## 1. Tài liệu nguồn và phạm vi đáng tin

- **[G]** `Đề tài CNPM tiếng việt.docx` (6 trang): 4 vai trò Admin/Organizer/Judge/Participant, 5 Core Flow, metadata analog, AI duplicate/AI-generated/tagging/analytics, JWT/RBAC, cloud storage, deliverables Web/Mobile.
- **[P]** `Kế hoạch dự án – Nền tảng quản lý cuộc thi nhiếp ảnh phim tích hợp AI___(1).docx` (13 trang): 11 Epic, 69 Story, 284 SP, phân công TV1..TV5, 4 Sprint 8 tuần, Jira/GitHub.
- **[D]** 18 FR/8 NFR, BR và entity/cardinality cụ thể do nhóm cần review, không phải tất cả đã có trong nguồn G/P. **TBD** là quyết định còn mở.

Bộ hồ sơ này nhằm giúp nhóm **chứng minh cách phân tích và cách sẽ đáp ứng yêu cầu**, không khẳng định đã viết xong phần mềm. `Implemented/Verified` chỉ khi có GitHub PR, staging và test evidence thực.

## 2. Cấu trúc repo theo sơ đồ lớp

```text
Project/
├── Docs/                         # Markdown documentation phân rã chi tiết
│   ├── README.md                 # Mục lục và trạng thái
│   ├── 00-overview/              # Problem, context, 5 Core Flow
│   ├── 01-governance/            # Requirements management / change control
│   ├── 02-requirements/          # 18 file FR và 8 file NFR, AC, TC
│   ├── 03-domain/                # Entity, ERD, data dictionary, BR
│   ├── 04-traceability/          # FR ↔ Jira FPC ↔ Test
│   ├── 05-quality/               # Happy/Unhappy/E2E/Test Plan
│   ├── 06-feasibility/           # Khả thi, hạn chế, estimate giờ
│   └── 07-review/                # Hướng dẫn nộp, Q&A
├── BE/
│   └── README.md                 # Backend API/data/auth design
├── FE/
│   └── README.md                 # 4 portal UI/route/error handling
├── Skills/
│   ├── README.md
│   └── <8 skills>/SKILL.md
├── README.md                     # Entry point cho thầy
└── Check-list.md                 # Checklist nghiệm thu tài liệu
```

## 3. Tài liệu nên mở theo thứ tự khi thầy kiểm tra

1. **[Docs Overview](Docs/README.md)** → chỉ cấu trúc hồ sơ.
2. **[Problem Statement](Docs/00-overview/problem-statement.md)** → vấn đề và bối cảnh.
3. **[5 Core Flows](Docs/00-overview/end-to-end-flows.md)** → logic xuyên suốt.
4. **[SRS / 18 Functional Requirements](Docs/02-requirements/README.md)** → danh mục và từng FR.
5. **[Ví dụ đặc tả sâu FR-06: Nộp ảnh](Docs/02-requirements/functional/FR-06-photo-submission.md)** → happy/unhappy, AC, TC.
6. **[Entity Catalogue & DB](Docs/03-domain/entities.md)** → field, key, FK, quan hệ, privacy.
7. **[Traceability Matrix](Docs/04-traceability/requirements-matrix.md)** → Requirement–Story–Test.
8. **[Feasibility & Limitations](Docs/06-feasibility/feasibility-study.md)** → khả thi, giới hạn AI/cloud, fallback.
9. **[Check-list](Check-list.md)** → tình trạng cần rà.
10. **[Hướng dẫn nộp GitHub](Docs/07-review/submission-guide.md)** → branch, PR, link.

## 4. Phân công review theo kế hoạch nhóm

| Thành viên | Vai trò [P] | Tài liệu review chính |
|---|---|---|
| TV1 – Lê Minh Khang | PM/BA + Admin | Problem/Context, Requirements Governance, Admin, Feasibility, Report |
| TV2 – Bùi Đức Hưng | Backend/Tech Lead | BE README, entity/ERD, Auth, Storage, Score aggregation, NFR |
| TV3 – Hoàng Duy Khánh | Participant FE | FE README, enrollment, roll/frame, submission, portfolio |
| TV4 – Nguyễn Khánh Hiền | Organizer/Judge | Contest, manual review, assignment, scoring/test scenarios |
| TV5 – Đào Thị Thanh Bình | AI/Analytics | AI duplicate/generator/tagging, PoC, limitations, analytics |

## 5. Những gì cần xác nhận trước khi tuyên bố “đã chốt”

[Danh sách quyết định còn mở](Docs/01-governance/open-questions.md): Web vs Flutter, số ảnh/format/size, yêu cầu negative, quy trình đăng ký, vòng chấm, công thức/tie-break, quyền công khai/retention, hạn nộp thật, stack Backend và Jira/GitHub URL.

## 6. “Hoàn thành Requirements” nghĩa là gì?

**Chưa:** chỉ tạo file Markdown. **Đạt bản đặc tả (Approved):** owner+reviewer chốt nguồn/FR/BR/Entity/AC/TC, liên kết Jira, các TBD blocker được xử lý. **Đạt ứng dụng (Verified):** có code+test actual+evidence. Hai mốc không trộn nhau.

## 7. Không lưu thông tin nhạy cảm

Không commit `.env`, API key, secret, ảnh cá nhân không được phép dùng, âm bản riêng tư. Nếu repo public, chỉ dùng ảnh mẫu có giấy phép hoặc nhóm sở hữu; đưa dữ liệu thật lên private storage theo quyền.

## 8. BẢN V3 — BUSINESS REQUIREMENTS DEFENSE PACK (28/10/2026)

**Mục đích:** nâng cấp 18 FR để trả lời câu hỏi “nếu có lỗi/tình huống ngoài mong đợi thì hệ thống xử lý nghiệp vụ thế nào và chứng minh bằng gì?”. Tài liệu phân tích thuộc **[D] đề xuất**; chưa có bằng chứng chạy test, các chính sách còn TBD cần nhóm/giảng viên chốt.

| Chủ đề phản biện | Mở ngay |
|---|---|
| Quy tắc ra quyết định cho nộp/duyệt/chấm/công bố | [Business Decision Tables](Docs/03-domain/business-decision-tables.md) |
| Trạng thái và guard | [State Transitions](Docs/03-domain/state-transitions.md) |
| Tính đúng luôn phải giữ | [Business Invariants](Docs/03-domain/business-invariants.md) |
| Lỗi & khôi phục | [Failure & Recovery Matrix](Docs/05-quality/failure-recovery-matrix.md) |
| Bộ test có dữ liệu seed/expected | [Business Scenario Tests](Docs/05-quality/business-scenario-tests.md) |
| Truy vết requirement đến evidence | [Requirement Coverage](Docs/04-traceability/requirement-coverage.md) |
| Gap/khả thi/MVP | [Requirement Gap Analysis](Docs/06-feasibility/requirement-gap-analysis.md) |
| Ôn phản biện từng vai trò | [Business Defense Playbook](Docs/07-review/business-defense-playbook.md) |
| Cập nhật lên GitHub | [Upgrade & GitHub Guide](Docs/07-review/upgrade-and-github-guide.md) |

**V3 kế thừa V2:** 18 file FR thêm mục 13 riêng từng chức năng, 8 NFR thêm verification scenario, Business Rules / Decision Log / Entity / BE / FE được cập nhật. Phân tích **không** được coi là chứng nhận phần mềm hoạt động cho đến khi có PR, staging và test evidence thực tế.
