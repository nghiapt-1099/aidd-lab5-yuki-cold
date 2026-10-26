# Mẫu một epic đầy đủ — `BL-M2-000` (#1) Nhập hàng

Mẫu này cho thấy đúng thứ đã lên GitHub Project. Chọn epic `M2` vì nó nhỏ mà đủ loại:
một dòng thay đổi từ PREMISE, màn mức A có sub-issue, màn mức B, nhóm API, lược đồ DB.

## 1. Cây epic → issue → sub-issue

| backlog_id | Loại | Cha | Màn | Mức | Nhãn | Tiêu đề |
|---|---|---|---|---|---|---|
| `BL-M2-000` (#1) | epic | — | — | — | `epic` | Nhập hàng |
| `BL-M2-003` (#36) | issue | `BL-M2-000` (#1) | — | — | `db,premise-1.1` | Thêm bảng stock_unit vào Database Design |
| `BL-M2-050` (#37) | issue | `BL-M2-000` (#1) | S1-08 | B | `screen,muc-b` | S1-08 — dựng màn theo spec mức B |
| `BL-M2-051` (#38) | issue | `BL-M2-000` (#1) | S1-09 | B | `screen,muc-b` | S1-09 — dựng màn theo spec mức B |
| `BL-M2-052` (#2) | issue | `BL-M2-000` (#1) | S2-02 | A | `screen,muc-a,state-change` | S2-02 — dựng màn theo spec mức A |
| `BL-M2-053` (#39) | issue | `BL-M2-000` (#1) | S2-03 | B | `screen,muc-b,state-change` | S2-03 — dựng màn theo spec mức B |
| `BL-M2-054` (#40) | issue | `BL-M2-000` (#1) | S2-14 | B | `screen,muc-b,state-change` | S2-14 — dựng màn theo spec mức B |
| `BL-M2-150` (#41) | issue | `BL-M2-000` (#1) | — | — | `api,be,state-change` | M2 — dựng 6 API có spec riêng |
| `BL-M2-180` (#42) | issue | `BL-M2-000` (#1) | — | — | `db` | M2_DB — dựng lược đồ nhóm Nhập hàng |
| `BL-M2-206` (#3) | sub-issue | `BL-M2-052` (#2) | S2-02 | A | `be,sub,state-change` | S2-02 — lớp BE |
| `BL-M2-207` (#161) | sub-issue | `BL-M2-052` (#2) | S2-02 | A | `fe,sub` | S2-02 — lớp FE |
| `BL-M2-208` (#162) | sub-issue | `BL-M2-052` (#2) | S2-02 | A | `qa,sub,state-change` | S2-02 — lớp test |

**11 mục** dưới epic: 8 issue · 3 sub-issue. Mọi epic có ≥1 con (luật R-14 — `tkm:estimate-swp` không cộng epic rỗng vào tổng).

## 2. Một mô tả issue đầy đủ, nguyên văn

Đây là nội dung sẽ thành body của issue trên GitHub. Nó có khối `## Nền` (ngăn xếp + thứ chưa chốt),
`## Nguồn trường` (luật C2 — mọi trường hiển thị phải ghi nguồn), `## Cột bảng` (rót từ spec theo hướng C),
`## Định nghĩa xong (DoD)` và `## Tham chiếu` (nơi khai chỗ hở, không bịa).

---

# BL-M2-003 — Thêm bảng stock_unit vào Database Design

## Nền

Ngăn xếp đã chốt ở Architecture Design của LAB-4 — mọi mục trong backlog này dựng trên đó:

| Lớp | Công nghệ | Ghi chú |
|---|---|---|
| Giao diện | **React** SPA (APP-01 PC, APP-02 tablet kho) · **React** PWA (APP-03 tài xế, có Service Worker) | Ba khu vực route, một hệ thiết kế chung |
| Máy chủ | **NestJS** modular monolith, module M1–M10, ranh giới ép lúc build | Luật dùng chung nằm ở gói `packages/domain-rules` |
| Ngôn ngữ | **TypeScript** trên toàn monorepo | |
| Dữ liệu | **PostgreSQL 16** — `business_event` chỉ-thêm cộng các bảng trạng thái | Ảnh POD ở object storage |

**Chưa chốt, phải hỏi trước khi dựng:** công cụ migration, test runner, địa chỉ môi trường thử, tài khoản thử của từng vai, IdP thử. Epic `BL-SETUP-000` (7 mục) nhận những việc này — xem [`task-breakdown.md`](task-breakdown.md) §4.8.

## Mục tiêu

`API-M2-012` và màn `S2-03` (function `N4-01`) cam kết sinh stock unit lúc xác nhận nhận hàng, nhưng Database Design chưa có bảng nào cho nó và cũng không nhắc tên nó lần nào. Làm xong thì `M2_DB.md` có thêm bảng `stock_unit`: sinh cùng giao dịch tạo lô, bất biến, mỗi dòng nối tới đúng một lô. Nhờ vậy `BL-M2-053` (#39) (producer) và `BL-M2-180` (#42) (dựng lược đồ) có chỗ để bám. Việc này là sửa tài liệu thiết kế, chưa viết mã; người làm là người giữ Database Design, người duyệt là Tech Lead.

## Nguồn trường

| Field/kết quả | Loại nguồn | Tham chiếu |
|---|---|---|
| Lô mà một stock unit thuộc về (khoá ngoại) | cột DB | `lot.lot_id` — `M2_DB.md`; mỗi stock unit trỏ đúng một lô, `ON DELETE RESTRICT` theo quy ước chung ở `COMMON_DB.md` |
| Dòng nhập đã sinh ra lô (đường truy ngược) | cột DB | `lot.receipt_line_id` — `M2_DB.md`; stock unit đi tới dòng nhập qua lô, không lưu lặp |
| Dấu vết lúc stock unit được sinh | sự kiện | `business_event` `lot_created` cùng giao dịch `API-M2-012` — `COMMON_DB.md`, `DATA-FLOW_DESIGN.md` bước "Tạo lô". Dùng sự kiện thay cho cột thời điểm riêng để khỏi bịa cột |

## Cột bảng

Rút từ `system-design/database-design/*_DB.md` §4.x của LAB-4.

### `inbound_appointment` — nguồn `M2_DB.md`

| | | | |
|---|---|---|---|
| Tên bảng (logic) | Lịch nhập hàng | Tạo bởi / ngày | pham.trong.nghia / 2026-10-13 |
| Tên bảng (vật lý) | `inbound_appointment` | Sửa bởi / ngày | — |
| Overview | Một dòng cho mỗi lần nhà cung cấp hẹn giao hàng. Điều phối tạo / sửa lịch (N1-16); kho đối chiếu khi xe đến. Trùng mã tham chiếu NCC giữa các lịch chỉ CẢNH BÁO, không chặn (N1-17). | Note | Bảng state: `status` đổi theo event (`appointment_arrived`, `receipt_started`, `receipt_completed`, `appointment_cancelled`); các cột kế hoạch (ETA, dải nhiệt, số dòng) sửa được khi `status = 'scheduled'` (kèm event, N1-16).<br>CHECK: `expected_line_count > 0`. |

| No. | Key | Tên logic | Tên vật lý | Data Type | Size | Not Null | Default | Note |
|---|---|---|---|---|---|---|---|---|
| 1 | PRI | Appointment ID | `appointment_id` | varchar | 30 | Yes |  | Mã sinh bằng sequence, [GIẢ ĐỊNH] dạng `APT-yyyymmdd-nnnn` |
| 2 | FK | Nhà cung cấp | `supplier_id` | varchar | 20 | Yes |  |  |
| 3 |  | Mã tham chiếu NCC | `supplier_reference` | varchar | 50 | Yes |  | Số phiếu / chuyến của NCC. Trùng giữa các lịch → cảnh báo (N1-17), KHÔNG UNIQUE |
| 4 |  | ETA nhà cung cấp | `eta_at` | timestamptz | N/A | Yes |  | N1-16 |
| 5 |  | Dải nhiệt xe dự kiến | `expected_temperature_band` | enum | temperature_band | Yes |  | N1-16 “dải nhiệt xe” |
| 6 |  | Số dòng dự kiến | `expected_line_count` | smallint | N/A | Yes |  | N1-16 |
| 7 |  | Trạng thái | `status` | enum | appointment_status | Yes | 'scheduled' | `scheduled` · `arrived` · `receiving` · `completed` · `cancelled` [GIẢ ĐỊNH] |
| 8 |  | Ghi chú | `note` | text | N/A | No |  |  |
| 9 | FK | Người tạo | `created_by` | uuid | N/A | Yes |  |  |
| 10 |  | Ngày tạo | `created_at` | timestamptz | N/A | Yes | now() |  |
| 11 |  | Ngày cập nhật | `updated_at` | timestamptz | N/A | Yes | now() | Trigger `trg_set_updated_at` |

| No. | Tên index | Cột | Primary key | Unique | Note |
|---|---|---|---|---|---|
| 1 | `pk_inbound_appointment` | appointment_id | Yes | Yes |  |
| 2 | `idx_inbound_appointment_eta_status` | eta_at, status | No | No | Tra cứu / theo dõi lịch theo ngày và trạng thái (N1-15) |
| 3 | `idx_inbound_appointment_supplier_ref` | supplier_id, supplier_reference | No | No | Phát hiện trùng mã tham chiếu NCC (N1-17); KHÔNG unique |

| No. | Tên khoá ngoại | Cột | Bảng đích | Cột đích | Note |
|---|---|---|---|---|---|
| 1 | `fk_inbound_appointment_supplier_id` | `supplier_id` | `supplier` (M1_DB) | `supplier_id` | ON DELETE RESTRICT |
| 2 | `fk_inbound_appointment_created_by` | `created_by` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT |

### `receipt` — nguồn `M2_DB.md`

| | | | |
|---|---|---|---|
| Tên bảng (logic) | Phiếu nhập hàng | Tạo bởi / ngày | pham.trong.nghia / 2026-10-13 |
| Tên bảng (vật lý) | `receipt` | Sửa bởi / ngày | — |
| Overview | Một dòng cho mỗi phiếu nhập; chứa các `receipt_line` (từng SKU / lô). Phiếu nối với lịch hẹn nhưng cho phép NULL (hàng đến không hẹn) [GIẢ ĐỊNH]. | Note | Bảng state: `status` (`open → completed / cancelled`) và `completed_at`, `completed_by` đổi theo event `receipt_completed`; `completed` rồi thì các dòng con bất biến (xem `receipt_line`). Đóng phiếu chỉ khi không còn dòng `variance_pending` (service, `422 M_0211`). |

| No. | Key | Tên logic | Tên vật lý | Data Type | Size | Not Null | Default | Note |
|---|---|---|---|---|---|---|---|---|
| 1 | PRI | Receipt ID | `receipt_id` | varchar | 30 | Yes |  | Mã sinh bằng sequence, [GIẢ ĐỊNH] `RCP-yyyymmdd-nnnn` |
| 2 | FK | Lịch nhập hàng | `appointment_id` | varchar | 30 | No |  | NULL = hàng đến không có lịch hẹn [GIẢ ĐỊNH] |
| 3 | FK | Nhà cung cấp | `supplier_id` | varchar | 20 | Yes |  |  |
| 4 |  | Thời điểm xe đến | `arrived_at` | timestamptz | N/A | Yes |  |  |
| 5 |  | Trạng thái | `status` | enum | receipt_status | Yes | 'open' | `open` · `completed` · `cancelled` |
| 6 |  | Thời điểm hoàn tất | `completed_at` | timestamptz | N/A | No |  |  |
| 7 | FK | Người hoàn tất | `completed_by` | uuid | N/A | No |  |  |
| 8 | FK | Người tạo | `created_by` | uuid | N/A | Yes |  |  |
| 9 |  | Ngày tạo | `created_at` | timestamptz | N/A | Yes | now() |  |
| 10 |  | Ngày cập nhật | `updated_at` | timestamptz | N/A | Yes | now() | Trigger `trg_set_updated_at` |

| No. | Tên index | Cột | Primary key | Unique | Note |
|---|---|---|---|---|---|
| 1 | `pk_receipt` | receipt_id | Yes | Yes |  |
| 2 | `idx_receipt_supplier_arrived` | supplier_id, arrived_at | No | No | Tra phiếu theo NCC / ngày |
| 3 | `idx_receipt_appointment` | appointment_id | No | No | Tìm phiếu của một lịch hẹn. Partial: WHERE appointment_id IS NOT NULL |
| 4 | `idx_receipt_open` | status, arrived_at | No | No | Danh sách phiếu đang mở của ca. Partial: WHERE status = 'open' |

| No. | Tên khoá ngoại | Cột | Bảng đích | Cột đích | Note |
|---|---|---|---|---|---|
| 1 | `fk_receipt_appointment_id` | `appointment_id` | [`inbound_appointment`](#41-inbound_appointment) | `appointment_id` | ON DELETE RESTRICT |
| 2 | `fk_receipt_supplier_id` | `supplier_id` | `supplier` (M1_DB) | `supplier_id` | ON DELETE RESTRICT |
| 3 | `fk_receipt_completed_by` | `completed_by` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT |
| 4 | `fk_receipt_created_by` | `created_by` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT |

### `receipt_line` — nguồn `M2_DB.md`

| | | | |
|---|---|---|---|
| Tên bảng (logic) | Dòng kiểm hàng nhập | Tạo bởi / ngày | pham.trong.nghia / 2026-10-13 |
| Tên bảng (vật lý) | `receipt_line` | Sửa bởi / ngày | — |
| Overview | Một dòng cho mỗi SKU của phiếu nhập. Ghi bảy trường P0 bắt buộc (tình trạng bao gói, số lượng, lô nhà cung cấp, loại hạn, hạn dùng, nhiệt độ thực đo, ảnh), cộng ngày sản xuất, truy xuất theo lane, phân loại số lượng nhận / từ chối / tạm giữ kèm mã lý do, và vị trí cất hàng (put-away gộp vào xác nhận, business-rules R-07.7). Xác nhận dòng tạo ra lô (`lot`) bất biến (N4-01); thiếu / sai trường truy xuất thì lô vào cách ly (N4-02).<br>⚠️ **Đoạn Overview trên chép từ thiết kế LAB-4 và còn ghi "put-away gộp vào xác nhận". Hành vi có hiệu lực của backlog này là **tách**: `S2-03` chọn vị trí nhận (`lot.location`, bất biến), `S2-04` mới là bước cất và sinh `stock_balance` — xem `PREMISE.md` §1.4 và errata `E11`. Giữ nguyên văn đoạn cũ để thấy chỗ lệch, không sửa chữ của nguồn.** | Note | BẤT BIẾN sau xác nhận: khi `status IN ('confirmed','confirmed_quarantined','rejected')` mọi cột ngừng sửa (trigger `trg_receipt_line_freeze`; business-rules R-07.8). Dòng `variance_pending` (lô đã tạo, chờ duyệt lệch số báo) chỉ còn cho đổi `qty_mismatch_approved_by` và `status`, qua API-M2-014. Nhiệt độ thực đo là bằng chứng, không sửa sau khi ghi.<br>CHECK `ck_receipt_line_p0_confirmed`: status IN ('confirmed','confirmed_quarantined','variance_pending') ⇒ packaging_condition, qty_received, supplier_lot, expiry_type, expiry_date, measured_temperature_c, production_date, location_id đều NOT NULL AND jsonb_array_length(photo_refs) >= 1 (N2-03: thiếu P0 chặn xác nhận). Ngoại lệ có chủ đích: `rice_origin`, `beef_individual_id` được phép thiếu ở `confirmed_quarantined` (business-rules R-07.9).<br>CHECK `ck_receipt_line_split`: qty_accepted + qty_rejected + qty_held = qty_received (khi cả bốn NOT NULL). CHECK `ck_receipt_line_reason_required`: qty_rejected + qty_held = 0 OR reason_code IS NOT NULL. CHECK `ck_receipt_line_mismatch`: qty_received = notified_qty OR qty_mismatch_reason IS NOT NULL (khi cả hai NOT NULL). CHECK `ck_receipt_line_manual_reason`: entry_mode = 'scan' OR manual_entry_reason IS NOT NULL (nhập tay phải có lý do).<br>CHECK `ck_receipt_line_mismatch_approved`: dòng đã nhận (`status IN ('confirmed','confirmed_quarantined')`) mà `qty_received <> notified_qty` (cả hai NOT NULL) thì `qty_mismatch_approved_by` NOT NULL — lệch số báo phải được người có thẩm quyền duyệt (RFP p.16 FR-REC-03; N2-08; DX-06). Xác nhận dòng lệch số báo vẫn tạo lô (hàng có thật) nhưng lô ở `on_hold` và dòng ở `variance_pending` tới khi API-M2-014 duyệt [U6] (người duyệt `quality` ≠ người xác nhận, `M_0010`); phiếu (`receipt`) không đóng được khi còn dòng `variance_pending` (`422 M_0211`). CQ21 hỏi khách vai trò duyệt.<br>CHECK `ck_receipt_line_held_lot`: `held_lot_id IS NULL OR qty_held > 0`; và `status IN ('confirmed','confirmed_quarantined','variance_pending') AND qty_held > 0` ⇒ `held_lot_id` NOT NULL.<br>Số lượng `qty_held` > 0: xác nhận dòng (API-M2-012) tạo **lô riêng** ở trạng thái `on_hold` (`lot.source_type = 'receipt_hold'`, `qty_received = qty_held`, đặt ở vị trí cách ly cùng dải `is_quarantine` nếu có vị trí `open`, không thì giữ vị trí nhận [U4], lý do nhóm `inbound_hold`), trỏ bằng `held_lot_id`; lô này nằm trong truy xuất / recall, không phân bổ được; giải phóng / huỷ qua API-M3-020/021. Giả định mặc định = có (RFP p.16; CQ16, mục 5 Q1). |

| No. | Key | Tên logic | Tên vật lý | Data Type | Size | Not Null | Default | Note |
|---|---|---|---|---|---|---|---|---|
| 1 | PRI | Receipt line ID | `receipt_line_id` | varchar | 30 | Yes |  | Mã sinh bằng sequence, [GIẢ ĐỊNH] `RCL-yyyymmdd-nnnnn` |
| 2 | FK | Phiếu nhập | `receipt_id` | varchar | 30 | Yes |  |  |
| 3 |  | Số dòng | `line_no` | smallint | N/A | Yes |  | Thứ tự trong phiếu; UNIQUE (receipt_id, line_no) |
| 4 | FK | SKU | `sku_id` | varchar | 20 | Yes |  |  |
| 5 |  | Trạng thái dòng | `status` | enum | receipt_line_status | Yes | 'pending' | `pending` (chưa kiểm) · `inspected` (đã ghi kiểm, chưa xác nhận) · `confirmed` (nhận, đã tạo lô khả dụng) · `confirmed_quarantined` (nhận, lô vào cách ly) · `variance_pending` (nhận, lô đã tạo nhưng lệch số báo chưa được duyệt — chờ API-M2-014; lô của dòng ở `on_hold` tới khi duyệt, không phân bổ được [U6]; sau duyệt → `confirmed` hoặc `confirmed_quarantined` theo trạng thái lô) · `rejected` (từ chối cả dòng) [GIẢ ĐỊNH tên giá trị] |
| 6 |  | Số lượng NCC báo trước | `notified_qty` | numeric | 12,3 | No |  | Để đối chiếu tổng (N2-08) |
| 7 |  | P0-1 Tình trạng bao gói | `packaging_condition` | enum | packaging_condition | No |  | `good` (良好) · `damaged` (損傷) · `soiled` (汚損) [GIẢ ĐỊNH — RFP không liệt kê giá trị]. Chỉ ghi nhận, không tự chặn (business-rules R-07.11) |
| 8 |  | P0-2 Số lượng thực đến | `qty_received` | numeric | 12,3 | No |  | CHECK >= 0. Gồm nhận + từ chối + tạm giữ. Đơn vị theo `sku.package_unit` |
| 9 |  | P0-3 Lô nhà cung cấp | `supplier_lot` | varchar | 50 | No |  | Chép sang `lot.supplier_lot` |
| 10 |  | P0-4 Loại hạn dùng | `expiry_type` | enum | expiry_type | No |  | Phải bằng `sku.expiry_type`, khác thì chặn xác nhận (business-rules R-07.3) — kiểm ở service (khác bảng) |
| 11 |  | P0-5 Hạn dùng | `expiry_date` | date | N/A | No |  | Chép sang `lot.expiry_date` |
| 12 |  | P0-6 Nhiệt độ thực đo (°C) | `measured_temperature_c` | numeric | 4,1 | No |  | Ngoài dải ⇒ chặn nhận khả dụng, tạo deviation, lô vào cách ly (BR-TEMP-02, business-rules R-07.5) |
| 13 |  | P0-7 Ảnh kiểm hàng | `photo_refs` | jsonb | N/A | Yes | '[]'::jsonb | Mảng `{object_key, sha256, taken_at}`; ảnh nằm ở kho đối tượng, DB chỉ giữ tham chiếu + hash. Cần >= 1 phần tử khi xác nhận (CHECK ở Note bảng) |
| 14 |  | Ngày sản xuất | `production_date` | date | N/A | No |  | Bắt buộc để tính cửa sổ giao (business-rules R-07.2); chép sang `lot.production_date`. Ngày sản xuất gốc không được đổi (BR-EXP-01) |
| 15 |  | Xuất xứ gạo | `rice_origin` | text | N/A | No |  | Chỉ lane `rice` (BR-TRACE-01…03 p.40). Thiếu ⇒ vào cách ly (business-rules R-07.9) |
| 16 |  | Số định danh cá thể bò | `beef_individual_id` | char | 10 | No |  | Chỉ lane `beef`. Định danh pháp định bên ngoài, không phải khoá ngoại (không FK). CHECK `beef_individual_id ~ '^[0-9]{10}$'` — sai định dạng ⇒ chặn xác nhận, không tự làm tròn (N1-59) |
| 17 |  | Số lượng chấp nhận | `qty_accepted` | numeric | 12,3 | No |  | CHECK >= 0. = `lot.qty_received` của lô tạo ra |
| 18 |  | Số lượng từ chối | `qty_rejected` | numeric | 12,3 | No |  | CHECK >= 0 |
| 19 |  | Số lượng tạm giữ | `qty_held` | numeric | 12,3 | No |  | CHECK >= 0. > 0 thì tạo lô tạm giữ riêng, trỏ bằng `held_lot_id` (H9; xem mục 5 Q1) |
| 20 | FK | Lô tạm giữ | `held_lot_id` | varchar | 30 | No |  | Lô riêng `on_hold` sinh từ `qty_held` lúc xác nhận (H9, CQ16). NULL khi `qty_held = 0`. CHECK `ck_receipt_line_held_lot` ở Note bảng |
| 21 | FK | Mã lý do | `reason_code` | varchar | 30 | No |  | Lý do từ chối / tạm giữ (FR-REC-03 p.16); nhóm `inbound_reject` · `inbound_hold`; bắt buộc khi `qty_rejected > 0` hoặc `qty_held > 0`. Nhóm `inbound_qty_mismatch` **không** dùng cho cột này: lệch số báo chỉ bắt `qty_mismatch_reason` [U5]. Lô tạm giữ ghi cùng mã lý do nhóm `inbound_hold` ở `lot_status_history` |
| 22 |  | Lý do lệch số báo | `qty_mismatch_reason` | text | N/A | No |  | Bắt buộc khi `qty_received <> notified_qty` (business-rules R-07.6: không chặn, bắt buộc lý do) |
| 23 | FK | Người duyệt lệch số | `qty_mismatch_approved_by` | uuid | N/A | No |  | RFP p.15 P1 “hoặc cần phê duyệt có thẩm quyền” (FR-REC-03, N2-08): BẮT BUỘC khi `qty_received <> notified_qty` và dòng đã nhận (CHECK `ck_receipt_line_mismatch_approved`); khác `confirmed_by` (`M_0010`). NULL khi không lệch |
| 24 | FK | Vị trí cất hàng | `location_id` | varchar | 20 | No |  | Chọn lúc xác nhận; bắt buộc khi `qty_accepted > 0` hoặc `qty_held > 0`; chỉ vị trí đúng dải nhiệt của SKU (business-rules R-07.7). Vị trí cách ly cho `confirmed_quarantined`; không có vị trí cách ly `open` cùng dải thì lô cách ly / `on_hold` giữ vị trí nhận, S1-15 hiện 移動待ち [U4] |
| 25 | FK | Phiên bản ngưỡng nhiệt đã dùng | `policy_version_id` | uuid | N/A | No |  | `policy_version` (type = temperature) tại thời điểm giao dịch (ADR-005): đánh giá lại sau này không đổi kết quả |
| 26 | FK | Người xác nhận | `confirmed_by` | uuid | N/A | No |  |  |
| 27 |  | Thời điểm xác nhận | `confirmed_at` | timestamptz | N/A | No |  |  |
| 28 |  | Cách nhập | `entry_mode` | enum | entry_mode | Yes | 'scan' | `scan` (quét mã) · `manual` (nhập tay khi không quét được). Chỉ ghi nhận cách nhập dòng kiểm hàng, không tự chặn (API M2-009, M2-011) |
| 29 |  | Lý do nhập tay | `manual_entry_reason` | text | N/A | No |  | NOT NULL khi `entry_mode = 'manual'` (CHECK `ck_receipt_line_manual_reason`); NULL khi `scan` |
| 30 |  | Ngày tạo | `created_at` | timestamptz | N/A | Yes | now() |  |
| 31 |  | Ngày cập nhật | `updated_at` | timestamptz | N/A | Yes | now() | Trigger `trg_set_updated_at`; chỉ sửa được khi dòng chưa xác nhận |

| No. | Tên index | Cột | Primary key | Unique | Note |
|---|---|---|---|---|---|
| 1 | `pk_receipt_line` | receipt_line_id | Yes | Yes |  |
| 2 | `uq_receipt_line_receipt_no` | receipt_id, line_no | No | Yes |  |
| 3 | `idx_receipt_line_sku_status` | sku_id, status | No | No | Tra dòng nhập theo SKU và trạng thái |
| 4 | `idx_receipt_line_beef` | beef_individual_id | No | No | Truy xuất theo số cá thể bò (S1-31). Partial: WHERE beef_individual_id IS NOT NULL |
| 5 | `idx_receipt_line_pending` | receipt_id, status | No | No | Màn kiểm hàng (S2-02) liệt kê dòng chưa xác nhận. Partial: WHERE status IN ('pending','inspected') |
| 6 | `idx_receipt_line_variance_pending` | receipt_id | No | No | Chặn đóng phiếu (`M_0211`) và hàng đợi duyệt lệch số báo (API-M2-014). Partial: WHERE status = 'variance_pending' |

| No. | Tên khoá ngoại | Cột | Bảng đích | Cột đích | Note |
|---|---|---|---|---|---|
| 1 | `fk_receipt_line_receipt_id` | `receipt_id` | [`receipt`](#42-receipt) | `receipt_id` | ON DELETE RESTRICT |
| 2 | `fk_receipt_line_sku_id` | `sku_id` | `sku` (M1_DB) | `sku_id` | ON DELETE RESTRICT |
| 3 | `fk_receipt_line_reason_code` | `reason_code` | `reason_code` (COMMON_DB) | `reason_code` | ON DELETE RESTRICT |
| 4 | `fk_receipt_line_location_id` | `location_id` | `location` (M1_DB) | `location_id` | ON DELETE RESTRICT |
| 5 | `fk_receipt_line_policy_version_id` | `policy_version_id` | `policy_version` (COMMON_DB) | `policy_version_id` | ON DELETE RESTRICT |
| 6 | `fk_receipt_line_qty_mismatch_approved_by` | `qty_mismatch_approved_by` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT |
| 7 | `fk_receipt_line_confirmed_by` | `confirmed_by` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT |
| 8 | `fk_receipt_line_held_lot_id` | `held_lot_id` | [`lot`](#44-lot) | `lot_id` | ON DELETE RESTRICT. Lô tạm giữ sinh từ `qty_held`; vòng với `fk_lot_receipt_line_id` được tạo bằng DEFERRABLE INITIALLY DEFERRED (hai dòng cùng transaction xác nhận) |

### `lot` — nguồn `M2_DB.md`

| | | | |
|---|---|---|---|
| Tên bảng (logic) | Lô hàng | Tạo bởi / ngày | pham.trong.nghia / 2026-10-13 |
| Tên bảng (vật lý) | `lot` | Sửa bởi / ngày | — |
| Overview | Một dòng cho mỗi lô, sinh khi xác nhận dòng nhập (N4-01), khi tạo lô tạm giữ từ `qty_held` của dòng nhập (H9), hoặc khi nạp migration. Lô là bằng chứng truy xuất: thuộc tính gốc không bao giờ sửa; sai thì tạo phiên bản / sự kiện đảo (ADR-004). Số lượng tồn theo từng vị trí nằm ở `stock_balance` (M3_DB); lịch sử trạng thái ở `lot_status_history` (M3_DB). | Note | BẤT BIẾN từ khi tạo (ADR-004; BR-EXP-01): chỉ `qty_available`, `status`, `updated_at` đổi, và chỉ qua event (trigger `trg_lot_immutable_columns` chặn UPDATE các cột còn lại). `status` đổi DUY NHẤT bằng cách INSERT vào `lot_status_history` (M3_DB), trigger `trg_lot_status_apply` cập nhật cột này và kiểm đồ thị chuyển trạng thái.<br>CHECK: `qty_received > 0`; `qty_available >= 0 AND qty_available <= qty_received`; `expiry_date > production_date` (mẫu số cửa sổ giao dương, business-rules R-02.2); `beef_individual_id IS NULL OR beef_individual_id ~ '^[0-9]{10}$'`; `ck_lot_lane_fields`: (traceability_lane = 'rice' OR rice_origin IS NULL) AND (traceability_lane = 'beef' OR beef_individual_id IS NULL); `ck_lot_source`: source_type IN ('receipt','receipt_hold') ⇒ receipt_line_id NOT NULL.<br>Lô thiếu trường truy xuất vẫn được tạo nhưng ở `quarantined` (FR-REC-05; business-rules R-07.9), nên các cột truy xuất không NOT NULL.<br>`traceability_lane`, `expiry_type`, `temperature_band` chép từ SKU lúc tạo để master đổi sau này không hồi tố đánh giá lô (RFP p.47).<br>In / in lại nhãn (API-M2-013, N2-26, N2-27) không có bảng riêng: mỗi lần in ghi một `business_event` `label_printed` (`aggregate_id` = `lot_id`; payload `label_kind`, `pallet_count`, `is_reprint`, `reason_code`, `entry_mode`, `manual_entry_reason`; người in = `actor_id`) — cùng cách lưu lý do duyệt ở M3_DB (C25). `is_reprint` = đã có sự kiện `label_printed` trước đó của lô [GIẢ ĐỊNH — B10 Việc 2] |

| No. | Key | Tên logic | Tên vật lý | Data Type | Size | Not Null | Default | Note |
|---|---|---|---|---|---|---|---|---|
| 1 | PRI | Lot ID | `lot_id` | varchar | 30 | Yes |  | RFP p.50. Mã in trên nhãn lô (N2-26) — sinh bằng sequence, [GIẢ ĐỊNH] `LOT-yyyymmdd-nnnnn` |
| 2 | FK | SKU | `sku_id` | varchar | 20 | Yes |  | RFP p.50 (id tham chiếu) |
| 3 | FK | Nhà cung cấp | `supplier_id` | varchar | 20 | Yes |  | RFP p.50 (id tham chiếu) |
| 4 |  | Lô nhà cung cấp | `supplier_lot` | varchar | 50 | Yes |  | RFP p.50 |
| 5 |  | Thời điểm nhận | `received_at_jst` | timestamptz | N/A | Yes |  | RFP p.50. Lưu timestamptz, hiển thị JST. Là mốc FIFO khi cùng hạn (bước ⑥ phân bổ) |
| 6 |  | Ngày sản xuất | `production_date` | date | N/A | Yes |  | RFP p.50 |
| 7 |  | Hạn dùng | `expiry_date` | date | N/A | Yes |  | RFP p.50. Ngày biên: tới hạn cũng bị chặn với 消費期限 (≤, không phải <). Điều kiện chặn đầy đủ: `expiry_type = consumption AND expiry_date ≤ reference_date + sku.expiry_block_buffer_days` (M1_DB; Q&A #8; hàm `isExpiryBlocked`); buffer đọc từ SKU lúc kiểm và ghi vào `evidence` của sự kiện chặn, không chép vào lô |
| 8 |  | Số lượng nhận | `qty_received` | numeric | 12,3 | Yes |  | RFP p.50. = `receipt_line.qty_accepted` (lô nhận) hoặc `receipt_line.qty_held` (lô tạm giữ `receipt_hold`) |
| 9 |  | Số lượng khả dụng | `qty_available` | numeric | 12,3 | Yes |  | RFP p.50. Lượng còn có thể phân bổ = tồn thực trừ phần đã phân bổ còn hiệu lực. Giảm khi phân bổ, tăng khi huỷ / vô hiệu phân bổ (N4-47). Xuất kho không đổi cột này vì tồn thực và phần phân bổ cùng giảm |
| 10 | FK | Vị trí nhận ban đầu | `location` | varchar | 20 | Yes |  | RFP p.50 trường `location` (text tham chiếu zone / vị trí), giữ đúng tên RFP. Là vị trí put-away lúc tạo — bất biến; lô tạm giữ đặt ở vị trí cách ly cùng dải (`is_quarantine = true`) nếu có vị trí `open`, không thì giữ vị trí nhận [U4]. Lô nhận của dòng `variance_pending` [U6] luôn đặt ở `receipt_line.location_id` (không ở vị trí cách ly). Vị trí HIỆN TẠI của lô: `stock_balance` (M3_DB), vì một lô có thể nằm nhiều vị trí |
| 11 |  | Trạng thái lô | `status` | enum | lot_status | Yes | 'available' | RFP p.50. `available` · `held_pending_remeasure` · `quarantined` · `on_hold` · `released` · `scrapped`. Lô tạm giữ lúc nhận (H9) và lô của dòng `variance_pending` [U6] tạo thẳng `on_hold` (dòng đầu `lot_status_history`: NULL → `on_hold`). Ứng viên phân bổ: `available`, `released` (business-rules R-05.2). Đồ thị chuyển ở `lot_status_history` (M3_DB); CQ3 cho `held_pending_remeasure` |
| 12 |  | Xuất xứ gạo | `rice_origin` | text | N/A | No |  | RFP p.50: không bắt buộc, chỉ lane gạo |
| 13 |  | Số định danh cá thể bò | `beef_individual_id` | char | 10 | No |  | RFP p.50: không bắt buộc, chỉ lane bò. Định danh pháp định bên ngoài, không FK. CHECK `beef_individual_id ~ '^[0-9]{10}$'` |
| 14 |  | Lane truy xuất (chép từ SKU) | `traceability_lane` | enum | traceability_lane | Yes |  | Bất biến; làm điều kiện cho CHECK `ck_lot_lane_fields` |
| 15 |  | Loại hạn dùng (chép từ SKU) | `expiry_type` | enum | expiry_type | Yes |  | Bất biến. Chốt chặn 消費期限 (N4-46) dùng cột này, không đọc lại SKU |
| 16 |  | Dải nhiệt (chép từ SKU) | `temperature_band` | enum | temperature_band | Yes |  | Bất biến. Bước ② phân bổ so cột này với dải yêu cầu (RFP p.46 Hình 35: Lô N:1 Dải nhiệt độ) |
| 17 |  | Nguồn tạo lô | `source_type` | enum | lot_source_type | Yes | 'receipt' | `receipt` · `receipt_hold` (lô tạm giữ lúc nhận, H9) · `migration` (DR-MIG-01) [GIẢ ĐỊNH] |
| 18 | FK | Dòng nhập sinh ra lô | `receipt_line_id` | varchar | 30 | No |  | NULL với lô từ migration. Unique theo `source_type` (hai index một phần) ⇒ retry cùng yêu cầu không tạo lô thứ hai (FR-REC-04, business-rules R-07.8); một dòng nhập có tối đa một lô `receipt` và một lô `receipt_hold` |
| 19 |  | Ngày tạo | `created_at` | timestamptz | N/A | Yes | now() |  |
| 20 |  | Ngày cập nhật | `updated_at` | timestamptz | N/A | Yes | now() | Trigger `trg_set_updated_at` |

| No. | Tên index | Cột | Primary key | Unique | Note |
|---|---|---|---|---|---|
| 1 | `pk_lot` | lot_id | Yes | Yes |  |
| 2 | `uq_lot_receipt_line` | receipt_line_id | No | Yes | Idempotent tạo lô nhận. Partial: WHERE source_type = 'receipt' AND receipt_line_id IS NOT NULL |
| 3 | `idx_lot_candidate` | sku_id, expiry_date, received_at_jst | No | No | Bước ⑥ phân bổ FEFO (cùng hạn → FIFO theo giờ nhập). Partial: WHERE status IN ('available','released') AND qty_available > 0 |
| 4 | `idx_lot_supplier_lot` | supplier_id, supplier_lot | No | No | Truy xuất ngược theo lô nhà cung cấp (S1-31) |
| 5 | `idx_lot_beef` | beef_individual_id | No | No | Truy xuất theo số cá thể bò (N1-58). Partial: WHERE beef_individual_id IS NOT NULL |
| 6 | `idx_lot_rice_origin` | rice_origin | No | No | Truy xuất theo xuất xứ gạo (N1-58). Partial: WHERE rice_origin IS NOT NULL |
| 7 | `idx_lot_status_expiry` | status, expiry_date | No | No | Tra cứu tồn theo trạng thái và hạn (N1-18) |
| 8 | `idx_lot_supplier_lot_only` | supplier_lot | No | No | Tra theo lô nhà cung cấp khi không biết nhà cung cấp (N1-58, soát B9 D34) |
| 9 | `uq_lot_receipt_hold_line` | receipt_line_id | No | Yes | Idempotent tạo lô tạm giữ (H9). Partial: WHERE source_type = 'receipt_hold' |

| No. | Tên khoá ngoại | Cột | Bảng đích | Cột đích | Note |
|---|---|---|---|---|---|
| 1 | `fk_lot_sku_id` | `sku_id` | `sku` (M1_DB) | `sku_id` | ON DELETE RESTRICT |
| 2 | `fk_lot_supplier_id` | `supplier_id` | `supplier` (M1_DB) | `supplier_id` | ON DELETE RESTRICT |
| 3 | `fk_lot_location` | `location` | `location` (M1_DB) | `location_id` | ON DELETE RESTRICT. Cột đặt tên `location` theo RFP p.50 nhưng là khoá ngoại tới `location.location_id` |
| 4 | `fk_lot_receipt_line_id` | `receipt_line_id` | [`receipt_line`](#43-receipt_line) | `receipt_line_id` | ON DELETE RESTRICT |

### `business_event` — nguồn `COMMON_DB.md` *(bảng của nhóm khác, mục này chỉ tham chiếu)*

| | | | |
|---|---|---|---|
| Tên bảng (logic) | Sự kiện nghiệp vụ | Tạo bởi / ngày | pham.trong.nghia / 2026-10-13 |
| Tên bảng (vật lý) | `business_event` | Sửa bởi / ngày | — |
| Overview | Mọi thay đổi nghiệp vụ (tạo lô, phân bổ, chặn, công bố tuyến, đóng deviation, đảo lịch sử giao…) ghi đúng một dòng ở đây, cùng transaction với việc cập nhật bảng state (ADR-004 #2). Bảng state có thể dựng lại từ bảng này (job dựng lại state, ADR-004 #4). | Note | BẤT BIẾN (ADR-004 #3): append-only — REVOKE UPDATE, DELETE, TRUNCATE khỏi vai trò `app_rw` + trigger `trg_business_event_block_mutation` (BEFORE UPDATE OR DELETE → RAISE EXCEPTION). Ngoại lệ duy nhất: vai trò `purge_job` khi có `retention_purge_run` đã duyệt (M9_DB; cơ chế ở Technical Design N4-24) [GIẢ ĐỊNH].<br>CHECK `ck_business_event_reversal_reason`: `reversal_of IS NULL OR reason_code IS NOT NULL OR reason_text IS NOT NULL`.<br>CHECK `ck_business_event_maker_checker`: `approver_id IS NULL OR approver_id <> actor_id`. |

| No. | Key | Tên logic | Tên vật lý | Data Type | Size | Not Null | Default | Note |
|---|---|---|---|---|---|---|---|---|
| 1 | PRI | ID sự kiện | `business_event_id` | bigint | N/A | Yes | GENERATED ALWAYS AS IDENTITY | Tăng dần theo thứ tự ghi; job dựng lại state đọc theo cột này |
| 2 |  | Loại sự kiện | `event_type` | varchar | 60 | Yes |  | snake_case, vd `lot_created`, `allocation_confirmed`, `expiry_blocked`. Danh mục đầy đủ ở Technical Design từng function; KHÔNG CHECK enum ở DB để thêm loại mới không cần migration [GIẢ ĐỊNH] |
| 3 |  | Loại thực thể | `aggregate_type` | varchar | 40 | Yes |  | Tên bảng state bị tác động (`lot`, `sales_order`, `route`, `deviation_case`…) |
| 4 |  | ID thực thể | `aggregate_id` | varchar | 64 | Yes |  | Khoá thực thể dạng text (khoá nghiệp vụ hoặc uuid). Không FK vì trỏ nhiều bảng khác nhau |
| 5 |  | Thời điểm xảy ra | `occurred_at` | timestamptz | N/A | Yes | now() | ISO 8601, hiển thị JST (RFP p.45). Với POD đồng bộ trễ = thời điểm trên thiết bị (ADR-008) |
| 6 |  | Thời điểm ghi | `recorded_at` | timestamptz | N/A | Yes | now() | Server ghi. Chênh với `occurred_at` cho biết độ trễ đồng bộ offline |
| 7 | FK | Người thực hiện | `actor_id` | uuid | N/A | No |  | NULL = hệ thống / job |
| 8 | FK | Vai trò lúc thực hiện | `actor_role` | varchar | 30 | No |  | Vai trò tại thời điểm làm, không đổi theo việc gán quyền sau này |
| 9 | FK | Thiết bị | `device_id` | varchar | 20 | No |  | Thiết bị quét / nhập (M1_DB `device`), NULL nếu thao tác từ máy văn phòng |
| 10 |  | Mã tương quan | `correlation_id` | uuid | N/A | Yes |  | Xuyên domain (NFR-OPS-01 RFP p.59); màn N1-21 lần theo cột này. Không có FK: mã sinh ở ranh giới request, không phải khoá bảng |
| 11 | FK | Phiên bản luật đã dùng | `rule_version_id` | uuid | N/A | No |  | `policy_version` (nhiệt độ / giờ lái) đã dùng khi sự kiện này được đánh giá (ADR-005 #3). Luật cửa sổ giao: phiên bản hợp đồng đi trong `payload.agreement_version_id` (xem mục 5, Q2) |
| 12 |  | Mã quy tắc | `rule_code` | varchar | 30 | No |  | Mã quy tắc nghiệp vụ khi là sự kiện chặn / áp luật (vd `BR-EXP-02`); màn hình hiện mã này (business-rules R-00.6) |
| 13 | FK | Mã lý do | `reason_code` | varchar | 30 | No |  | Bắt buộc với sự kiện đảo (xem CHECK ở Note bảng) |
| 14 |  | Lý do (văn bản) | `reason_text` | text | N/A | No |  | Lý do tự do kèm theo |
| 15 | FK | Người duyệt | `approver_id` | uuid | N/A | No |  | Sự kiện đảo lịch sử giao bắt buộc có người duyệt (DR-HIST-01, RFP p.47); kiểm ở service khi `aggregate_type = 'shipment_line'` |
| 16 | FK, UNI | Đảo sự kiện | `reversal_of` | bigint | N/A | No |  | Sự kiện này đảo sự kiện nào. Sửa sai = sự kiện đảo, không có UPDATE (ADR-004) |
| 17 | FK | Thay thế phiên bản | `supersedes` | bigint | N/A | No |  | Sự kiện này tạo phiên bản mới thay cho sự kiện nào (vd sửa tuyến sau công bố) |
| 18 |  | Dữ liệu căn cứ | `payload` | jsonb | N/A | Yes | '{}'::jsonb | Giá trị đã dùng để quyết định (tồn, hạn, ngưỡng, danh sách lô…). KHÔNG chứa token, chữ ký, ảnh POD gốc (business-rules R-13.4) |

| No. | Tên index | Cột | Primary key | Unique | Note |
|---|---|---|---|---|---|
| 1 | `pk_business_event` | business_event_id | Yes | Yes |  |
| 2 | `idx_business_event_aggregate` | aggregate_type, aggregate_id, business_event_id | No | No | Chuỗi sự kiện của một thực thể (N1-20 lịch sử trạng thái lô); thứ tự ghi |
| 3 | `idx_business_event_correlation` | correlation_id, business_event_id | No | No | Lần theo giao dịch xuyên domain (N1-21) |
| 4 | `idx_business_event_type_occurred` | event_type, occurred_at | No | No | Lọc theo loại sự kiện + khoảng thời gian (báo cáo M8, kiểm toán) |
| 5 | `idx_business_event_actor_occurred` | actor_id, occurred_at | No | No | Kiểm toán theo người làm. Partial: WHERE actor_id IS NOT NULL |
| 6 | `brin_business_event_occurred` | occurred_at | No | No | BRIN — bảng chỉ-thêm nên `occurred_at` gần như tăng dần; rẻ hơn B-tree cho truy vấn khoảng dài |
| 7 | `uq_business_event_reversal_of` | reversal_of | No | Yes | Một sự kiện chỉ bị đảo một lần [GIẢ ĐỊNH]. Partial: WHERE reversal_of IS NOT NULL |

| No. | Tên khoá ngoại | Cột | Bảng đích | Cột đích | Note |
|---|---|---|---|---|---|
| 1 | `fk_business_event_actor_id` | `actor_id` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT — người dùng chỉ bị vô hiệu hoá, không xoá |
| 2 | `fk_business_event_approver_id` | `approver_id` | `user_account` (M9_DB) | `user_account_id` | ON DELETE RESTRICT |
| 3 | `fk_business_event_actor_role` | `actor_role` | `role` (M9_DB) | `role_code` | ON DELETE RESTRICT |
| 4 | `fk_business_event_device_id` | `device_id` | `device` (M1_DB) | `device_id` | ON DELETE RESTRICT |
| 5 | `fk_business_event_rule_version_id` | `rule_version_id` | [`policy_version`](#44-policy_version) | `policy_version_id` | ON DELETE RESTRICT |
| 6 | `fk_business_event_reason_code` | `reason_code` | [`reason_code`](#47-reason_code) | `reason_code` | ON DELETE RESTRICT |
| 7 | `fk_business_event_reversal_of` | `reversal_of` | [`business_event`](#41-business_event) | `business_event_id` | Tự tham chiếu; ON DELETE RESTRICT |
| 8 | `fk_business_event_supersedes` | `supersedes` | [`business_event`](#41-business_event) | `business_event_id` | Tự tham chiếu; ON DELETE RESTRICT |

## Định nghĩa xong (DoD)

- [ ] `M2_DB.md` mục 2 (Danh sách bảng) có thêm một dòng cho `stock_unit`; câu mô tả "4 bảng" ở đầu file nói 5 bảng; dòng đó mang Status `Decision (provisional)`, không phải `Approved`, tới khi khách duyệt (luật C8).
- [ ] `M2_DB.md` mục 1 (Lịch sử sửa đổi) có dòng mới: tăng version, nêu lý do là chỗ hở của `N4-01` / `API-M2-012`.
- [ ] ER Diagram (mục 3) có `stock_unit` và quan hệ tới `lot`; tên bảng, tên cột ở ER khớp từng ký tự với mục 4 (bộ kiểm "ERD ↔ bảng cột" xanh — C3).
- [ ] Mục 4 có định nghĩa `stock_unit` đủ bốn khối: Overview, Định nghĩa cột, Index, Khoá ngoại; khoá ngoại tới `lot.lot_id` ghi `ON DELETE RESTRICT`.
- [ ] Note của bảng nói rõ bất biến: không UPDATE, không DELETE, có trigger chặn theo cùng khuôn với `lot`.
- [ ] Note của bảng nói stock unit sinh trong **cùng giao dịch** `API-M2-012` với lô, và retry cùng yêu cầu không sinh thêm stock unit.
- [ ] Mọi cột ngoài khoá chính và khoá ngoại tới `lot` đều gắn `[GIẢ ĐỊNH]` trong cột Note và có một dòng ở mục "Câu hỏi mở / TBD" cho tới khi Tech Lead chốt.
- [ ] Tìm `stock_unit` trong thư mục Database Design ra ≥ 1 file (trước khi làm: 0 kết quả, bằng chứng `EV-01`).
- [ ] Diff của `M2_DB.md` chỉ chạm các mục trên: định nghĩa cột và ràng buộc của `lot`, `receipt_line`, `receipt`, `inbound_appointment` không đổi.
- [ ] Tech Lead ký cấu trúc cột trước khi `BL-M2-180` (#42) dựng migration cho bảng này (ghi người ký và ngày vào dòng lịch sử sửa đổi).

## Tham chiếu

- Tiền đề: [`PREMISE.md`](PREMISE.md) §1.1, nhãn **[đề xuất]** — chỗ hở có nguồn, nhưng **cấu trúc bảng là lựa chọn của người làm**. Bằng chứng `EV-01`, `EV-16`.
- RFP: `p.16` `FR-REC-04`, `DR-LOT-01` (lô và stock unit bất biến, retry không trùng khoá). Từ điển RFP có định nghĩa 「unit」 (PREMISE §1.1 dẫn); người làm đọc mục đó khi chốt cấu trúc.
- Function: `N4-01` (`Requirements/03_requirements/functional/04-function-list.csv`). Spec: `LAB-4/Output/specs/specs/m2-inbound/screen-specification/S2-03_SPEC.md` mục 3; API: `API-M2-012` — `LAB-4/Output/specs/specs/_common/api-list.md:136`.
- Database Design: `LAB-4/Output/specs/specs/system-design/database-design/m2-inbound/M2_DB.md`.
- Phụ thuộc: `BL-M2-053` (#39) (nơi ghi stock unit), `BL-M2-180` (#42) (dựng lược đồ M2).
- **Chỗ hở (C2), không điền bừa:** spec không nói (a) mỗi lô có một hay nhiều stock unit, (b) stock unit mang mã định danh nào, (c) có số lượng / đơn vị riêng không, (d) có cột thời điểm sinh hay chỉ dựa vào sự kiện. Cả bốn là quyết định của người làm, trình Tech Lead; issue này không áp đặt.
- Không có `CQ` nào đang mở chặn issue này. Bản LAB-4 đã nộp **không sửa** (PREMISE); thay đổi nằm trong repo sản phẩm.

---

## 3. Vì sao chọn mục này làm mẫu

`BL-M2-003` (#36) là **một dòng của PREMISE §1.1** (thêm bảng `stock_unit`). Nó cho thấy ba thứ cùng lúc:

- mã `BL-M2-003` (#36) được **ghim cứng** trong script sinh backlog vì PREMISE đã trích nó — đổi số là gãy tham chiếu;
- lô viết mô tả **không bịa cấu trúc bảng**: mọi cột ngoài khoá ngoại tới `lot` đều gắn `[GIẢ ĐỊNH]` và có dòng TBD;
- Status của bảng đặt `Decision (provisional)` theo luật C8 — chưa có khách thật duyệt.
