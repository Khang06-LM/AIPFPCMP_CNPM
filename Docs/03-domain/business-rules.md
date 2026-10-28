# BR-INDEX — Quy tắc nghiệp vụ liên kết Requirements

> **Quan trọng:** Tất cả các BR bên dưới được phân tích để có thể kiểm thử. Chúng là **đề xuất [D]**, không có nghĩa giảng viên đã quyết định chi tiết các chính sách này. Reviewer phải kiểm chứng trước khi nâng lên Approved.

## Quy tắc theo nhóm yêu cầu

### FR-01 — Xác thực, phiên đăng nhập và phân quyền theo vai trò

- BR-01: Chỉ người dùng đã xác thực mới sử dụng tài nguyên cần đăng nhập.
- BR-02: Vai trò được kiểm tra tại API, không chỉ ẩn nút ở FE.
- BR-03: Mọi tác vụ tác động dữ liệu phải kiểm tra quyền sở hữu hoặc phân công.
- BR-04: Mật khẩu lưu dưới dạng hash có salt; không lưu plain text.

### FR-02 — Quản trị người dùng, danh mục phim và nhật ký hệ thống

- BR-05: Không xóa cứng danh mục đã được tham chiếu trong submission, chỉ vô hiệu hóa theo phương án.
- BR-06: Admin không tự ý xem file minh chứng riêng tư nếu không có quyền nghiệp vụ.
- BR-07: Thao tác thay đổi quyền/khóa user phải được ghi log.

### FR-03 — Tạo và cấu hình cuộc thi, hạng mục, vòng và tiêu chí chấm

- BR-08: close_at phải lớn hơn open_at; mốc thời gian lưu cùng timezone chuẩn.
- BR-09: Không cho thay đổi tùy tiện tiêu chí khi đã phát sinh điểm; dùng phiên bản hoặc khóa.
- BR-10: Không công bố cuộc thi chưa đủ hạng mục/quy chế tối thiểu.
- BR-11: Quyền sửa cuộc thi thuộc Organizer được phân công hoặc Admin theo chính sách.

### FR-04 — Đăng ký tham gia và phê duyệt đăng ký

- BR-12: Không tự tạo nhiều đăng ký trùng cho cùng một cuộc thi nếu chính sách mỗi người một đăng ký.
- BR-13: Trạng thái đăng ký tách biệt trạng thái Submission.
- BR-14: Luồng tự động duyệt hoặc duyệt thủ công phải được thầy/nhóm xác nhận.

### FR-05 — Quản lý cuộn phim, khung hình và siêu dữ liệu analog

- BR-15: Frame thuộc đúng Roll và Roll thuộc Participant đăng nhập.
- BR-16: Frame number không trùng trong một Roll nếu bài toán yêu cầu duy nhất.
- BR-17: Thay đổi metadata đã dùng trong bài nộp cần lưu lịch sử hoặc khóa theo trạng thái.
- BR-18: Negative/contact sheet là minh chứng tùy chọn theo thể lệ cuộc thi.

### FR-06 — Tải ảnh scan và gửi bài dự thi

- BR-19: Một Submission tham chiếu đúng một ảnh scan chính và đúng Frame sở hữu hợp lệ (MVP).
- BR-20: Check hạn nộp trên thời gian máy chủ ở bước finalize; không chỉ kiểm tra JS.
- BR-21: Chỉ chấp nhận định dạng/cỡ tối đa theo ContestRule hoặc cấu hình đã chốt; số mẫu 20MB chỉ là ví dụ, chưa có hiệu lực.
- BR-22: Không lộ URL truy cập file riêng tư công khai; cho phép truy cập qua signed URL có hạn.
- BR-23: Không chuyển sang SUBMITTED khi upload chưa hoàn tất.

### FR-07 — Kiểm tra điều kiện hợp lệ của bài dự thi

- BR-24: Quy tắc xác thực máy chỉ kiểm chứng điều kiện định lượng; không kết luận tác phẩm có phải phim thật.
- BR-25: Báo cáo validation phải truy vết từng rule và nguyên nhân.
- BR-26: Chạy lại validation không tạo nhiều bản quyết định mâu thuẫn.

### FR-08 — Duyệt bài và xử lý yêu cầu chỉnh sửa

- BR-27: AI flag không có quyền quyết định loại tác phẩm tự động.
- BR-28: Một quyết định cuối phải có người thực hiện/thời điểm/lý do khi từ chối.
- BR-29: Chỉ bài Approved mới được đưa vào danh sách phân công giám khảo.
- BR-30: Yêu cầu sửa chỉ khả dụng theo quy tắc contest và deadline.

### FR-09 — Phân công giám khảo theo cuộc thi, vòng và hạng mục

- BR-31: Judge chỉ được xem và chấm bài được phân công.
- BR-32: Không gán trùng cùng Judge–Round–Category theo chính sách.
- BR-33: Bài chưa Approved không nằm trong hàng đợi chấm.

### FR-10 — Chấm điểm đa tiêu chí, nhận xét và chốt theo vòng

- BR-34: Điểm từng tiêu chí trong [0,max_score] theo thang điểm cấu hình.
- BR-35: Công thức điểm chuẩn hóa và trọng số phải chốt, BE là nguồn tính điểm.
- BR-36: Điểm đã Finalized không sửa trực tiếp; phải qua quy trình mở lại có audit nếu được phép.
- BR-37: Không hiển thị điểm Judge khác trước khi chốt nếu chính sách chấm độc lập.

### FR-11 — Tổng hợp điểm, xếp hạng và công bố kết quả

