# task-breakdown — cách chia backlog LAB-5

| | |
|---|---|
| Trạng thái | **Đã duyệt ở G2 (2026-10-26)**; §4.11 và các số ở §6 cập nhật sau khi backlog lên bảng công việc |
| Ngày | 2026-10-26 |
| Tiền đề | [`PREMISE.md`](PREMISE.md) bản 7, duyệt ở G1 (2026-10-26). Errata `E11` đã áp vào SSOT ở B2 |
| Vật liệu | Bộ thiết kế LAB-4 đã nộp: **67 màn Core** (16 mức A, 51 mức B) · **83 API** có file spec riêng · **11 Technical Design** · **11 nhóm Database Design** |
| Dữ liệu backlog | `work/backlog.csv` (đã đẩy lên GitHub ở B4) · mô tả từng mục ở `work/issues/{backlog_id}.md` |

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

### 1.10 Định nghĩa xong (DoD)

Mỗi mục **không phải epic** có một file mô tả theo khuôn `work/issue-template.md`, gồm bốn mục bắt buộc: `## Mục tiêu` · `## Nguồn trường` · `## Định nghĩa xong (DoD)` · `## Tham chiếu`.

| # | Luật DoD | Kiểm bằng |
|---|---|---|
| 2.1 | Mỗi gạch đầu dòng nói một thứ **quan sát được**: màn hiện gì, API trả gì, bảng có dòng nào, test nào xanh. Cấm "hoạt động đúng", "đã xong" | Người soát; đọc mù ở R2 |
| 2.2 | **C2 — mọi trường nghiệp vụ hiển thị phải ghi nguồn**: `cột DB` · `công thức` · `sự kiện` · `trạng thái cục bộ` · `API ngoài`. Trường không có nguồn là **lỗi chặn**, không phải câu hỏi mở | `check_issue_source.py` |
| 2.3 | Tham chiếu loại `cột DB` phải là `bảng.cột` **có thật** trong `database-design/` | `check_issue_source.py` tra lại từng cặp |
| 2.4 | **C7 — issue gắn nhãn `state-change`**: DoD phải đòi **sự kiện riêng** cho mỗi thực thể bị loại hoặc đổi trạng thái, kể cả khi giao dịch tổng thành công | `check_event_dod.py` |
| 2.5 | Issue chạm câu hỏi khách còn mở phải nhắc đúng mã `CQ` và nói nó chặn phần nào | `check_cq_open.py` |
| 2.6 | **Không chép nguyên văn RFP** — ≥20 từ tiếng Việt hoặc ≥40 ký tự tiếng Nhật liền mạch là lỗi chặn | `check_leak.py` |

### 1.11 Tự chứa — "tối thiểu đủ để bắt đầu" (hướng C)

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

### 1.12 Độ hạt và epic

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
**Tổng: 212 mục — 13 epic · 151 issue · 48 sub-issue.** Khối số trong mỗi epic: `000` epic · `001–019` thay đổi từ PREMISE và issue làm rõ · `010–019` việc nền (epic `SETUP`) · `020–039` function nền · `050–149` màn · `150–179` nhóm API · `180–199` Technical Design và Database Design · `200–299` sub-issue. Mục nhóm API và mục lược đồ **chia phần theo ngân sách độ dài** vì body một issue GitHub tối đa 65 536 ký tự.

### 1.13 Ba mục vượt ngưỡng hợp lý — khai ra, chưa tách [QUYẾT ĐỊNH 2026-10-26]

Nguyên tắc §1.9 định nghĩa **loại việc** của từng khối số, nhưng **không đặt trần độ lớn**.
Hệ quả: ba mục nặng 24 điểm, tức khoảng 153 giờ mỗi mục.

| Mục | Chứa | Vì sao to |
|---|---|---|
| `BL-M5-150` | 11 API (`API-M5-002` … `-011`, `-024`) | Lần chia trước cắt theo **độ dài thân mục** cho vừa trần 65 536 ký tự của GitHub, không cắt theo nhóm nghiệp vụ |
| `BL-M5-182` | 7 bảng nhóm tuyến và giờ tài xế | Cùng lý do |
| `BL-M5-183` | 6 bảng nhóm giao hàng và ngoại lệ | Cùng lý do |

**Đo so với sức chứa sprint:** một sprint hai tuần của đội này chứa 565 giờ (§kịch bản sprint).
Một mục 153 giờ chiếm hơn một phần tư sprint — khó gọi là "đủ nhỏ để theo dõi".

