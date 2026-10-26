# PREMISE — backlog LAB-5 khác bản thiết kế LAB-4 ở đâu

| | |
|---|---|
| Trạng thái | **Bản 7 — chờ duyệt G1.** Sửa 5 lỗi chặn của vòng soát thứ sáu, cộng 3 phát hiện khi xét lại quyết định tách put-away |
| Ngày | 2026-10-26 |
| Mốc so sánh | Bộ thiết kế LAB-4 đã nộp, 192 tài liệu `approved` (2026-10-15). Bản đó **không sửa** |
| Nhãn mức suy | Mỗi dòng §1/§2 mang **đúng một** nhãn, và nhãn đó nói về **phương án đề ra**, không phải về bằng chứng của chỗ hở: **[nguồn nói trực tiếp]** nguồn phát biểu chính phương án này · **[suy ra]** phương án là hệ quả tất yếu của nguồn · **[đề xuất]** lựa chọn thiết kế |
| Bằng chứng | `work/evidence/premise-evidence.md` (16 mục `EV-nn`) trên `work/evidence/raw/` (5 file, stdout không cắt, mỗi lệnh kèm mã thoát). **`EV-16`** là phản chứng: mỗi phương án có thể phá thứ gì, **một truy vấn riêng cho mỗi dòng §1** |

## Cách đọc

- **Mã tài liệu** giữ từ LAB-1: `S2-04` (màn), `N4-01` (function), `API-M3-029` (API), `M_0015` (mã lỗi), `R-07.7` (luật SSOT).
- **`RFP p.NN`** là số trang RFP 第2.0版 bản JA. Tài liệu này **chỉ dẫn số trang và mã**, không chép nội dung RFP.
- Đường dẫn `LAB-4/…` là **tài liệu nội bộ, không nộp kèm kho mã này**.

---

## 1. Spec thêm / bỏ / sửa so với LAB-4