- BR-38: Chỉ phiếu FINALIZED hợp lệ được tính.
- BR-39: Chính sách trung bình/trọng số/đồng điểm phải có quyết định D-06 trước khi triển khai.
- BR-40: Không công bố bảng nháp chưa được Organizer phê duyệt.
- BR-41: Kết quả đã công bố giữ snapshot để tái lập, thay đổi phải có audit.

### FR-12 — Kho lưu trữ số, triển lãm và truy xuất hồ sơ tác phẩm

- BR-42: Không tự công khai âm bản hoặc tài liệu minh chứng riêng tư.
- BR-43: Bản archive dựa trên kết quả đã công bố; thay đổi theo quy trình đính chính.
- BR-44: Quyền sử dụng/hình ảnh tác giả cần được xác định trong thể lệ.

### FR-13 — Phát hiện ảnh trùng và ảnh tương đồng cao

- BR-45: AI duplicate là cảnh báo, không là bằng chứng vi phạm tuyệt đối.
- BR-46: Phạm vi so sánh (trong cuộc thi hay toàn hệ thống) phải xác nhận.
- BR-47: Lưu version thuật toán và threshold để tái lập kết quả.

### FR-14 — Phát hiện ảnh có dấu hiệu do AI tạo

- BR-48: Không coi điểm AI-generated là xác suất thật nếu mô hình không hiệu chuẩn.
- BR-49: Không tự loại ảnh; luôn có human review.
- BR-50: API bên ngoài và việc gửi ảnh phải được xét quyền sử dụng/dữ liệu.

### FR-15 — Gắn nhãn, phân loại và tìm kiếm nội dung ảnh

- BR-51: Tag tự động chỉ mô tả dự đoán, không sửa metadata film stock do người dùng khai báo.
- BR-52: Không kết luận địa điểm/danh tính người trong ảnh nếu thiếu dữ liệu được phép.
- BR-53: Manual tag có ưu tiên hiển thị theo chính sách.

### FR-16 — Báo cáo thống kê và phân tích cuộc thi

- BR-54: KPI có định nghĩa SQL và mốc thời gian; không tính double-count khi upload retry.
- BR-55: Admin xem toàn nền tảng, Organizer chỉ xem cuộc thi thuộc quyền.
- BR-56: Báo cáo không chứa dữ liệu cá nhân vượt mục đích.

### FR-17 — Hồ sơ cá nhân, portfolio, lịch sử và theo dõi trạng thái

- BR-57: Chỉ hiển thị lịch sử của người đăng nhập (trừ vai trò được cấp quyền).
- BR-58: Phản hồi giám khảo có thể bị giữ kín trước công bố theo chính sách.
- BR-59: Portfolio công khai phải có sự đồng ý/phạm vi xuất bản.

### FR-18 — Thông báo nghiệp vụ và truy vết thay đổi

- BR-60: Thông báo không quyết định trạng thái nghiệp vụ; thay đổi trạng thái là nguồn sự thật.
- BR-61: Không gửi dữ liệu nhạy cảm cho người nhận sai.
- BR-62: Không được xóa log làm mất dấu vết quyết định quan trọng.

## Những vấn đề phải chốt bằng quyết định có mã

[D-01..D-08](../01-governance/open-questions.md): mobile, hạn mức bài, duyệt enrollment, định dạng/size, vòng chấm, công thức/tie-break, minh chứng, bản quyền/lưu trữ.

## Cách kiểm BR

Mỗi BR phải được ánh xạ đến ít nhất một `AC-FR-xx-yy` và một `TC-FR-xx-yy` trong file FR liên quan. Đối với BR trái với thể lệ chính thức, tạo Change Request và sửa đồng thời FR, Data Dictionary, test, Story.

## V3 — Quy tắc nghiệp vụ phản biện cần đề xuất phê duyệt

- **BR-63 [D]:** Ảnh chỉ tính nộp thành công khi Backend finalize và commit dữ liệu trước hạn theo quyết định D-14; thời điểm upload start không quyết định tính hợp lệ.
- **BR-64 [D]:** Cùng request_key/scope chỉ tạo một logical Submission; retry không tăng quota.
- **BR-65 [D]:** Upload thành công không đồng nghĩa Submission tồn tại; file mồ côi phải có lifecycle cleanup/audit.
- **BR-66 [D]:** Submission APPROVED bị nghi ngờ phải có ReviewIncident và lịch sử; không xóa quyết định cũ.
- **BR-67 [D]:** ScoreSheet dùng optimistic lock và không sửa FINALIZED trực tiếp.
- **BR-68 [D]:** Ranking chỉ công bố khi đủ số phiếu theo thể lệ, rubric thống nhất, tie-break đã chốt và không có blocker hợp lệ.
- **BR-69 [D]:** Sau công bố, đính chính tạo snapshot/version mới; không overwrite bản đã công bố.
- **BR-70 [D]:** AI timeout/false positive không được tự động loại bài; human review là bắt buộc cho kết luận vi phạm.
- **BR-71 [D]:** Contact sheet/negative mặc định private; quyền hiển thị ảnh công khai phải theo thể lệ/consent được chấp thuận.
- **BR-72 [D]:** Notification thất bại không được đảo ngược trạng thái nghiệp vụ đã commit; event retry idempotent.

**Trace:** [Decision Tables](business-decision-tables.md) · [State](state-transitions.md) · [Invariants](business-invariants.md) · [Tests](../05-quality/business-scenario-tests.md). Mọi rule **chưa có phê duyệt từ nhóm/giảng viên**.