**Chưa tách, và đây là lý do.** Tách đúng cách là cắt theo cụm khoá ngoại và nhóm nghiệp vụ,
không phải cắt theo độ dài. Việc đó kéo theo chấm lại điểm, ghi lại giờ cho cả 212 mục và sinh
lại mọi bảng trong bộ nộp — **tổng SWP sẽ tăng**, vì ma trận là phép nhân và phần việc chung
của mỗi mục lặp lại. Người duyệt chọn giữ nguyên và khai ra, thay vì chạy thêm một vòng sát
lúc nộp.

**Việc phải làm ở vòng chia sau:** thêm vào §1 một luật trần độ lớn (ví dụ *quá 16 điểm thì
tách, trừ khi nói rõ vì sao không tách được*) cộng một luật kiểm máy cho nó. Không có luật đó
thì vòng sau lại sinh ra mục 24 điểm mà bộ kiểm vẫn xanh — đúng họ lỗi "bộ kiểm chỉ kiểm thứ
mình nghĩ ra để kiểm".

## 2. Spec → issue

Mỗi dòng là một đơn vị spec của LAB-4 và những mục backlog sinh ra từ nó. Cột cuối ghi **phần
spec chưa thành mục nào và vì sao** — chỗ nào ghi "chưa có mục" là chỗ backlog còn thiếu việc,
không phải chỗ spec thừa.