| # | Spec | Thêm/Bỏ/Sửa | Trước → sau | Nhãn | Vì sao · phản chứng | EV | Backlog |
|---|---|---|---|---|---|---|---|
| 1.1 | Database Design (M2 hoặc M3) | **Thêm** | `API-M2-012` và `S2-03` (`N4-01`) cam kết tạo stock unit, nhưng Database Design **không có bảng và không nhắc tên nó lần nào** → thêm bảng `stock_unit`: sinh lúc xác nhận nhận hàng, bất biến, khoá ngoại tới đúng một lô | **[đề xuất]** | Chỗ hở có nguồn trực tiếp: RFP từ điển `33-R-07-glossary.md:25` định nghĩa 「unit」; `04-function-list.csv:139` có `N4-01`; `proposed-architecture.md:181` liệt kê `stock_unit` là bảng trạng thái; tìm trong Database Design → KHÔNG TÌM THẤY. Cấu trúc bảng là lựa chọn của tôi. **Phản chứng:** `S2-03` và `API-M2-012` là **producer đã phụ thuộc** — thêm bảng là làm lược đồ khớp hợp đồng đã có | `EV-01`, `EV-16` | `BL-M2-003` |
| 1.2 | `M1_DB` bảng `location` · `S2-04` · `API-M3-028` | **Thêm** | `fefo_access_rank` hiện trên màn và trong `items[]` của API nhưng **không có cột nguồn** → thêm cột `fefo_access_rank` (integer, cho `NULL`) vào `location`; migration đặt `NULL` cho mọi vị trí đang có | **[đề xuất]** | Chỗ hở có nguồn trực tiếp: `RFP p.22` FR-INV-02 đòi 温度帯・状態・容量・FEFO アクセス可能性; SSOT `N2-10` giữ đủ bốn; `grep access_rank` trên `M1_DB` → 0. Kiểu cột và cách migration là lựa chọn của tôi. **Phản chứng:** 4 file nhắc `API-M1-041`; cột cho `NULL` nên không phá — muốn lộ ra API thì phải sửa hợp đồng đó | `EV-03`, `EV-16` | `BL-M1-001`, `BL-M3-001` |
| 1.3 | `S2-04` (các ô dùng `qty_available`) | **Sửa** | "Phần chưa cất" lấy từ `lot.qty_available` → tính từ **movement đã cất**: `lot.qty_received` − Σ `stock_movement.qty` của lô với `movement_type = putaway` | **[suy ra]** | Thiết kế tự nói đại lượng này tồn tại: `api-list.md:172` (`API-M3-027`) — "Danh sách lô đã nhận, **chưa cất hàng**"; `S2-04:208` — "**chưa có `stock_balance` cho phần chưa cất**". `M2_DB:358` định nghĩa `qty_available` là *lượng còn phân bổ được* (tồn thực trừ phần đã phân bổ), **không** liên quan đã cất hay chưa. **Vì sao không lấy thẳng `stock_balance`:** `qty_on_hand` là **tồn hiện tại**, giảm theo `ship_out`, `transfer`, `scrap_out` (`M3_DB` §4.2, enum `movement_type`) — nên `qty_received` − Σ `qty_on_hand` **phình lên sau khi hàng xuất kho** và lô đã cất xong lại hiện là "chưa cất". Chỉ movement `putaway` mới là **lượng đã từng được cất**, không bị các bước sau trừ đi. **Tra RFP:** chương nhập kho `RFP p.13–17` không mô tả bước cất riêng, `未格納 / 格納済 / 格納待ち` toàn RFP → **rỗng** — RFP không có đại lượng này. **Phản chứng:** 35 file đọc `stock_movement`; phương án không đổi bảng nào, chỉ đổi cách cộng. **Chỗ chưa khép:** lô cách ly nhận `quarantine_transfer` lúc nhận nên **có `stock_balance` mà không có `putaway`** — đúng phần `CQ36` hỏi; công thức này chỉ áp cho lô đi đường cất thường cho tới khi khách trả lời | `EV-07`, `EV-16`, `EV-17`, `EV-18` | `BL-M3-002` |
| 1.4 | `S2-04`:208 · SSOT `business-rules.md` R-07.7 | **Sửa — khai lệch nguồn** | Viết lại một nghĩa: `S2-03` chọn **vị trí nhận ban đầu** (`lot.location`, bất biến), **không** tạo `stock_balance`; `S2-04` là bước cất, `API-M3-029` ghi movement `putaway` và sinh `stock_balance`. **Lệch với R-07.7** — khai rõ kèm errata (§5) | **[suy ra]** | Bốn nguồn của LAB-4 đều nói tách: `S2-04:56` — màn để "**cất lô đã nhận vào vị trí kho**"; `:178` — "một lệnh = **một movement `putaway`**"; `API-M3-027` — lọc "qty chưa có `stock_balance`"; `M2_DB` Q2 `closed — G6` — "`lot.location` là vị trí nhận ban đầu, **bất biến**; vị trí hiện tại lấy từ `stock_balance`". `R-07.7` "gộp" là cách đọc của prototype. **Tra RFP cả hai chiều:** `RFP p.13–17` không chia nhận và cất thành hai bước, cũng không bắt gộp; luật vị trí put-away nằm ở `RFP p.22` (`FR-INV-01`, `FR-INV-02`) và chỉ nói về vị trí put-away. → RFP **không quyết** việc này; đây là quyết định thiết kế, và errata §5 phải khai đúng vậy. **Phản chứng:** cùng truy vấn 25 file với §1.3 | `EV-06`, `EV-07`, `EV-16`, `EV-17` | `BL-M3-002`, `BL-M2-003` |
| 1.5 | `M3_DB` · `COMMON_DB` | **Thêm** | Luồng sửa sai một `putaway` chưa đủ để dựng → chốt ba thứ: **`movement_type` của movement bù**, **endpoint nào tạo nó**, **cách công thức §1.3 trừ nó ra** | **[đề xuất]** | Cơ chế đảo đã có: `COMMON_DB:275` — "Sửa sai = sự kiện đảo, không có UPDATE"; `reason_category` có giá trị `reversal`; đường join có sẵn qua `M3_DB:296` `business_event_id` (FK `:324`). Ba câu cần chốt là của tôi. **Phản chứng:** 35 file đọc `stock_movement`. **Mục này là điều kiện cần của §1.3, không phải việc phụ:** công thức cộng movement `putaway` sẽ đếm thừa nếu một lệnh cất bị đảo mà không có cách trừ ra. *Bản 7 từng thu mục này lại với lý do "movement bù không còn là chỗ dựa của công thức" — lý do đó sai cùng gốc với §1.3* | `EV-08`, `EV-16`, `EV-18` | `BL-M3-002` |
| 1.6 | `S1-21` | **Sửa** | `S1-21` không mô tả màn làm gì khi `API-M5-003` trả 409 `M_0015` lúc đơn không còn `allocated` → tải lại **toàn bộ** trạng thái chọn ở vùng `C-3…C-8`; **không** giữ lại tập đơn còn hợp lệ, vì hợp đồng lỗi không trả về đơn nào gây lỗi | **[đề xuất]** | Chỗ hở có nguồn trực tiếp: `RFP p.34` FR-SCH-01 là nguồn luồng lập tuyến; `API-M5-003_API.md:73` có 409 `M_0015`; `S1-21_SPEC.md:207` dùng mã này cho `D-15`, `D-2`, `D-13`, `E-4` — không cho vùng chọn đơn. **Phản chứng:** `M_0015` được **45 file API** dùng làm mã chung "trạng thái không cho phép thao tác" → **bác** mọi phương án tách nghĩa mã | `EV-12`, `EV-16` | `BL-DRV-001` |
| 1.7 | `S3-06` · `API-M5-028` · `M5_DB` | **Sửa** | Chưa xác định `API-M5-028.completed_at_jst` **có đồng nhất với `driver_hours_ledger.shift_end_jst` hay là một mốc riêng**; nếu riêng thì chưa có nơi lưu → chốt điều đó trước khi dựng | **[đề xuất]** | `grep 'shift_completed_at\|shift_end_jst'` toàn specs → 25 dòng: `shift_completed_at` chỉ ở response (`S3-06:133`, `api-list:245`); **`shift_end_jst` là cột có thật** (`M5_DB:207`, `:589`). **Phản chứng:** `API-M5-026:76` **đã** ghi `route_status = completed` và `driver_hours_ledger` ở điểm giao cuối; `N1-39_TECH:369` ghi **một dòng** `period_scope = day` → **bác** phương án giao hai việc đó cho `API-M5-028`. **Tra RFP:** `勤務終了 / 終業時刻 / シフト終了` toàn RFP → **đúng một dòng** (`01-S1-01` mô tả hiện trạng tải ảnh POD), không quy định mốc hệ thống nào. Ngược lại `RFP p.31` nêu đúng lỗi hiện trạng là gộp 運転時間・休息期間・待機時間 vào 総労働時間, và từ điển `RFP p.87` ghi 拘束時間/休息期間 **không đồng nghĩa** với 運転時間/休止時間 → coi hai mốc là một mà không chốt là lặp lại chính lỗi RFP yêu cầu bỏ. *Bản 6 viết "không bảng nào lưu" — tìm theo tên chính xác rồi kết luận vắng mặt* | `EV-11`, `EV-16`, `EV-17` | `BL-M5-001` |
| 1.8 | `M8_DB` bảng cột `audit_export` | **Thêm** | Index `idx_audit_export_content_sha256` trỏ `content_sha256` nhưng bảng cột **không có dòng** cho nó → thêm dòng: `char(64)`, CHECK `~ '^[0-9a-f]{64}$'`, cho `NULL` trước khi gói sinh xong, bất biến sau đó | **[suy ra]** | Chỗ hở trực tiếp: có ở ERD (`:70`), Overview (`:117`), ô Ghi chú (`:135`, `:138`), index (`:157`) — không có hàng bảng cột. Kiểu và ràng buộc **suy từ** `manifest_sha256` cùng bảng (`:134`). **Phản chứng:** 5 consumer thật ngoài `M8_DB`; thêm dòng là sửa tài liệu cho khớp ERD và index đã có | `EV-14`, `EV-16` | `BL-M8-001` |
| 1.9 | `M8_DB`:135 | **Sửa** | Dòng `canonicalization_version` có **9 ô** trong khi mọi dòng khác của khối có **10** → thêm ô còn thiếu | **[nguồn nói trực tiếp]** | **[phạm vi: sửa ô bảng]** — không có mặt nghiệp vụ nào để tra RFP. Đếm cùng một thước (`FS='\|'`, `NF-1`): dòng 121–134 và 136–140 đều 10 ô. Sửa ô bảng, không đổi lược đồ. **Phản chứng:** không consumer nào đọc ô trống đó | `EV-14`, `EV-16` | `BL-M8-002` |
| 1.10 | `m6-temperature-haccp/` | **Thêm** | Khối HACCP không dẫn nguồn pháp lý ngoài nào → thêm **một khối nguồn ngoài ở mức module** cho `SRC-EXT-01`, `SRC-EXT-02`: mã · tên · cơ quan · URL · **phiên bản ghi "nguồn không nêu phiên bản"** · ngày truy cập 2026-08-16 · trạng thái 「OPS-LAW-01 chưa xác nhận phạm vi」 | **[đề xuất]** | Chỗ hở trực tiếp: `grep -rEln 'R-06\.\|BR-HACCP'` trên `m6` → 8 file dẫn luật nội bộ, nhưng `grep 'SRC-\|http'` → **exit 1, rỗng**. `RFP p.88` cung cấp đúng 6 trường, **không có cột phiên bản**. Đặt ở mức module là lựa chọn của tôi. **Phản chứng:** không đụng dẫn chiếu `R-06.x` của 8 file; `S1-51` đã làm đúng cách này cho khối giờ lái | `EV-13`, `EV-16` | `BL-M6-001` |

