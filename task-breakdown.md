# task-breakdown — cách chia backlog LAB-5

| | |
|---|---|
| Trạng thái | **Bản 1 — chờ duyệt G2** |
| Ngày | 2026-10-26 |
| Tiền đề | [`PREMISE.md`](PREMISE.md) bản 7, duyệt ở G1 (2026-10-26). Errata `E11` đã áp vào SSOT ở B2 |
| Vật liệu | Bộ thiết kế LAB-4 đã nộp: **67 màn Core** (16 mức A, 51 mức B) · **83 API** có file spec riêng · **11 Technical Design** · **11 nhóm Database Design** |
| Dữ liệu backlog | `work/backlog.csv` (chưa lên GitHub — B4 mới đẩy) · mô tả từng mục ở `work/issues/{backlog_id}.md` |

## Cách đọc

- **`backlog_id`** dạng `BL-{epic}-{nnn}` là khoá duy nhất của một mục, **ổn định** kể cả khi sinh lại backlog. Trước khi GitHub Project tồn tại, mọi tham chiếu dùng mã này; sau B4 mới có số `#` của issue.
- **Mã LAB-1 giữ nguyên:** `S2-04` (màn), `N4-01` (function), `API-M3-029` (API), `M_0015` (mã lỗi).
- **`RFP p.NN`** là số trang RFP 第2.0版 bản JA. Tài liệu này **chỉ dẫn số trang và mã**, không chép nội dung RFP.
- Đường dẫn `LAB-4/…` là **tài liệu nội bộ, không nộp kèm kho mã public**.

---

## 1. Nguyên tắc chia task

| # | Nguyên tắc | Vì sao |
|---|---|---|
| 1.1 | **Một mục = một thứ chạy được, kiểm được.** Không có mục kiểu "làm phần tồn kho" | Đề cấm issue to và mơ hồ; mục không kiểm được thì không theo dõi được tiến độ |
| 1.2 | **Màn là đơn vị chia chính.** 67 màn Core thành 67 issue, không gộp, không bỏ | Màn là thứ cả khách lẫn đội dev cùng nhìn thấy; `traceability.csv` đã nối màn ↔ feature ↔ API ↔ WBS |
| 1.3 | **Chỉ màn mức A mới tách sub-issue** theo lớp BE · FE · test | Mức A là màn có luồng nghiệp vụ thật; mức B phần lớn là CRUD, tách ra chỉ thêm chi phí quản |
| 1.4 | **Epic không bao giờ rỗng.** Mỗi epic có ≥1 con | `tkm:estimate-swp` không chấm epic trực tiếp — điểm của epic là tổng con; epic rỗng bị đánh `needs-breakdown` và **không cộng vào tổng** (R-14) |
| 1.5 | **Mỗi thay đổi thiết kế ở PREMISE §1 là một mục riêng**, mang mã đã trích trong PREMISE | Để dòng tiền đề và dòng việc trỏ lẫn nhau được; đổi số mã sau này là làm gãy tham chiếu |
| 1.6 | **Mỗi câu hỏi khách còn mở là một issue làm rõ riêng**, không trộn vào issue làm | C4: không tự chọn cách hiểu thay khách. Issue làm rõ chặn issue làm, và chặn được nhìn thấy trên board |
| 1.7 | **Ràng buộc giờ lái 2024 có epic riêng**, không nằm lẫn trong epic vận tải | Ràng buộc ngoài phạm vi chức năng thông thường; tách ra thì thấy ngay nó chạm những đâu |
| 1.8 | **API có spec riêng gom theo module thành một issue mỗi module**, không một issue mỗi API | 83 issue API lẻ làm board không đọc được, mà vẫn không mịn hơn về mặt theo dõi |
| 1.9 | **Ranh giới ba loại mục** [QUYẾT ĐỊNH G2]: mục `150` giữ **lớp vào/ra của API** (route, quyền, mã lỗi, idempotency) · mục `180+` **chỉ dựng bảng và ràng buộc** · sub-issue `200+` giữ **hành vi nghiệp vụ** khi màn gọi | Bốn lô viết mô tả tự chia ba kiểu khác nhau rồi xin xác nhận — nguyên tắc chia của tôi không định nghĩa chỗ này |

## 2. Định nghĩa xong (DoD)

