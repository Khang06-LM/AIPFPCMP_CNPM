# DEF-01 — Sổ tay hỏi đáp nghiệp vụ với giảng viên

> **DRAFT 28/10/2026**. Dùng để ôn và phản biện, **không** học thuộc câu trả lời như bằng chứng đã code. Nếu thầy hỏi “đã chạy chưa?”, phải mở PR/test evidence hoặc nói rõ **thiết kế – chưa kiểm thử**.

## 1. Phương pháp 6 câu trả lời cho mọi tình huống

1. **Business problem:** tác nhân và dữ liệu nào bị ảnh hưởng? Ví dụ: hai lần nhấn nộp tạo hai bài.
2. **Rule & invariant:** điều kiện nào luôn đúng? Ví dụ: một request ID chỉ tạo một submission.
3. **Decision:** hệ thống quyết định theo điều kiện nào, actor nào có quyền?
4. **Implementation concept:** FE cảnh báo, BE kiểm quyền/state/transaction, DB unique/version, storage/async nếu cần.
5. **Verification:** đưa 1 dữ liệu seed, thao tác thử, Expected + DB audit; chỉ nói PASS khi chạy thật.
6. **Limit:** vấn đề nào hệ thống không thể bảo đảm, MVP cắt gì, ràng buộc nào đang TBD.

## 2. Câu hỏi tình huống và nơi mở file

| Thầy hỏi | Lập luận ngắn | Mở ở đâu |
|---|---|---|
| Upload bắt đầu trước hạn, kết thúc sau hạn? | Chốt theo thời điểm server finalize, temp asset không phải bài nộp | [BDT-SUB](../03-domain/business-decision-tables.md), [FR-06](../02-requirements/functional/FR-06-photo-submission.md) |
| Response báo lỗi nhưng DB đã commit? | GET/retry cùng request key idempotent, không tạo 2 bài | [Failure Matrix F-05](../05-quality/failure-recovery-matrix.md) |
| Đã duyệt rồi lại nghi gian lận? | UNDER_INVESTIGATION, append-only review, chặn downstream/correction | [State machine](../03-domain/state-transitions.md), [FR-08](../02-requirements/functional/FR-08-manual-review.md) |
| Judge mất mạng sau lưu điểm? | Không đoán kết quả; đọc lại score/version rồi retry an toàn | [FR-10](../02-requirements/functional/FR-10-judging-scoring.md), [F-11](../05-quality/failure-recovery-matrix.md) |
| Hai bài cùng 9.5, một giải nhất? | Không chọn tùy tiện; block publish đến khi D-06 được phê duyệt | [BDT-PUBLISH](../03-domain/business-decision-tables.md), [Open Questions](../01-governance/open-questions.md) |
| AI khẳng định ảnh AI nhưng có contact sheet? | Detector chỉ flag, thẩm định và appeal có human decision, giữ dấu vết | [FR-14](../02-requirements/functional/FR-14-generated-image-ai.md) |
| Server AI ngừng hoạt động? | Manual review nếu policy cho phép, AIJob UNAVAILABLE, không auto-reject | [Failure Matrix](../05-quality/failure-recovery-matrix.md) |
| Công bố rồi phát hiện sai? | Correction version mới, không sửa/xóa published snapshot cũ | [FR-11](../02-requirements/functional/FR-11-ranking-result-publication.md) |
| Đã đủ yêu cầu chưa? | Đi qua FR–BR–Entity–AC–TC–PR evidence, chỉ VERIFIED khi chạy test pass | [Coverage](../04-traceability/requirement-coverage.md) |
| 284 SP liệu khả thi? | Điền giờ mỗi người có thể làm, so effort và buffer, chốt MVP/PoC | [Gaps](../06-feasibility/requirement-gap-analysis.md) |

## 3. Script trình bày 2 phút cho nhóm trưởng

> “Nhóm em phân rã từ vấn đề và 5 luồng nghiệp vụ theo đề tài thành các yêu cầu FR, NFR. Mỗi yêu cầu liên kết với Business Rule, Entity, tiêu chí nghiệm thu, Test Case và Jira Story. Vì thầy yêu cầu xử lý tình huống ngoài luồng chính, nhóm em bổ sung Decision Tables, State Machines, Invariants và Failure & Recovery Matrix. Chẳng hạn FR-06 không coi upload thành công là nộp thành công: Backend phải chốt trước deadline, có idempotency và đối soát khi mất phản hồi. Nếu thầy hỏi phần đã code hay chưa, nhóm sẽ mở PR và kết quả test thực tế; các mục chưa có bằng chứng vẫn để DRAFT/NOT RUN. Những quy tắc như hòa điểm và số giám khảo đang để Decision Log, chờ chốt trước khi triển khai.”

## 4. Họp thử: mỗi thành viên phải chứng minh được gì?

- **TV1:** Business Problem → Core Flow → FR → Jira; D-xx chưa chốt, test evidence, scope/hours.
- **TV2:** API auth/DB transaction + idempotency, status guard, rollback/cleanup, migration.
- **TV3:** upload + metadata, owner permissions, deadline boundary, retry/error UI.
- **TV4:** review/assignment/score, version conflict, tie-break, correction after publish.
- **TV5:** similarity vs generated-image detector, false positives, AI unavailable, evaluation limits.

## 5. Câu trả lời không nên dùng

- ❌ “AI chắc chắn nhận diện ảnh phim thật 100%.”
- ❌ “Frontend đã ẩn nút thì user không có quyền.”
- ❌ “Upload xong thì bài được chấp nhận”, khi chưa finalize & validate.
- ❌ “Timeout nghĩa là DB chưa lưu.”
- ❌ “Nếu bằng điểm thì hệ thống chọn người nộp trước” khi chưa có thể lệ.
- ❌ “Tất cả test đã đạt” khi tài liệu hiện chưa có test run thực tế.