| Spec | Epic | Issue / sub-issue | Phần spec chưa thành issue và vì sao |
|---|---|---|---|
| `S1-01` mức B · `S1-01_SPEC.md` | `M9` | [#137](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/137) `BL-M9-050` | — |
| `S1-02` mức B · `S1-02_SPEC.md` | `M9` | [#138](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/138) `BL-M9-051` | — |
| `S1-03` mức B · `S1-03_SPEC.md` | `M1` | [#20](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/20) `BL-M1-050` | — |
| `S1-04` mức B · `S1-04_SPEC.md` | `M1` | [#21](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/21) `BL-M1-051` | — |
| `S1-05` mức B · `S1-05_SPEC.md` | `M1` | [#22](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/22) `BL-M1-052` | — |
| `S1-06` mức A · `S1-06_SPEC.md` | `M1` | `BL-M1-053` · [#155](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/155) `BL-M1-209` · [#156](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/156) `BL-M1-210` · [#157](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/157) `BL-M1-211` | — |
| `S1-07` mức A · `S1-07_SPEC.md` | `M1` | `BL-M1-054` · [#158](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/158) `BL-M1-212` · [#159](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/159) `BL-M1-213` · [#160](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/160) `BL-M1-214` | — |
| `S1-08` mức B · `S1-08_SPEC.md` | `M2` | [#37](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/37) `BL-M2-050` | — |
| `S1-09` mức B · `S1-09_SPEC.md` | `M2` | [#38](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/38) `BL-M2-051` | — |
| `S1-10` mức B · `S1-10_SPEC.md` | `M3` | [#46](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/46) `BL-M3-050` | — |
| `S1-11` mức B · `S1-11_SPEC.md` | `M3` | [#47](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/47) `BL-M3-051` | — |
| `S1-12` mức B · `S1-12_SPEC.md` | `M3` | [#48](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/48) `BL-M3-052` | — |
| `S1-13` mức B · `S1-13_SPEC.md` | `M3` | [#49](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/49) `BL-M3-053` | — |
| `S1-14` mức B · `S1-14_SPEC.md` | `M3` | [#50](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/50) `BL-M3-054` | — |
| `S1-15` mức A · `S1-15_SPEC.md` | `M3` | `BL-M3-055` · [#163](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/163) `BL-M3-215` · [#164](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/164) `BL-M3-216` · [#165](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/165) `BL-M3-217` | — |
| `S1-16` mức B · `S1-16_SPEC.md` | `M3` | [#52](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/52) `BL-M3-056` | — |
| `S1-17` mức B · `S1-17_SPEC.md` | `M4` | [#61](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/61) `BL-M4-050` | — |
| `S1-18` mức A · `S1-18_SPEC.md` | `M4` | `BL-M4-051` · [#166](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/166) `BL-M4-203` · [#167](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/167) `BL-M4-204` · [#168](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/168) `BL-M4-205` | — |
| `S1-19` mức A · `S1-19_SPEC.md` | `M4` | `BL-M4-052` · [#169](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/169) `BL-M4-206` · [#170](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/170) `BL-M4-207` · [#171](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/171) `BL-M4-208` | — |
| `S1-20` mức B · `S1-20_SPEC.md` | `M5` | [#79](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/79) `BL-M5-050` | — |
| `S1-21` mức A · `S1-21_SPEC.md` | `M5` | [#16](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/16) `BL-DRV-001` · `BL-M5-051` · [#175](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/175) `BL-M5-203` · [#176](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/176) `BL-M5-204` · [#177](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/177) `BL-M5-205` | — |
| `S1-22` mức A · `S1-22_SPEC.md` | `M5` | `BL-M5-052` · [#178](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/178) `BL-M5-206` · [#179](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/179) `BL-M5-207` · [#180](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/180) `BL-M5-208` | — |
| `S1-23` mức A · `S1-23_SPEC.md` | `M5` | `BL-M5-053` · [#181](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/181) `BL-M5-209` · [#182](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/182) `BL-M5-210` · [#183](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/183) `BL-M5-211` | — |
| `S1-24` mức B · `S1-24_SPEC.md` | `M5` | [#83](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/83) `BL-M5-054` | — |
| `S1-25` mức B · `S1-25_SPEC.md` | `M5` | [#84](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/84) `BL-M5-055` | — |
| `S1-26` mức B · `S1-26_SPEC.md` | `M5` | [#85](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/85) `BL-M5-056` | — |
| `S1-27` mức B · `S1-27_SPEC.md` | `M6` | [#100](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/100) `BL-M6-050` | — |
| `S1-28` mức A · `S1-28_SPEC.md` | `M6` | `BL-M6-051` · [#190](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/190) `BL-M6-203` · [#191](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/191) `BL-M6-204` · [#192](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/192) `BL-M6-205` | — |
| `S1-29` mức A · `S1-29_SPEC.md` | `M6` | `BL-M6-052` · [#193](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/193) `BL-M6-206` · [#194](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/194) `BL-M6-207` · [#195](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/195) `BL-M6-208` | — |
| `S1-30` mức B · `S1-30_SPEC.md` | `M6` | [#103](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/103) `BL-M6-053` | — |
| `S1-31` mức A · `S1-31_SPEC.md` | `M7` | `BL-M7-050` · [#196](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/196) `BL-M7-200` · [#197](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/197) `BL-M7-201` · [#198](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/198) `BL-M7-202` | — |
| `S1-32` mức B · `S1-32_SPEC.md` | `M7` | [#113](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/113) `BL-M7-051` | — |
| `S1-33` mức A · `S1-33_SPEC.md` | `M7` | `BL-M7-052` · [#199](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/199) `BL-M7-206` · [#200](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/200) `BL-M7-207` · [#201](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/201) `BL-M7-208` | — |
| `S1-34` mức B · `S1-34_SPEC.md` | `M7` | [#115](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/115) `BL-M7-053` | — |
| `S1-35` mức B · `S1-35_SPEC.md` | `M8` | [#122](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/122) `BL-M8-050` | Phạm vi xuất dữ liệu (định dạng, giới hạn dòng) **chưa chốt**, nên chưa tách mục xuất riêng |
| `S1-36` mức B · `S1-36_SPEC.md` | `M8` | [#123](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/123) `BL-M8-051` | — |
| `S1-37` mức B · `S1-37_SPEC.md` | `M9` | [#139](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/139) `BL-M9-052` | — |
| `S1-38` mức B · `S1-38_SPEC.md` | `M8` | [#124](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/124) `BL-M8-052` | — |
| `S1-39` mức B · `S1-39_SPEC.md` | `M9` | [#140](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/140) `BL-M9-053` | — |
| `S1-40` mức B · `S1-40_SPEC.md` | `M10` | [#33](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/33) `BL-M10-050` | Màn bảng điều khiển migration mới có mục dựng màn; **chưa có mục cho script chuyển đổi, đối soát và chạy thử** — §4.11 |
| `S1-41` mức B · `S1-41_SPEC.md` | `M10` | [#34](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/34) `BL-M10-051` | Như trên — phần thực thi migration và ghi lineage chưa thành mục |
| `S1-48` mức B · `S1-48_SPEC.md` | `M1` | [#25](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/25) `BL-M1-055` | — |
| `S1-49` mức B · `S1-49_SPEC.md` | `M1` | [#26](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/26) `BL-M1-056` | — |
| `S1-50` mức B · `S1-50_SPEC.md` | `M1` | [#27](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/27) `BL-M1-057` | — |
| `S1-51` mức A · `S1-51_SPEC.md` | `M5` | `BL-M5-057` · [#184](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/184) `BL-M5-221` · [#185](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/185) `BL-M5-222` · [#186](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/186) `BL-M5-223` | — |
| `S1-52` mức B · `S1-52_SPEC.md` | `M6` | [#104](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/104) `BL-M6-054` | — |
| `S2-01` mức B · `S2-01_SPEC.md` | `M9` | [#141](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/141) `BL-M9-054` | — |
| `S2-02` mức A · `S2-02_SPEC.md` | `M2` | `BL-M2-052` · [#3](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/3) `BL-M2-206` · [#161](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/161) `BL-M2-207` · [#162](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/162) `BL-M2-208` | — |
| `S2-03` mức B · `S2-03_SPEC.md` | `M2` | [#39](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/39) `BL-M2-053` · [#44](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/44) `BL-M3-002` | — |
| `S2-04` mức B · `S2-04_SPEC.md` | `M3` | [#43](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/43) `BL-M3-001` · [#44](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/44) `BL-M3-002` · [#45](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/45) `BL-M3-003` · [#53](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/53) `BL-M3-057` | — |
| `S2-05` mức B · `S2-05_SPEC.md` | `M3` | [#54](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/54) `BL-M3-058` | — |
| `S2-06` mức B · `S2-06_SPEC.md` | `M3` | [#55](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/55) `BL-M3-059` | — |
| `S2-07` mức B · `S2-07_SPEC.md` | `M4` | [#64](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/64) `BL-M4-053` | — |
| `S2-08` mức B · `S2-08_SPEC.md` | `M4` | [#65](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/65) `BL-M4-054` | — |
| `S2-09` mức A · `S2-09_SPEC.md` | `M4` | `BL-M4-055` · [#172](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/172) `BL-M4-215` · [#173](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/173) `BL-M4-216` · [#174](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/174) `BL-M4-217` | — |
| `S2-10` mức B · `S2-10_SPEC.md` | `M4` | [#67](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/67) `BL-M4-056` | — |
| `S2-11` mức B · `S2-11_SPEC.md` | `M6` | [#105](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/105) `BL-M6-055` | — |
| `S2-14` mức B · `S2-14_SPEC.md` | `M2` | [#40](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/40) `BL-M2-054` | — |
| `S2-15` mức B · `S2-15_SPEC.md` | `M3` | [#56](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/56) `BL-M3-060` | — |
| `S2-16` mức B · `S2-16_SPEC.md` | `M9` | [#142](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/142) `BL-M9-055` | — |
| `S2-17` mức B · `S2-17_SPEC.md` | `M4` | [#68](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/68) `BL-M4-057` | — |
| `S3-01` mức B · `S3-01_SPEC.md` | `M9` | [#143](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/143) `BL-M9-056` | — |
| `S3-02` mức B · `S3-02_SPEC.md` | `M5` | [#87](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/87) `BL-M5-058` | — |
| `S3-03` mức A · `S3-03_SPEC.md` | `M5` | `BL-M5-059` · [#187](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/187) `BL-M5-227` · [#188](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/188) `BL-M5-228` · [#189](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/189) `BL-M5-229` | — |
| `S3-05` mức B · `S3-05_SPEC.md` | `M5` | [#89](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/89) `BL-M5-060` | — |
| `S3-06` mức B · `S3-06_SPEC.md` | `M5` | [#76](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/76) `BL-M5-001` · [#90](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/90) `BL-M5-061` | — |
| `S3-07` mức B · `S3-07_SPEC.md` | `M5` | [#91](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/91) `BL-M5-062` | — |
| chức năng nền `N4-*` — N4-18 | `DRV` | [#17](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/17) `BL-DRV-020` | — |
| nhóm API có spec riêng của module — M1 | `M1` | [#28](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/28) `BL-M1-150` | — |
| nhóm API có spec riêng của module — M1 | `M1` | [#29](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/29) `BL-M1-151` | — |
| Technical Design dùng chung — N4-13 | `M1` | [#30](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/30) `BL-M1-180` | — |
| lược đồ cơ sở dữ liệu của module — M1_DB | `M1` | [#31](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/31) `BL-M1-181` | — |
| lược đồ cơ sở dữ liệu của module — M1_DB | `M1` | [#32](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/32) `BL-M1-182` | — |
| chức năng nền `N4-*` — Script chuyển đổi dữ liệu từ hệ cũ | `M10` | [#202](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/202) `BL-M10-020` | — |
| chức năng nền `N4-*` — Đối soát sau chuyển đổi và lần chạy thử trên dữ liệu thật | `M10` | [#203](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/203) `BL-M10-021` | — |
| lược đồ cơ sở dữ liệu của module — M10_DB | `M10` | [#35](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/35) `BL-M10-180` | — |
| nhóm API có spec riêng của module — M2 | `M2` | [#41](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/41) `BL-M2-150` | — |
| lược đồ cơ sở dữ liệu của module — M2_DB | `M2` | [#42](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/42) `BL-M2-180` | — |
| nhóm API có spec riêng của module — M3 | `M3` | [#57](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/57) `BL-M3-150` | — |
| Technical Design dùng chung — N4-47 | `M3` | [#58](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/58) `BL-M3-180` | — |
| lược đồ cơ sở dữ liệu của module — M3_DB | `M3` | [#59](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/59) `BL-M3-181` | — |
| lược đồ cơ sở dữ liệu của module — M3_DB | `M3` | [#60](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/60) `BL-M3-182` | — |
| nhóm API có spec riêng của module — M4 | `M4` | [#69](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/69) `BL-M4-150` | — |
| nhóm API có spec riêng của module — M4 | `M4` | [#70](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/70) `BL-M4-151` | — |
| Technical Design dùng chung — N4-04 | `M4` | [#71](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/71) `BL-M4-180` | — |
| Technical Design dùng chung — N4-46 | `M4` | [#72](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/72) `BL-M4-181` | — |
| Technical Design dùng chung — N4-48 | `M4` | [#73](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/73) `BL-M4-182` | — |
| lược đồ cơ sở dữ liệu của module — M4_DB | `M4` | [#74](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/74) `BL-M4-183` | — |
| lược đồ cơ sở dữ liệu của module — M4_DB | `M4` | [#75](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/75) `BL-M4-184` | — |
| nhóm API có spec riêng của module — M5 | `M5` | [#92](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/92) `BL-M5-150` | — |
| nhóm API có spec riêng của module — M5 | `M5` | [#93](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/93) `BL-M5-151` | — |
| Technical Design dùng chung — N1-39 | `M5` | [#94](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/94) `BL-M5-180` | — |
| Technical Design dùng chung — N1-41 | `M5` | [#95](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/95) `BL-M5-181` | — |
| lược đồ cơ sở dữ liệu của module — M5_DB | `M5` | [#96](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/96) `BL-M5-182` | — |
| lược đồ cơ sở dữ liệu của module — M5_DB | `M5` | [#97](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/97) `BL-M5-183` | — |
| nhóm API có spec riêng của module — M6 | `M6` | [#106](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/106) `BL-M6-150` | — |
| Technical Design dùng chung — N1-54 | `M6` | [#107](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/107) `BL-M6-180` | — |
| Technical Design dùng chung — N4-09 | `M6` | [#108](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/108) `BL-M6-181` | — |
| lược đồ cơ sở dữ liệu của module — M6_DB | `M6` | [#109](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/109) `BL-M6-182` | — |
| lược đồ cơ sở dữ liệu của module — M6_DB | `M6` | [#110](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/110) `BL-M6-183` | — |
| lược đồ cơ sở dữ liệu của module — M6_DB | `M6` | [#111](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/111) `BL-M6-184` | — |
| nhóm API có spec riêng của module — M7 | `M7` | [#116](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/116) `BL-M7-150` | — |
| Technical Design dùng chung — N1-58 | `M7` | [#117](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/117) `BL-M7-180` | — |
| Technical Design dùng chung — N1-62 | `M7` | [#118](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/118) `BL-M7-181` | — |
| lược đồ cơ sở dữ liệu của module — M7_DB | `M7` | [#119](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/119) `BL-M7-182` | — |
| lược đồ cơ sở dữ liệu của module — M8_DB | `M8` | [#125](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/125) `BL-M8-180` | — |
| chức năng nền `N4-*` — N4-06 | `M9` | [#126](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/126) `BL-M9-020` | — |
| chức năng nền `N4-*` — N4-08 | `M9` | [#127](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/127) `BL-M9-021` | — |
| chức năng nền `N4-*` — N4-12 | `M9` | [#128](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/128) `BL-M9-022` | — |
| chức năng nền `N4-*` — N4-16 | `M9` | [#129](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/129) `BL-M9-023` | — |
| chức năng nền `N4-*` — N4-17 | `M9` | [#130](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/130) `BL-M9-024` | — |
| chức năng nền `N4-*` — N4-20 | `M9` | [#131](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/131) `BL-M9-025` | — |
| chức năng nền `N4-*` — N4-21 | `M9` | [#132](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/132) `BL-M9-026` | — |
| chức năng nền `N4-*` — N4-22 | `M9` | [#133](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/133) `BL-M9-027` | — |
| chức năng nền `N4-*` — N4-24 | `M9` | [#134](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/134) `BL-M9-028` | — |
| chức năng nền `N4-*` — N4-25 | `M9` | [#135](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/135) `BL-M9-029` | — |
| chức năng nền `N4-*` — N4-26 | `M9` | [#136](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/136) `BL-M9-030` | — |
| nhóm API có spec riêng của module — M9 | `M9` | [#144](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/144) `BL-M9-150` | — |
| lược đồ cơ sở dữ liệu của module — M9_DB | `M9` | [#145](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/145) `BL-M9-180` | — |
| `ARCHITECTURE_DESIGN.md` — sơ đồ triển khai, hạ tầng AWS | — | **không thành mục** | Backlog này dừng ở mức ứng dụng. Việc hạ tầng nằm ở sheet hạ tầng của ước lượng LAB-1, ngoài phạm vi LAB-5 |
| `ui-glossary-ja.md` — nhãn tiếng Nhật trên màn | — | **không thành mục** | Nhãn JA đi kèm từng màn, không tách việc riêng. Chỗ nhãn còn `[GIẢ ĐỊNH]` thì ghi trong mục của màn đó |
| Wireframe HTML của LAB-4 | — | **không thành mục** | Là đầu vào để dựng màn, không phải việc phải làm |

## 3. Hạng mục chịu ràng buộc đặc thù của đề

Đề nêu đích danh **giới hạn giờ lái xe tải từ 2024** cho Product B. Backlog tách nó thành một
epic riêng, và tách thêm tám ràng buộc nghiệp vụ khác thành nhãn. Nguyên tắc chọn giữa hai cách:
**ràng buộc có vòng đời riêng và bảng riêng thì thành epic; ràng buộc bắc qua nhiều module mà
không có dữ liệu riêng thì thành nhãn.**

| Ràng buộc | Issue | Vì sao tách riêng |
|---|---|---|
| **Giới hạn giờ lái xe tải từ 2024** — ràng buộc đề bài nêu đích danh cho Product B | Epic `BL-DRV-000` cùng 2 mục con: [#16](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/16) `BL-DRV-001` · [#17](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/17) `BL-DRV-020` | Tách thành **một epic riêng** chứ không phải nhãn: luật này có vòng đời riêng (bản quy định đổi theo năm), có bảng riêng (`driver_hours_ledger`), và khi quy định đổi thì chỉ epic này phải sửa. Gộp vào M5 thì mỗi lần đổi luật phải lần trong 32 mục của M5 để tìm chỗ chạm. |
| `rb-dai-nhiet` — dải nhiệt của lô phải khớp vị trí cất và phương tiện | **42 mục** mang nhãn `rb-dai-nhiet` — ví dụ `BL-M1-054` · [#28](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/28) `BL-M1-150` · [#29](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/29) `BL-M1-151` … | Chạm nhiều module (M2 nhận, M3 cất, M5 chở, M6 đo) nên không gom được vào một epic. Dùng nhãn để lọc ra khi quy định nhiệt đổi. |
| `rb-deviation-cach-ly` — lệch nhiệt thì lô vào cách ly | **39 mục** mang nhãn `rb-deviation-cach-ly` — ví dụ `BL-M2-052` · [#39](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/39) `BL-M2-053` · [#162](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/162) `BL-M2-208` … | Cùng lý do: một luật, nhiều màn. Nhãn cho phép hỏi *mọi mục nào phải sinh sự kiện cách ly*. |
| `rb-han-su-dung` — 消費期限 chặn, 賞味期限 cảnh báo | **25 mục** mang nhãn `rb-han-su-dung` — ví dụ [#21](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/21) `BL-M1-051` · [#31](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/31) `BL-M1-181` · `BL-M2-052` … | Đây là một trong bốn lỗi 重大エラー của LAB-1. Tách nhãn để khi test hồi quy biết đúng tập mục phải chạy lại. |
| `rb-truy-xuat-gao-bo` — gạo phải có xuất xứ, bò phải có số cá thể | **24 mục** mang nhãn `rb-truy-xuat-gao-bo` — ví dụ `BL-M2-052` · [#41](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/41) `BL-M2-150` · [#42](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/42) `BL-M2-180` … | Luật theo **mặt hàng**, không theo màn. Nhãn là cách duy nhất gom đúng tập mục. |
| `rb-tap-quan-hop-dong` — tập quán hợp đồng của từng khách | **22 mục** mang nhãn `rb-tap-quan-hop-dong` — ví dụ `BL-M1-053` · [#28](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/28) `BL-M1-150` · [#30](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/30) `BL-M1-180` … | Giá trị khác nhau theo khách, nên mọi mục đọc luật này phải đọc từ dữ liệu chủ chứ không hằng số trong mã. |
| `rb-phan-bo-nguyen-tu` — phân bổ 6 bước phải nguyên tử | **16 mục** mang nhãn `rb-phan-bo-nguyen-tu` — ví dụ `BL-M4-051` · `BL-M4-052` · `BL-M4-055` … | Ràng buộc kỹ thuật bắc qua M4 và M3; tách nhãn để lúc đổi cách khoá biết hết chỗ chạm. |
| `rb-ngoai-tuyen` — hàng đợi chứng cứ POD tối đa 8 giờ | **13 mục** mang nhãn `rb-ngoai-tuyen` — ví dụ [#76](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/76) `BL-M5-001` · [#87](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/87) `BL-M5-058` · `BL-M5-059` … | Chỉ APP-03 được phép ngoại tuyến; RFP **cấm** giao dịch kho ngoại tuyến. Nhãn giữ ranh giới đó nhìn thấy được. |
| `rb-cam-dao-ngay` — cấm đảo ngày giao | **11 mục** mang nhãn `rb-cam-dao-ngay` — ví dụ `BL-M4-051` · [#69](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/69) `BL-M4-150` · [#70](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/70) `BL-M4-151` … | Một trong bốn lỗi 重大エラー. So với **lịch sử giao hàng**, không so với hôm nay — nhãn để test hồi quy bắt đúng tập. |

## 4. Chỗ hở — xử lý thế nào

Nguồn: báo cáo của 11 lô viết mô tả (mỗi lô bắt buộc liệt kê giá trị đã đoán, chỗ spec nói hai kiểu, trường không tra được nguồn) cộng phần soát của session chính. Phân loại theo `LAB-5 Planning.md` §3 bước 8.

### 4.1 Hệ quả xuôi dòng của quyết định G1 — **đã trình và duyệt ở G2 (2026-10-26)**

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
| ~~`M9_DB.md:34` ↔ `:46`, `:486`~~ **Không phải mâu thuẫn — tôi đọc sót** | Tôi ghi "lịch sử sửa đổi nói 11 vai, câu hỏi mở Q1 nói 8 vai". Đọc lại cả dòng: `:46` ghi **"RFP p.07–08 (8 chức danh) + 3 vai trò hỗ trợ — 11 vai trò"** — hai con số là hai thứ khác nhau, không chọi nhau. Và `:486` cột Trạng thái ghi **`answered (B7): 11 vai trò`**, tức câu hỏi đã có câu trả lời từ LAB-4. | **Rút khỏi danh sách.** Không mở câu hỏi khách nào cho cặp này — mở là bịa ra một câu hỏi đã được trả lời |
| **14 API của M4** (`-001`, `-003`, `-005`, `-007`, `-009`, `-010`, `-011`, `-012`, `-013`, `-014`, `-016`, `-017`, `-020`, `-021`) — *bản trước tôi đếm 7, quét lại toàn thư mục `api-design/` ra 14* | dòng 400 ghi "hoặc thiếu `If-Match`" trong khi **không API nào nhận `If-Match`** — chép nhầm từ mẫu | `BL-M4-150` (#69) |
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

Ngoài ra, lỗ 1 và 3 nghĩa là backlog **thiếu hẳn một nhóm việc**: dựng dự án, môi trường, tài khoản thử, dữ liệu mẫu. **Đã thêm ở B4** — epic `SETUP` 7 mục, xem §6. Khi viết mục này thì chưa thêm vì nó **đổi số epic** — thứ G2 duyệt.

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

**Đã thêm mục ở vòng này.** `BL-M10-020` (script chuyển đổi, 15 SWP) và `BL-M10-021` (đối soát
và chạy thử, 10 SWP) nay nhận đúng phần việc còn thiếu; `BL-M10-021` phục vụ hai lần diễn tập
migration ở tuần 8 và tuần 15 cùng bằng chứng cho cổng `ACC-MIG-01`.

**Hai chỗ vẫn chưa có mục nhận, phát hiện khi viết hai mục trên — để mở:**

| Chỗ hở | Vì sao chưa thành mục |
|---|---|
| `API-M10-011` — job so sánh hệ cũ và hệ mới trong tuần vận hành song song (`N4-28`) | Phạm vi tuần vận hành song song chưa chốt; nhét vào `BL-M10-021` sẽ trộn hai việc khác mốc thời gian |
| Lần tải cuối lúc cutover | Thuộc kế hoạch chuyển đổi vận hành, không thuộc phần dựng — cần người nghiệp vụ xác nhận trước khi chia |

Và một chỗ hở của chính thiết kế, lộ ra khi viết `BL-M10-021`: `RFP p.64` đòi đối soát theo
**giá trị** tồn kho, nhưng Database Design **không có cột nào** là đơn giá hay giá trị. Mục
hiện chỉ đối soát được số lượng, và nói rõ điều đó.

**Không vá bằng cách nâng giờ của ba mục đang có.** Việc chưa được chia thì chưa có mục để
gắn giờ; nâng số chỉ làm con số đẹp lên trong khi backlog vẫn thiếu việc. Phải thêm mục ở
vòng break sau — ghi thành việc phải làm, không để tự nhớ.

*Phát hiện này đến từ phép so §5 của `estimate.md`, tức sau khi backlog đã lên board. Nó cho
thấy phép so với ước lượng cũ là một bộ kiểm thật cho bước break, không chỉ là một mục phải nộp.*

---

### 4.12 Ba chỗ hở lộ ra ở vòng đọc mù trước khi nộp

Ba lượt đọc mù cuối (mỗi lượt một người chưa biết gì về dự án, chỉ được mở đúng mục cần làm)
chỉ ra ba thứ **thiết kế LAB-4 không có**, chứ không phải mô tả issue quên chép:

| Chỗ hở | Chỗ phát hiện | Xử lý |
|---|---|---|
| **Biểu thức của CHECK constraint.** Bảng định nghĩa bảng ở Database Design có cột, index, khoá ngoại — **không có mục nào cho CHECK**. Tên ràng buộc xuất hiện rải trong cột Note, biểu thức thì không | `BL-M9-180`, `BL-M3-181` và mọi mục lược đồ | **Để mở.** Người dựng DDL phải tự viết biểu thức rồi trình người duyệt. Không bịa vào mô tả issue, vì bịa thì mục trông đủ mà DDL vẫn sai |
| **Giá trị đầy đủ của enum dùng chung** (`active_status`, `device_type`, `reason_category`) | `BL-M9-180`, `BL-M1-150` | **Để mở.** `BL-PLAT-002` nhận nhóm bảng `COMMON_DB`; danh sách giá trị chốt ở đó, các mục khác dẫn sang |
| **Ma trận bật/tắt nút theo vai × trạng thái** của spec màn (mục 6.1, 6.2) không chép được vào sub-issue FE vì nó là bảng của màn, không của lớp | `BL-M7-207` | **Để mở.** Sub-issue FE nay trỏ đích danh mục cha và mục giữ hợp đồng API; chép bảng sang cả ba lớp sẽ sinh ba bản dễ lệch nhau (C1) |

**Vì sao không vá bằng cách viết thêm vào mô tả.** Ba thứ trên là **quyết định thiết kế chưa có**,
không phải thông tin đã có mà quên chép. Viết đại một biểu thức CHECK vào issue thì người đọc mù
sẽ chấm `ĐỦ ĐỂ LÀM`, còn người dựng thật sẽ dựng sai — đổi một phép đo lấy một lỗi.

## 5. Bốn quyết định kỹ thuật — đã duyệt ở G2 (2026-10-26)

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

## 6. Bằng chứng đã trình ở G2

| Thứ | Ở đâu | Trạng thái |
|---|---|---|
| Backlog dạng dữ liệu | `work/backlog.csv` | 201 mục — 13 epic · 140 issue · 48 sub-issue |
| Mô tả từng mục | `work/issues/*.md` | 188 file (mọi mục không phải epic) |
| Mẫu một epic đầy đủ | `Output/epic-mau-M2.md` | Cây epic + một mô tả nguyên văn |
| Vòng đỏ trước khi sinh backlog | `work/checks/R8/` | 7/7 bộ kiểm đỏ vì thiếu dữ liệu, đúng kỳ vọng |
| Vòng xanh + đọc mù lần 1 | `work/checks/R9/` | Bộ kiểm 7/7 xanh · đọc mù **9 KHÔNG · 6 ĐOÁN · 0 ĐỦ** |
| Sau hướng C + đọc mù lần 2 | `work/checks/R10/` | Bộ kiểm 8/8 xanh · đọc mù **6 KHÔNG · 9 ĐOÁN · 0 ĐỦ** |
| Trước khi nộp + đọc mù lần 3 | `work/checks/R20/` | Chấm theo phần *làm được ngay* của khối mới **Bắt đầu từ đâu**, trên 8 mục khó nhất: **0 KHÔNG · 6 ĐOÁN · 2 ĐỦ** — lần đầu có mục đạt `ĐỦ ĐỂ LÀM`. Phần còn `PHẢI ĐOÁN` là thiết kế chưa có, xem §4.12 |
| Sau khi sửa công thức §1.3 | `work/checks/R11/` | Cả ba bộ kiểm xanh |
| `rebuild.sh` của B2 | `work/checks/R7/rebuild.log`, `rebuild-lan-2.log` | exit 0 **hai lần liên tiếp** |
| Sổ vòng test | `work/test-summary.md` | R1…R19 |
| Sổ lỗi AI | `work/ai-log.md` | 216 dòng |

