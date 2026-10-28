# SOURCE-01 — Đối chiếu đề tài gốc, kế hoạch nhóm và phạm vi triển khai

## 1. Yêu cầu từ đề tài giảng viên [G]

| Nguồn | Phạm vi bắt buộc đọc | Tài liệu đặc tả trong gói | Điểm cần quyết định |
|---|---|---|---|
| 4 vai trò | Admin, Organizer, Judge, Participant | FR-01/02/04/08/09/10/17 | Multi-role policy |
| Core Flow 1 | Planning & Config | FR-03 | Template & criteria version |
| Core Flow 2 | Submission + analog metadata | FR-04/05/06/17 | Format, quota, frame reuse |
| Core Flow 3 | Verification | FR-07/08/13/14 | Human review, AI thresholds |
| Core Flow 4 | Judging & Evaluation | FR-09/10/11 | Round/rubric/aggregation/tie-break |
| Core Flow 5 | Result & Archive | FR-11/12/15/16 | Consent & retention |
| AI Applications | Duplicate, AI-generated, Tagging, Analytics | FR-13/14/15/16 | Research/prototype vs production |
| Non-functional | JWT+RBAC, cloud object storage, modularity, concurrency | NFR-01..08 | Concurrency/latency/retention target |
| Expected deliverables | Web portal, Web/Mobile participant, Admin, AI, Archive | Readmes / FR set / Check-list | Web vs Flutter clarification |

## 2. Mapping sang 11 Epic của kế hoạch [P]

EP-1: FR-01/06/07 + NFR; EP-2: FR-02/18; EP-3: FR-03/04; EP-4: FR-04/17; EP-5: FR-05/06/17; EP-6: FR-07/08; EP-7: FR-09/10/11; EP-8: FR-11/12; EP-9: FR-13/14/15/16; EP-10: Docs; EP-11: Testing & Release.

## 3. Không có bằng chứng nên không được tuyên bố

- Hai file nguồn là **đề tài + kế hoạch**, không chứa code/deployment, benchmark AI, minh chứng test Pass hoặc quyết định chi tiết về mọi rule. Những tài liệu ở repo này là **bản phân tích có kiểm chứng logic**, còn độ đáp ứng triển khai phải ghi chứng cứ sau.