**Không dòng nào ở §1 được áp trước khi bạn duyệt G1.**

---

## 2. Giả định bổ sung

| # | Giả định | Nhãn | Chỗ để ngỏ · phản chứng | EV |
|---|---|---|---|---|
| 2.1 | Thứ tự gợi ý vị trí tách **hai nhánh**: lô khả dụng → lọc dải nhiệt khớp SKU, không phải vị trí cách ly, còn sức chứa; lô `quarantined` / `on_hold` / `held_pending_remeasure` → **chỉ vị trí cách ly cùng dải**. Trong mỗi nhánh xếp theo `fefo_access_rank` tăng dần, `NULL` xuống cuối, cùng hạng theo `location_id` | **[đề xuất]** | `RFP p.22` FR-INV-02 **bắt buộc** tiêu chí tiếp cận FEFO nhưng **KHÔNG TÌM THẤY** chỗ nào nói xếp hạng theo gì. **Phản chứng:** `S2-04:211` — lô bị giữ **vẫn nằm trong danh sách cất**; `:220` — "Lô cách ly **chỉ sang vị trí cách ly cùng dải**" (`M_0308`) | `EV-03`, `EV-16` |
| 2.2 | `v_stock_consistency` là view kiểm tra nội bộ; lệch thì ghi `business_event` cảnh báo và đưa vào báo cáo vận hành, **không chặn giao dịch** | **[đề xuất]** | Tên này xuất hiện **đúng một chỗ** (`M3_DB:240`); **KHÔNG TÌM THẤY** định nghĩa, SQL, lịch chạy hay cách báo lệch ở cả ba lớp. **Phản chứng:** không consumer nào đọc view này | `EV-07`, `EV-16` |