Mỗi mục **không phải epic** có một file mô tả theo khuôn `work/issue-template.md`, gồm bốn mục bắt buộc: `## Mục tiêu` · `## Nguồn trường` · `## Định nghĩa xong (DoD)` · `## Tham chiếu`.

| # | Luật DoD | Kiểm bằng |
|---|---|---|
| 2.1 | Mỗi gạch đầu dòng nói một thứ **quan sát được**: màn hiện gì, API trả gì, bảng có dòng nào, test nào xanh. Cấm "hoạt động đúng", "đã xong" | Người soát; đọc mù ở R2 |
| 2.2 | **C2 — mọi trường nghiệp vụ hiển thị phải ghi nguồn**: `cột DB` · `công thức` · `sự kiện` · `trạng thái cục bộ` · `API ngoài`. Trường không có nguồn là **lỗi chặn**, không phải câu hỏi mở | `check_issue_source.py` |
| 2.3 | Tham chiếu loại `cột DB` phải là `bảng.cột` **có thật** trong `database-design/` | `check_issue_source.py` tra lại từng cặp |
| 2.4 | **C7 — issue gắn nhãn `state-change`**: DoD phải đòi **sự kiện riêng** cho mỗi thực thể bị loại hoặc đổi trạng thái, kể cả khi giao dịch tổng thành công | `check_event_dod.py` |
| 2.5 | Issue chạm câu hỏi khách còn mở phải nhắc đúng mã `CQ` và nói nó chặn phần nào | `check_cq_open.py` |
| 2.6 | **Không chép nguyên văn RFP** — ≥20 từ tiếng Việt hoặc ≥40 ký tự tiếng Nhật liền mạch là lỗi chặn | `check_leak.py` |

## 2bis. Tự chứa — "tối thiểu đủ để bắt đầu" (hướng C)

Kho nộp là **public**, spec LAB-4 là tài liệu nội bộ không đẩy lên được. Mục nào chỉ trỏ sang spec thì với người đọc ngoài là trống — đọc mù vòng R9 cho **9/15 `KHÔNG LÀM ĐƯỢC`** đúng vì lý do đó (§4.8).

Hướng C: mỗi mục mang **tối thiểu đủ để bắt tay vào làm**, phần dài vẫn trỏ spec. Dưới đây là định nghĩa "tối thiểu" theo từng loại, và luật máy kiểm nó (`work/checks/check_selfcontained.py`). Nội dung do script `work/enrich-issues.py` rút thẳng từ spec LAB-4, không chép tay.

| Loại mục | Khối bắt buộc | Nội dung tối thiểu | Nguồn rút |
|---|---|---|---|
| **mọi mục** | `## Nền` | Ngăn xếp: React SPA / PWA · NestJS modular monolith · TypeScript · PostgreSQL 16. Kèm danh sách thứ **chưa chốt** (công cụ migration, test runner, môi trường thử, tài khoản thử, IdP thử) | `_common/architecture-design/ARCHITECTURE_DESIGN.md` |
| nhãn `api` | `## Hợp đồng API` | Mỗi API: `METHOD` · path · vai trò được gọi · mã lỗi. **Phủ đủ** mọi mã trong cột `api_ids` của mục | `_common/api-list.md` |
| nhãn `db` | `## Cột bảng` | Mỗi bảng mục chạm tới: cột · kiểu · Size · Not Null · mặc định · ghi chú | `system-design/database-design/*_DB.md` §4.x |
| nhãn `screen` | `## Ô trên màn` | Danh sách item (mã ô · tên · loại điều khiển · bắt buộc · ghi chú) và bảng kiểm đầu vào | spec màn §4.1, §4.2 |
| nhãn `qa` | `## Tiêu chí nghiệm thu` | Mỗi `AC-n` một dòng: tiền đề · thao tác · kỳ vọng | spec màn cha §6.3 |

Sub-issue FE/BE **không** chép lại bảng ô của màn — chúng trỏ sang issue cha bằng `backlog_id`, và issue cha nằm trong cùng kho nộp. "Tự chứa" ở đây nghĩa là **tự chứa trong kho nộp**, không phải trong một file.

**Hai lỗi của tài liệu nguồn lộ ra khi chép** — khai thẳng trong chính bản chép, không sửa ngầm:

| Nguồn | Lỗi | Xử lý |
|---|---|---|
| `M8_DB.md` dòng `canonicalization_version` | Thiếu một ô so với tiêu đề bảng (đúng chỗ PREMISE §1.9 đã ghi) | Bản chép chèn ô trống + ghi chú ngay dưới bảng. Lỗi gốc do `BL-M8-002` (#121) sửa |
| `M3_DB.md` ô Overview của `inventory_adjustment` | Dấu `\|` trong backtick (`\|qty_delta\|`) không được thoát, làm vỡ bảng | Bản chép thoát dấu; lỗi gốc vẫn còn ở nguồn |

## 3. Độ hạt và epic

**Chốt P2 — hỗn hợp** [QUYẾT ĐỊNH G2, người dùng chốt 2026-10-26] (ba phương án ở `LAB-5 Planning.md` §3.2; P1 theo màn quá thô ở màn mức A, P3 theo lớp cho issue mơ hồ — đúng lỗi đề cấm).

| Epic | Tên | Issue | Sub-issue | Trong đó là màn |
|---|---|---|---|---|
| `BL-M1-000` (#5) | Dữ liệu chủ | 15 | 6 | 8 |
| `BL-M2-000` (#1) | Nhập hàng | 8 | 3 | 5 |
| `BL-M3-000` (#7) | Tồn kho | 18 | 3 | 11 |
| `BL-M4-000` (#8) | Xuất hàng | 15 | 9 | 8 |
| `BL-M5-000` (#9) | Vận tải · giao hàng | 22 | 15 | 13 |
| `BL-M6-000` (#10) | Nhiệt độ · HACCP | 14 | 6 | 6 |
| `BL-M7-000` (#11) | Truy xuất · thu hồi | 8 | 6 | 4 |
| `BL-M8-000` (#12) | Báo cáo · kiểm toán | 6 | 0 | 3 |
| `BL-M9-000` (#13) | Nền tảng · bảo mật | 20 | 0 | 7 |
| `BL-M10-000` (#6) | Chuyển dữ liệu | 3 | 0 | 2 |
| `BL-DRV-000` (#4) | Ràng buộc giờ lái 2024 | 2 | 0 | — |
| `BL-PLAT-000` (#14) | Quy ước dùng chung | 2 | 0 | — |
| `BL-SETUP-000` (#15) | Nền tảng dự án và dữ liệu mẫu | 7 | 0 | — |
**Tổng: 201 mục — 13 epic · 140 issue · 48 sub-issue.** Khối số trong mỗi epic: `000` epic · `001–019` thay đổi từ PREMISE và issue làm rõ · `010–019` việc nền (epic `SETUP`) · `020–039` function nền · `050–149` màn · `150–179` nhóm API · `180–199` Technical Design và Database Design · `200–299` sub-issue. Mục nhóm API và mục lược đồ **chia phần theo ngân sách độ dài** vì body một issue GitHub tối đa 65 536 ký tự.

## 4. Chỗ hở — xử lý thế nào

Nguồn: báo cáo của 11 lô viết mô tả (mỗi lô bắt buộc liệt kê giá trị đã đoán, chỗ spec nói hai kiểu, trường không tra được nguồn) cộng phần soát của session chính. Phân loại theo `LAB-5 Planning.md` §3 bước 8.

### 4.1 Hệ quả xuôi dòng của quyết định G1 — **để mở, trình G2**

Quyết định tách put-away (PREMISE §1.3, §1.4, duyệt G1) có hai hệ quả mà **phản chứng ở G1 không quét tới**. Hai lô viết mô tả độc lập, ở hai module khác nhau, cùng tìm ra:

| Chỗ | Nguồn | Hệ quả | Trỏ tới |
|---|---|---|---|
| Tập "lô liên quan" của cảnh báo nhiệt | `N4-09` dựng tập này từ `stock_movement`, mà `M3_DB` không có loại movement cho bước nhận | Lô đã nhận nhưng **chưa cất** rơi khỏi tập lô liên quan; ô vị trí ở `API-M6-005` cũng trống cho lô đó | `BL-M6-181` (#108), `BL-M6-051` (#101), `BL-M6-203` (#190) |
| Phân bổ đơn | `N4-04` bước 4.1 mục 5 chỉ nạp lô có `stock_balance` > 0, chọn vị trí lấy hàng theo `stock_balance` | Lô chưa cất chưa có `stock_balance` dù `lot.qty_available` > 0 → chưa rõ có được phân bổ không | `BL-M4-051` (#62), `BL-M4-055` (#66), `BL-M4-150` (#69), `BL-M4-180` (#71) |

**Không sửa PREMISE** — đã chốt ở G1. Hai chỗ này cần quyết định ở G2 trước khi code phần phân bổ và phần cảnh báo nhiệt.

### 4.2 PREMISE kể thiếu một phía — **bổ sung, không sửa**

| Mục PREMISE | Phần đã ghi | Phần tìm thêm |
|---|---|---|
| `CQ35` (replay `Idempotency-Key`) | hai nguồn: `api-conventions.md:162` ↔ `common-spec.md:52` | nguồn thứ ba `API-M1-023_API.md` (trả bản đã lưu kèm `M_0008`); `error-codes.md:58` định nghĩa `M_0008` lệch với spec màn; `common-spec.md:50` nhắc "nhận lại `422` cũ do replay" |
| §1.7 (mốc kết thúc ca) | hai nghĩa: `completed_at_jst` ↔ `shift_end_jst` | nghĩa thứ ba: `ADR-008` và `S3-03` Q10 quét việc quá hạn theo "`shift_end_jst` + 8h", còn `M5_DB` tính 8h từ `delivery.completed_at_jst`. Thêm: `API-M5-026` chuyển tuyến `completed` ở điểm giao cuối, `API-M5-028` trả 409 khi tuyến không "đang chạy" — có thể luôn 409 |

Câu hỏi gửi khách phải kèm **đủ mọi phía**, nếu không khách trả lời cho một cặp mà vẫn còn cặp thứ hai hở.

### 4.3 Chỗ hở lược đồ — cùng họ với `stock_unit` ở PREMISE §1.1

| Chỗ hở | Bằng chứng | Trỏ tới |
|---|---|---|
| **Phiên đăng nhập không có bảng nào** | `M9_DB.md` Q2 `closed — G6` nói phiên lưu PostgreSQL kèm `last_activity_at`, nhưng cả 7 bảng M9 không bảng nào giữ phiên. Ảnh hưởng `session_expires_at`, `locked_at`, luật "khoá thì mọi API trả `M_0003`" | `BL-M9-050` (#137), `054`, `056`, `150`, `180` |

Lô viết mô tả **không tự thêm bảng** — đúng theo luật. Cần quyết ở G2.

### 4.4 Mâu thuẫn giữa hai nguồn — **ghi cả hai, không tự hoà giải** (C4)

Các lô báo về hơn 50 cặp. Những cặp chạm nhiều issue nhất:

| Cặp | Nói gì khác nhau | Trỏ tới |
|---|---|---|
| `S2-02_SPEC.md:209`, `:294`, `:316` ↔ `API-M2-011_API.md:39`, `:96`, `:100` | spec màn: thiếu trường truy xuất thì 422 `M_0203` **chặn ghi**; hợp đồng API: chỉ đặt `will_quarantine` và `warnings[M_0203]` | `BL-M2-052` (#2), `206`, `207`, `208`, `150` |
| `S1-28`/`S1-29` ↔ `N4-09`, `N1-54`, `API-M6-006` | mức nghiêm trọng của case kế thừa từ alarm, hay sao một lần lúc mở rồi không đồng bộ | `BL-M6-051` (#101), `052`, `203`, `205`, `180` |
| `S1-33_SPEC.md` ↔ `N1-62_TECH.md`, `API-M7-005_API.md` | số bò sai: 400 `M_0014` không sự kiện, hay 422 `M_0701` kèm `trace_seed_rejected` | `BL-M7-052` (#114), `150` |
| `S2-07` C-9 ↔ `API-M4-016`, `S2-08`, `S2-09` | cờ vô hiệu suy từ có mã deviation hay không, hay đọc `invalidated_reason_code` | `BL-M4-053` (#64) |
| `M5_DB` Q5 ↔ `S3-03`, `API-M5-026` | hàng bị từ chối ghi vào dòng giao, hay `REFUSED` vẫn tính là hoàn tất giao | `BL-M5-003` (#77), `BL-M5-150` (#92) |
| `M9_DB.md:34` ↔ `:46`, `:486` | lịch sử sửa đổi và ma trận quyền nói **11 vai trò**; danh sách bảng và câu hỏi mở Q1 nói **8 vai trò (RFP p.07–08)** | `BL-M9-180` (#145) |
| Bảy API của M4 (`-001`, `-003`, `-005`, `-007`, `-009`, `-010`, `-011`) | dòng 400 ghi "hoặc thiếu `If-Match`" trong khi **không API nào nhận `If-Match`** — chép nhầm từ mẫu | `BL-M4-150` (#69) |
| `API-M5-026_API.md:87` | `"signature\|photo"` có dấu `\|` **chưa thoát**, làm dòng bảng thừa hai ô | `BL-M5-151` (#93) (bản chép đã gộp và khai báo) |

Chi tiết từng cặp nằm ở mục `## Tham chiếu` của chính file mô tả. **Không file nào tự chọn một cách hiểu.**

### 4.5 Chỗ đã sửa trong vòng này

| Chỗ hở | Sửa thế nào |
|---|---|
| Phủ theo màn bỏ sót **12 function nền** loại `Background` không gắn màn nào | Thêm khối số `020–039`: 11 issue vào `M9`, `N4-18` vào `DRV`. Thêm luật kiểm "mọi function Core phải có đường vào backlog" |
| Nhãn `state-change` gắn tay, chỉ được 2 mục | `work/sync-labels.py` suy nhãn **từ nội dung** (DoD có đòi "sự kiện riêng"); luật kiểm hai chiều. Kết quả **104 mục** |
| Luật "tham chiếu `cột DB` phải tra được" **lách được** — hai lô viết vòng để khỏi bị quét | Thêm cách khai `[sẽ tạo ở BL-…]`; luật đổi thành đòi **điều kiện** (ô có ≥1 định danh tra được trong Database Design) thay vì đòi một dạng viết |

### 4.6 Còn để mở — cần quyết ở G2

| # | Việc phải quyết | Vì sao không tự quyết |
|---|---|---|
| 4.6.1 | **Ranh giới giữa issue nhóm API (`150`), issue lược đồ (`180`) và sub-issue BE (`200+`)** | Nguyên tắc chia của tôi không định nghĩa. Bốn lô tự chia theo ba cách khác nhau rồi xin xác nhận. G2 duyệt độ hạt, đây đúng chỗ của nó |
| 4.6.2 | **20 function `Background` có gắn màn** có nên tách issue riêng không | Hiện chúng nằm trong issue màn. Hai lô báo thiếu chỗ bám cho worker (`N4-23`, `N4-27`, `N4-28`). Tách thì mịn hơn nhưng thêm ~20 mục |
| 4.6.3 | **`BL-PLAT-002` (#147) gom hai việc lớn** (lớp API chung + 9 đối tượng DB) | Lô viết đề nghị tách; ranh giới đã ghi sẵn trong file |
| 4.6.4 | **Trường hiển thị không tra được nguồn** rải khắp các epic (nhiều nhất: `name_ja` của loại báo cáo, `widget_code` và `queue_code` của `S1-02`, `recipients_count` của `S1-34`, tên sự kiện của hàng chục thao tác) | C2 gọi đây là **lỗi chặn**, không phải câu hỏi mở. Mỗi chỗ đã ghi trong `## Tham chiếu` của file; cần quyết gom thành issue làm rõ hay trả lại thiết kế |

### 4.8 Kết quả đọc mù — **chỗ hở lớn nhất của B3**

15 mục được đọc bởi người **chỉ** có file mô tả trong tay, không có spec LAB-4 (tiêu chí chọn mẫu và kết quả đầy đủ: `work/checks/R9/blind-read-sample.md`, `blind-read-result.md`).

**Vòng R9 (trước hướng C): 9 `KHÔNG LÀM ĐƯỢC` · 6 `PHẢI ĐOÁN` · 0 `ĐỦ ĐỂ LÀM`.**

**Vòng R10 (sau hướng C, cùng 15 mục, cùng prompt): 6 `KHÔNG` · 9 `PHẢI ĐOÁN` · 0 `ĐỦ`** (`work/checks/R10/blind-read-result.md`). Bốn mục lên hạng, một mục **xuống** — `BL-DRV-020` (#17), vì khối `## Nền` nói thẳng hạ tầng chưa chốt nên người đọc soi kỹ hơn và đếm ra "chín mục kiểm" chỉ có bảy.

Bốn lỗ cắt ngang:

| # | Lỗ | Sửa được bằng cách viết lại mô tả? |
|---|---|---|
| 1 | Không mục nào nói **stack, framework, công cụ migration, test runner, môi trường thử, tài khoản thử, IdP thử** — cả 15 người đọc đều hỏi | **Được** — thiếu hẳn một nhóm việc nền tảng |
| 2 | Mục **nhóm API, lược đồ DB, lớp test** là **mục lục trỏ sang spec LAB-4**, không mang nội dung | **Không** — xem dưới |
| 3 | Không mục nào **dựng dữ liệu mẫu / fixture** mà hàng loạt DoD lại đòi | **Được** |
| 4 | DoD kiểu "mã lỗi tương ứng của spec", "môi trường thật", "mọi chuỗi giao diện" **không kiểm được** | **Được** |

**Lỗ số 2 là mâu thuẫn ở mức thiết kế, không phải lỗi của người viết.** Kho mã nộp là **public**; spec LAB-4 là tài liệu nội bộ, không đẩy lên được (`LAB-5 Planning.md` §1.3.1). Mọi mục cần hợp đồng API, kiểu cột hay bố cục màn vì thế **không thể tự chứa**. Ba hướng, cần bạn chọn ở G2:

| Hướng | Nghĩa là | Giá phải trả |
|---|---|---|
| **A — giữ nguyên** | Board là công cụ quản lý cho đội **đã có** spec trong tay; issue là con trỏ | Người ngoài mở board không dựng được gì. Đề bài đòi issue "đủ nhỏ để theo dõi", không đòi tự chứa — nhưng người chấm cũng là người ngoài |
| **B — chép nội dung cần thiết vào issue** | Đưa đường dẫn endpoint, kiểu cột, mã lỗi, nội dung AC vào chính mô tả | Spec LAB-4 là **tự viết**, không phải RFP, nên không vi phạm luật cấm chép RFP. Nhưng khối lượng rất lớn và dễ lệch khi spec đổi |
| **C — ở giữa** | Mỗi issue mang **tối thiểu đủ để bắt đầu**: đường dẫn + phương thức + vai + mã lỗi cho API; kiểu cột + NOT NULL cho DB; nội dung AC cho test. Phần dài vẫn trỏ spec | Vừa sức, nhưng phải định nghĩa "tối thiểu" cho từng loại mục |

**Sáu mục còn đỏ sau hướng C, chia ba nhóm — chỉ nhóm đầu là việc có thể thêm:**

| Nhóm | Mục | Hướng C giải được không |
|---|---|---|
| Thiếu **hạ tầng** | `BL-M1-211` (#157), `BL-DRV-020` (#17) | **[QUYẾT ĐỊNH G2] thêm epic `SETUP`** — epic thứ 13 gom: dựng monorepo, công cụ migration, test runner, môi trường thử, IdP thử, tài khoản mỗi vai, bộ dữ liệu mẫu |
| Mục **nhóm API** cần thân request/response | `BL-M4-150` (#69), `BL-M1-150` (#28), `BL-M3-150` (#57) | **[QUYẾT ĐỊNH G2] rót thêm thân API** — rút lược đồ request/response từ 83 file API Design vào chính mục, tức sang hướng B cho riêng loại này. Spec LAB-4 là tự viết nên không phạm luật cấm chép RFP |
| **Sub-issue FE** trỏ issue cha | `BL-M7-207` (#200) | **Không, theo thiết kế.** Phép đo đọc một file nên luôn đỏ; trong kho nộp thật người đọc mở được issue cha |

Ngoài ra, lỗ 1 và 3 nghĩa là backlog **thiếu hẳn một nhóm việc**: dựng dự án, môi trường, tài khoản thử, dữ liệu mẫu. Chưa thêm vào backlog vì nó **đổi số epic** — thứ G2 duyệt.

### 4.9 Lỗi công thức của PREMISE §1.3 — **đã sửa sau G1**

Một lượt đọc mù nêu: `stock_balance.qty_on_hand` là **tồn hiện tại**, giảm theo `ship_out`, `transfer`, `scrap_out`. Nên công thức bản 7 — `lot.qty_received` − Σ `qty_on_hand` — **phình lên sau khi hàng xuất kho**: lô nhận 100, cất đủ 100, xuất 40 thì công thức ra 40 "chưa cất", trong khi lô đã cất xong.

| | |
|---|---|
| Công thức đúng | `lot.qty_received` − Σ `stock_movement.qty` của lô với `movement_type = putaway` |
| Vì sao | Chỉ `putaway` là **lượng đã từng được cất**, không bị các bước sau trừ đi. Năm trong tám giá trị `movement_type` làm `qty_on_hand` giảm |
| Kéo theo | §1.5 (luồng sửa sai một `putaway`) trở lại là **điều kiện cần** của §1.3 — công thức cộng `putaway` sẽ đếm thừa nếu một lệnh cất bị đảo mà không trừ ra được. Bản 7 từng thu §1.5 lại với lý do "movement bù không còn là chỗ dựa", lý do đó sai cùng gốc |
| Chưa phủ | Lô cách ly đi `quarantine_transfer` lúc nhận nên **có `stock_balance` mà chưa từng có `putaway`** — đúng phần `CQ36` hỏi |
| Bằng chứng | `work/evidence/premise-evidence.md` `EV-18`, raw `ev-cong-thuc-chua-cat.md` |

**Đây là sửa một dòng đã được duyệt ở G1.** Bản 6 của PREMISE tính theo movement là đúng; bản 7 tôi đổi sang tồn hiện tại và làm hỏng. Sáu vòng soát Codex và 11 lô viết mô tả đều không thấy. `BL-M3-002` (#44) đã cập nhật công thức và thêm hai ca test bắt đúng lỗi này.

### 4.10 Một lỗi mô tả đã sửa ngay

`BL-M5-001` (#76) tự mâu thuẫn: mục tiêu viết "issue này không tự chọn phương án", nhưng DoD lại đòi văn bản viết **theo phương án đã chọn**. Người đọc mù đánh giá `KHÔNG LÀM ĐƯỢC` vì không biết đầu ra là quyết định hay là tập phương án. Đã viết lại cho đầu ra rõ: **soạn hai phương án kèm hệ quả, trình người duyệt, rồi mới ghi quyết định** — hai việc, hai thời điểm.

### 4.7 Câu hỏi khách còn mở — đã có issue làm rõ

| Mã | Issue làm rõ | Số issue bị chạm |
|---|---|---|
| `CQ30` | `BL-M6-002` (#99) | 32 |
| `CQ31` | `BL-M1-002` (#19) | 14 |
| `CQ32` | `BL-M5-004` (#78) | 32 |
| `CQ35` | `BL-PLAT-001` (#146) | 142 |
| `CQ36` | `BL-M3-003` (#45) | 38 |

`CQ35` chạm 142 trên 172 mục vì gần như mọi thao tác ghi đều dùng `Idempotency-Key`. Các mục chỉ chặn phần kiểm **mã trạng thái của lần gửi lại**; phần "không tạo bản ghi thứ hai" vẫn làm được.

### 4.11 Chuyển đổi dữ liệu (M10) bị break mỏng — phát hiện ở bước quy giờ

So với ước lượng LAB-1, epic `M10` chỉ bằng **một nửa**: LAB-1 có `F1-20` bảng điều khiển
migration và đối soát (189 giờ) cộng `F4-02` thực thi migration và ghi lineage (109 giờ);
backlog mới có 3 mục — hai màn mức B (`S1-40`, `S1-41`) và một mục lược đồ `M10_DB`.

**Không có mục nào** cho: script chuyển đổi từ hệ cũ, bước đối soát sau khi chạy, và lần
chạy thử trên dữ liệu thật. Đây là chỗ hở của chính bước break task, không phải chỗ LAB-1
ước thừa.

**Không vá bằng cách nâng giờ của ba mục đang có.** Việc chưa được chia thì chưa có mục để
gắn giờ; nâng số chỉ làm con số đẹp lên trong khi backlog vẫn thiếu việc. Phải thêm mục ở
vòng break sau — ghi thành việc phải làm, không để tự nhớ.

*Phát hiện này đến từ phép so §5 của `estimate.md`, tức sau khi backlog đã lên board. Nó cho
thấy phép so với ước lượng cũ là một bộ kiểm thật cho bước break, không chỉ là một mục phải nộp.*

---

## 5. Bốn quyết định kỹ thuật — trình G2

Bốn mục này `LAB-5 Planning.md` §1.4 đánh dấu `[ĐỀ XUẤT]`, tức **của tôi chứ không phải đề bài đòi**. Trình từng mục, không duyệt gộp (C4).

### 5.1 (C) Label cho 8 ràng buộc đặc thù — **CHỐT: gắn cả 8** [G2, 2026-10-26]

Ràng buộc **giờ lái xe tải từ 2024** là `[FACT đề lab]` — đã tách thành epic riêng `BL-DRV-000` (#4), không phải quyết.

Còn **8 điểm rẽ nhánh đặc thù** của Product B: 3 dải nhiệt có phiên bản · 賞味期限 so với 消費期限 · cấm đảo ngược ngày giao · tập quán hợp đồng 1/3 và 1/2 · phân bổ 6 bước nguyên tử · deviation kéo theo cách ly · truy xuất gạo và thịt bò · ngoại tuyến chỉ ở app tài xế.

| Phương án | Được | Mất |
|---|---|---|
| **Gắn label cho cả 8** (đề xuất của tôi) | Lọc board theo ràng buộc; thấy ngay ràng buộc nào chạm bao nhiêu issue | 8 label nữa trên board; phải định nghĩa rõ từng label để người gắn sau không gắn lệch |
| Chỉ giữ epic ràng buộc 2024 | Đúng đủ thứ đề đòi, board gọn | Bảy ràng buộc kia chìm trong mô tả, không đếm được |

### 5.2 (E) Người phụ trách giả định — **CHỐT: trường `Owner (giả định)`** [G2, 2026-10-26]

GitHub không cho gán `assignee` là user không có quyền repo. Đề xuất: trường single-select tự tạo **`Owner (giả định)`** với giá trị lấy từ cơ cấu đội (`team-structure.png`): BE1, BE2, FE1, FE2, QA1, BrSE… `assignee` thật để trống, và `README.md` ghi rõ đây là trường giả lập.

### 5.3 (F) Burndown — **CHỐT: script sinh bảng + ảnh** [G2, 2026-10-26]

GitHub Project v2 không có burndown sẵn. Đề cho phép "ảnh hoặc bảng". Đề xuất: script đọc `gh project item-list --format json` → **bảng + ảnh**, cả hai vào gốc kho, sinh lại được từ dữ liệu Project chứ không vẽ tay.

### 5.4 Quy trình git và publish — **CHỐT: script + leak-scan chặn** [G2, 2026-10-26]

| Việc | Cách làm đề xuất |
|---|---|
| Nhánh | Không commit thẳng nhánh mặc định; mỗi bước một nhánh, conventional commit, không nhắc AI trong message |
| Đồng bộ `Output/` sang kho public | **Bằng script**, không chép tay — kho public chỉ nhận `Output/` cộng issue, không nhận `work/` hay spec LAB-4 |
| Leak-scan | Chạy **trước mỗi lần đẩy**, chặn nếu dính ≥20 từ tiếng Việt hoặc ≥40 ký tự tiếng Nhật trùng RFP |
| Thứ tự | Đẩy backlog lên Project (B4) **trước**, rồi mới đồng bộ tài liệu — để mọi `backlog_id` trong tài liệu đổi được thành `#số` |

---

## 6. Bằng chứng trình G2

| Thứ | Ở đâu | Trạng thái |
|---|---|---|
| Backlog dạng dữ liệu | `work/backlog.csv` | 184 mục — 12 epic · 124 issue · 48 sub-issue |
| Mô tả từng mục | `work/issues/*.md` | 172 file, 2,8 MB |
| Mẫu một epic đầy đủ | `Output/epic-mau-M2.md` | Cây epic + một mô tả nguyên văn |
| Vòng đỏ trước khi sinh backlog | `work/checks/R8/` | 7/7 bộ kiểm đỏ vì thiếu dữ liệu, đúng kỳ vọng |
| Vòng xanh + đọc mù lần 1 | `work/checks/R9/` | Bộ kiểm 7/7 xanh · đọc mù **9 KHÔNG · 6 ĐOÁN · 0 ĐỦ** |
| Sau hướng C + đọc mù lần 2 | `work/checks/R10/` | Bộ kiểm 8/8 xanh · đọc mù **6 KHÔNG · 9 ĐOÁN · 0 ĐỦ** |
| Sau khi sửa công thức §1.3 | `work/checks/R11/` | Cả ba bộ kiểm xanh |
| `rebuild.sh` của B2 | `work/checks/R7/rebuild.log`, `rebuild-lan-2.log` | exit 0 **hai lần liên tiếp** |
| Sổ vòng test | `work/test-summary.md` | R1…R11 |
| Sổ lỗi AI | `work/ai-log.md` | 166 dòng |

