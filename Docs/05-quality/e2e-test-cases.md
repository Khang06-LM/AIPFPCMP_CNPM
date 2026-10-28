# E2E-01 — Những kịch bản thầy có thể yêu cầu demo trực tiếp

> Chưa chạy; mô tả Expected để kiểm tra logic toàn hệ thống.

## E2E-01: Luồng xuyên suốt thành công

**Tiền điều kiện:** seed 4 user có role đúng, 1 Contest có category/criterion hợp lệ; ảnh scan và metadata hợp lệ.

1. Organizer đăng nhập, tạo cuộc thi `CT-01`, hạng mục `Landscape`, thời gian nhận bài hợp lệ và tiêu chí chấm.
2. Organizer công bố cuộc thi. **Expected:** Participant xem CT-01 ở trang danh sách.
3. Participant đăng nhập, đăng ký CT-01. **Expected:** Enrollment APPROVED hoặc PENDING đúng theo chính sách D-03.
4. Nếu Pending, Organizer duyệt Enrollment. **Expected:** Participant được phép gửi bài.
5. Participant tạo FilmRoll `R-01` và Frame `F-01`; khai báo film stock, ISO, format, camera, lens/lab/scan theo rule.
6. Upload ảnh scan `image-01.jpg`, finalize. **Expected:** Submission `S-01`, có Frame/MediaAsset, trạng thái SUBMITTED.
7. Validation rules hoàn tất; Organizer mở hàng đợi và Approved. **Expected:** S-01 APPROVED, có ReviewDecision.
8. Organizer phân Judge-01 theo Category/Round; Judge xem S-01 và nhập điểm từng tiêu chí, Finalize. **Expected:** một ScoreSheet FINALIZED với CriterionScores.
9. BE tổng hợp điểm; Organizer xem RankingSnapshot, chốt và Publish. **Expected:** Result visible sau publish, trước đó không public.
10. Public vào Archive, thấy tác phẩm đạt giải nếu được xác nhận award/consent. **Expected:** negative không lộ.

**Bằng chứng phải lưu:** screenshot 10 bước + DB IDs liên hệ + network status/API responses đã che token.

## E2E-02: Nộp sau hạn
1. Participant mở form khi còn hạn, chờ quá close_at.
2. Gửi finalize. **Expected:** BE trả lỗi hạn, không tạo `SUBMITTED` hợp lệ.
3. Organizer không thấy bài trong hàng đợi Approved/judging.

## E2E-03: Ảnh lỗi / service AI lỗi
1. Upload file giả JPEG với MIME và bytes không hợp lệ. **Expected:** file bị reject, không có Submission hoàn tất.
2. Với ảnh hợp lệ, tắt AI service. **Expected:** Validation có trạng thái AI unavailable/pending nhưng Organizer có thể review thủ công.

## E2E-04: Phân quyền
1. Participant lấy link trang Judge và gọi API điểm trực tiếp.
2. **Expected:** FE chặn UI + API trả 403; không có ScoreSheet được tạo.
3. Judge A gọi bài của Judge B. **Expected:** không truy dữ liệu private ngoài phạm vi.

## E2E-05: Concurrent review/scoring
1. Hai Organizer cùng mở một Submission; A Approve trước, B Reject trên version cũ.
2. **Expected:** B bị 409, trạng thái không bị ghi đè im lặng.
3. Judge chốt điểm hai lần. **Expected:** không tạo phiếu trùng hoặc tính điểm hai lần.

## E2E-06: Kết quả chưa đủ điều kiện
1. Một ScoreSheet còn DRAFT hoặc có bài đồng điểm chưa có tie-break.
2. Organizer bấm Publish. **Expected:** báo blocking condition, không xuất bản ngẫu nhiên.

## E2E-07: Bảo vệ bằng chứng âm bản
1. Visitor public truy cập file negative theo URL suy đoán.
2. **Expected:** không nhận binary private, signed URL riêng tư không bị lộ.

## Hướng ghi kết quả

Từng bước: `Expected`, `Actual`, `Status`, `Issue`, `Evidence`, `Commit`. Trước khi chạy, tất cả là **NOT RUN**.