---

## 3. Hướng đã chốt

| # | Vấn đề | Phương án chốt | Quyết định / nguồn trực tiếp | EV |
|---|---|---|---|---|
| 3.1 | Putaway **gộp** vào `S2-03` hay **tách** ở `S2-04`? | **Tách** | **Người dùng chốt 2026-10-26**, nay có thêm bốn nguồn của chính LAB-4: `S2-04:56`, `S2-04:178`, `API-M3-027`, và `M2_DB` Q2 `closed — G6`. **LAB-4 đã ghi sẵn mâu thuẫn này** ở `api-list.md:458` với ghi chú "cần Tech Lead chốt (M2_DB Q2)" | `EV-06` |
| 3.2 | `subtype` của Technical Design? | **Giữ `technical-design` — không sửa** | Nguồn phát biểu trực tiếp: `quy-chuan-thu-muc-spec-github.md:224` và `:311` — bộ chuẩn **đã đổi tên** ở `0.19.0`; việc còn nợ là đổi tên file khuôn (`ISS-67`) | `EV-10` |
| 3.3 | Mã định danh giữ dạng có gạch nối? | **Giữ mã LAB-1** | **Quyết định G0 Q8 của LAB-4**, ghi ở `LAB-4/Output/specs/README.md` mục L1 | `EV-10` |

---

## 4. Chỗ góp ý review LAB-4 cần đọc lại

| Mã | Góp ý nói | Nguồn nói | EV |
|---|---|---|---|
| `L4-5` | 11 file Technical Design lệch khuôn → "trả `subtype` về khuôn" | QC-01 `:224`, `:311` — bộ chuẩn **đã đổi** sang `technical-design` ở `0.19.0`. **Không sửa** (§3.2) | `EV-10` |
| `L4-4` | Ba tài liệu nói ba cách khi thiếu trường truy xuất | `business-rules.md:121` R-07.5 — "chặn xác nhận 'nhận khả dụng'; tạo deviation; **hàng vào cách ly**"; `API-M2-012` làm đúng vậy: 422 **kèm** `created[]`. **Không phải mâu thuẫn** | `EV-09` |

---

## 5. Errata đề xuất cho SSOT — **chưa áp**

| Mã | File | Sửa gì | EV |
|---|---|---|---|
| `E11` | `04_design/business-rules.md` (R-07.7) | Cột "Quy tắc": `S2-03` chọn vị trí nhận ban đầu (không tạo `stock_balance`), `S2-04` là bước cất; giữ câu cũ làm ghi chú phạm vi prototype. Cột "Áp dụng" giữ **`S2-03, S2-04`**. Cột "Nguồn" **tách theo màn** | `EV-06` |

