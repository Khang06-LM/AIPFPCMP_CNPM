# REVIEW-QA — Câu hỏi và câu trả lời ôn trước buổi kiểm tra

**1. “Requirements là gì, tại sao không chỉ viết chức năng?”**
Yêu cầu mô tả hệ thống phải làm gì và điều kiện nghiệm thu. Mỗi FR ở đây có actor, pre/postcondition, main/alternative/exception flow, BR, entity, AC/TC, khả thi và giới hạn.

**2. “Phân rã thế nào?”**
Từ PB và business goals, bám 5 Core Flows [G], nhóm chia ra 18 FR [D], 8 NFR, liên kết các AC và TC. Mở cây phân rã tại `Docs/01-governance/requirements-management.md`.

**3. “Vì sao nhiều file .md?”**
Một chức năng được duyệt và thay đổi độc lập; PR diff rõ, traceability dễ; không lẫn Problem Context với Entity hay Test.

**4. “Nộp ở đâu?”**
Repo GitHub của nhóm với root `Docs/BE/FE/README.md/Check-list.md/Skills/` đúng sơ đồ. Nếu thầy yêu cầu LMS thì nộp thêm theo kênh đó.

**5. “Có kết quả thực tế chưa?”**
Từ hai tài liệu nguồn chỉ xác nhận đề tài + kế hoạch; chưa có bằng chứng chạy code/test. Bản phân tích đánh dấu DRAFT và NOT RUN, sẽ cập nhật khi có PR/CI/demo.

**6. “Chức năng có logic nối nhau không?”**
Có: contest → enrollment → filmRoll/frame → scan upload → validation → organizer review → judge assignment → scoring → ranking publish → archive. Xem sơ đồ Mermaid tại Core Flows.

**7. “Một unhappy case quan trọng?”**
Participant mở form trước deadline, bấm submit sau deadline. BE dựa giờ máy chủ chặn finalize, không đưa bài đến Judge; TC tương ứng FR-06.

**8. “Entity sao không chỉ là tên bảng?”**
Entity Catalogue có key/FK, chủ sở hữu, cardinality, unique, data privacy, trạng thái; Data Dictionary xác định ràng buộc cho từng trường trọng điểm.

**9. “AI khả thi không?”**
Duplicate có thể PoC bằng checksum/pHash, còn AI-generated detector khó tin cậy và có thể sai với ảnh scan phim; chỉ flag, không auto reject.

**10. “Tính SP theo giờ?”**
Kế hoạch cũ tự giả định nửa ngày/SP; nhóm sẽ ước lượng giờ theo từng subtask, tính capacity theo lịch học, thêm test/review/đệm. CSV chứa phạm vi minh họa chưa được nhóm duyệt.

**11. “NFR có số liệu đo chưa?”**
Chưa có benchmark; số concurrent users, latency, quota phải chốt trước khi đo. Không bịa tải hệ thống.

**12. “Điểm bao nhiêu, hòa điểm thì sao?”**
Đang là quyết định D-06; không tự đặt quy chế. Thiết kế lưu criterion version, chỉ dùng điểm FINALIZED và chặn công bố nếu chưa có tie-break.

**13. “Giới hạn về lưu trữ?”**
Cloud storage hỗ trợ lưu file nhưng không đồng nghĩa cam kết lưu vĩnh viễn với tài khoản miễn phí; cần retention và backup policy.

**14. “Backend/Frontend README làm để làm gì?”**
Để người khác hiểu module, API, quyền, trạng thái lỗi, cấu hình env, cách chạy/test khi code xong. Gói này mới là design-ready, không chứa source để giả lập đã làm.

**15. “Nếu giảng viên yêu cầu chỉnh một rule?”**
Tạo CR với tác động FR/BR/entity/API/test/hours, PR được review, cập nhật traceability/version; không sửa ngầm.
