# BE — Kiểm thử, cấu hình và vận hành

> **ID:** BE-06 · **Ngày:** 05/11/2026 · **Trạng thái: DRAFT / REVIEW REQUIRED**.
> **Chủ trì:** Bùi Đức Hưng; cả nhóm · **Review:** owner liên quan và một thành viên khác.
> [G]: đề tài; [P]: kế hoạch; [D]: thiết kế đề xuất; TBD: chưa quyết định. Nội dung kỹ thuật là [D], chưa phải code hoặc bằng chứng kiểm thử.

## 1. Kiểm thử cần chuẩn bị

| Mức | Tình huống có ý nghĩa | Expected thiết kế |
|---|---|---|
| Unit | Owner/contest mismatch, state guard | Từ chối trước mutation |
| Unit | Điểm ngoài range/rubric cũ | Không chốt phiếu |
| Integration | DB lỗi sau upload | Không có bài giả, object đối soát được |
| Integration | Save/finalize timeout sau commit | Đọc lại đúng kết quả, không nhân đôi |
| Security | Token hợp lệ nhưng sai assignment | Không đọc/ghi phiếu ngoài quyền |
| AI | Callback lặp/cũ/quota lỗi | Không tự loại bài, không ghi đè input mới |
| E2E | Đủ 4 role và 5 Core Flow | Hồ sơ/điểm/kết quả truy vết |
| Reliability | Hai người chấm/công bố đồng thời | Không mất cập nhật, không publication nửa chừng |
| Recovery | Backup/restore metadata và media mapping | Tái lập hồ sơ và quyền |

Đối chiếu [Test Matrix](../Docs/05-quality/test-matrix.md), [Business Scenarios](../Docs/05-quality/business-scenario-tests.md), [Failure Recovery](../Docs/05-quality/failure-recovery-matrix.md). Thiết kế case không phải test PASS.

## 2. Mẫu hồ sơ lần chạy

| Field | Phải ghi khi thực hiện |
|---|---|
| Run ID / testcase / FR | Định danh và yêu cầu |
| Commit + contract/schema version | Phiên bản thực |
| Môi trường + dữ liệu seed | Có thể tái lập, dữ liệu được phép |
| Steps / input / expected / actual | Không chỉ “thành công” |
| Status | PASS / FAIL / BLOCKED / NOT RUN |
| Evidence / bug / người chạy | Link log an toàn và lỗi |

Hiện tại application tests: **NOT RUN**. Không đưa số coverage/throughput/uptime giả. Các NFR chưa có mục tiêu đo giữ TBD.

## 3. Cài đặt sau khi có source

Chưa có stack/runtime/package manifest/code nên **không có lệnh npm/dotnet/migration được xác minh**. Owner phải điền: runtime version; package manager; dependency lock; lệnh install; cấu hình DB/storage/AI; migrate; seed; run; build; test; rollback. Không sao chép lệnh của mẫu nha khoa.

Tệp .env.example chỉ chứa tên biến và placeholder theo [Security](04-security-and-permissions.md); .env thực không commit.

## 4. Deploy và backup

[D] Tách local/test/staging; xác nhận nhà cung cấp, tài khoản/ngân sách và quyền. Checklist trước deploy: schema tương thích, secrets, private storage, API origin/session, migrations và backup, phiên bản AI, smoke dữ liệu giả.

Sau deploy ghi URL thật, commit/tag, log build/deploy/smoke và known issues. Backup cần cả dữ liệu quan hệ và mapping/object media, không chỉ dump DB. RPO/RTO/retention chưa chốt; phục hồi cần diễn tập trên môi trường an toàn. Rollback phải xét migration, không chỉ quay lại code.

## 5. Chưa chốt gì thì dừng phần nào?

| Quyết định | Việc còn bị chặn |
|---|---|
| D-11 stack | Lệnh cài/build/runtime cụ thể |
| D-02/04/07 bài và tệp | Schema bài nhiều ảnh, giới hạn/mime/metadata |
| D-05/06 chấm | Tính điểm/qua vòng/nghiệm thu kết quả |
| D-08/18 quyền public | Archive/portfolio/retention |
| D-09/19 AI | Chất lượng và đường review khi lỗi |
| D-20 version | Contract và test xung đột |

Happy: một người khác làm theo guide trên môi trường sạch, log chứng minh chạy đúng commit.
Unhappy: thiếu cấu hình AI → báo phụ thuộc không sẵn sàng, không tự coi ảnh không hợp lệ.
Jira: [GC-64](https://0703032147khang.atlassian.net/browse/GC-64), [GC-65](https://0703032147khang.atlassian.net/browse/GC-65), [GC-67](https://0703032147khang.atlassian.net/browse/GC-67), [GC-70](https://0703032147khang.atlassian.net/browse/GC-70), [GC-71](https://0703032147khang.atlassian.net/browse/GC-71).