**Ba điều chỉnh so với bản 6:** (a) cột Nguồn **tách theo màn** — `S2-04` put-away dẫn `[FACT p.22 FR-INV-01, FR-INV-02]`, còn `S2-03` vị trí nhận đúng dải nhiệt ghi là **thiết kế LAB-4** (`S2-03` D-6, `M_0206`), **không gọi là FACT p.22** vì `FR-INV-02` nói về vị trí put-away được đề xuất, không quy định vị trí nhận ban đầu; (b) dẫn thêm câu hỏi mở `api-list.md` #3 vốn đã nêu chính mâu thuẫn này; (c) **không** trỏ tài liệu LAB-5 như một nguồn của SSOT.

**Cách áp — bắt buộc:** merge vào mảng có sẵn của `_tools/errata.json`, **không** thay cả file. **Chỉ áp qua `rebuild.sh`** — chạy riêng `apply_errata.py` lần hai sẽ dừng vì `find` không còn khớp. **Không chạy `build_errata.py`**.

---

## 6. Câu hỏi chờ khách

| Mã | Câu hỏi | Vì sao không tự quyết | Chặn | EV |
|---|---|---|---|---|
| `CQ30` | Mức nghĩa vụ HACCP theo quy mô doanh nghiệp là mức nào, ai xác nhận? | `RFP p.88` đòi mọi nghĩa vụ suy từ `SRC-EXT-*` phải tái thẩm định phạm vi (OPS-LAW-01); **KHÔNG TÌM THẤY** chỗ nào nói mức theo quy mô | `BL-M6-001` | `EV-13` |
| `CQ31` | Khả năng tiếp cận FEFO xếp hạng theo gì? | `RFP p.22` bắt buộc tiêu chí nhưng **KHÔNG TÌM THẤY** chỗ nào nói cách xếp hạng | `BL-M1-001`, `BL-M3-001` | `EV-03` |
| `CQ32` | Khi khách **từ chối nhận toàn bộ**: (a) hệ thống tạo bản ghi nào · (b) số lượng nằm ở đâu · (c) `claim` / trả hàng nối với dòng giao thế nào · (d) lần đó có tính là **"lần giao cuối được chấp nhận"** của BR-DATE-01 không? | Mô hình hiện tại **không biểu diễn được `shipment_line` có số lượng 0** (`M4_DB:737` CHECK `qty > 0`); chưa rõ từ chối toàn phần phải biểu diễn bằng dòng khác hay thực thể khác. `RFP p.30` FR-OUT-07 đòi `claim` / trả hàng **nối được** với dòng giao, lô và chứng cứ. Bốn vế ràng buộc lẫn nhau nên **trả lời cùng lúc**, không tách thành bốn câu độc lập | `BL-M5-003` | `EV-04`, `EV-05` |
| `CQ35` | Replay `Idempotency-Key` trả **đúng trạng thái lần đầu** (`api-conventions.md:162`) hay cố định **`200`** (`common-spec.md:52` CM-S03)? | Hai tài liệu chung nói khác nhau. Luật C4 cấm tự hoà giải, và **không được giải bằng số đông tài liệu** | mọi mục có thao tác ghi | `EV-15` |
| `CQ36` | Lô `quarantined` / `on_hold` **có nằm trong danh sách cất của `S2-04`** không, và nếu có thì lọc theo gì? | `API-M3-027` lọc "qty **chưa có** `stock_balance`", nhưng `API-M3-003:59` cho thấy lô `quarantined` **đã có** `balances[]` tại `C-Q-01` (vào đó bằng `quarantine_transfer` lúc nhận), trong khi `S2-04:211` `[GIẢ ĐỊNH]` nói lô đó **vẫn trong danh sách**. Có balance thì không lọt bộ lọc "chưa có balance" | `BL-M3-002`, `BL-M3-001` | `EV-07`, `EV-16` |

### 6.1 Câu hỏi đã rút so với các bản trước

| Mã cũ | Vì sao rút |
|---|---|
| `CQ29` | Tiền đề "RFP tự mâu thuẫn" không đứng được: `R-07.5` và `API-M2-012` cho thấy chặn và cách ly xảy ra **cùng lúc** (§4, `L4-4`) |
| `CQ33` | Không rút — **đã gộp vào `CQ32`** thành bốn vế |
| `CQ34` | Rút: `RFP p.47` giao chủ dữ liệu vị trí cho **warehouse operations**; ánh xạ sang vai `ops_manager` là **giả định của LAB-4 đã được duyệt** (`S1-05_SPEC.md:182`), không phải RFP quy định trực tiếp |
