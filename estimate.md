# estimate.md — chấm SWP cho backlog Yuki Cold Logistics

> Trạng thái: §1–§2 đã chốt ở gate G4 (2026-10-26). §3–§5 làm ở bước quy giờ, **chưa có số** —
> không điền tạm để tránh đọc nhầm là đã chốt.

## 1. Báo cáo của công cụ chấm

| Thứ | Giá trị |
|---|---|
| Báo cáo HTML | [`swp-report/swp-lab5-2026-10-26.html`](swp-report/swp-lab5-2026-10-26.html) — tự chứa, mở offline, không gọi tài nguyên ngoài |
| Nguồn chấm | GitHub Project `https://github.com/users/nghiapt-1099/projects/1` — **chính bảng công việc**, không phải danh sách rời |
| Mô hình điểm | `standard-work-point` · ma trận `swp-matrix-v2` — SWP = ô tra của Bề rộng × Độ khó, **không cộng hai trục** |
| Phạm vi | 201 mục: **172 mục chấm trực tiếp** + **29 mục cha** lấy tổng của con |
| Tổng SWP sau hiệu chỉnh | **1245** (trước khi người kiểm: 1182) |
| Mục `needs-breakdown` | **0** — mọi mục cha đều có con |
| Ghi vào Project | trường `Point` của cả 201 mục, do Agent ghi sau khi người kiểm xong (prompt nguyên văn: `work/prompts/swp-ghi-point.md` — tài liệu nội bộ, không nộp) |

**Cách đọc hai trục.** *Bề rộng* (Scope ∈ 1·2·3·5·8·13) đo việc phải động vào bao nhiêu chỗ.
*Độ khó* (Complexity ∈ 0·1·2·3·5·8) đo mỗi chỗ khó tới đâu. Hai trục **nhân** chứ không cộng,
vì cái khó áp lên từng đơn vị bề rộng. Mục cha **cộng thẳng** điểm của con, không tra lại ma trận.

**Điểm ban đầu do luật máy sinh, không phải AI chấm từng mục.** Luật nằm ở `work/swp/score-rule.py`:
bề rộng rút từ số đếm có thật trong mục (dòng nguồn trường, gạch DoD, số endpoint, số bảng),
độ khó cộng từ các cờ có bằng chứng (đổi trạng thái, chạy nền, ≥5 bảng, ràng buộc ngoại tuyến,
dựng môi trường, gọi hệ thống ngoài). Làm vậy để **mọi điểm tra ngược được về một con số đếm
được**, thay vì một lần đoán không kiểm lại được. Chỗ luật đếm sai là chỗ bước §2 phải bắt.

**Chỗ hở chỉ hạ độ tin cậy, không nâng trục.** Một mục còn câu hỏi chờ khách thì ghi
`confidence: low` — không vì thế mà cho thêm điểm. Việc chưa rõ không tự nhiên nhiều hơn;
chỉ là ước lượng của nó kém chắc hơn.

## 2. Năm mục mẫu người kiểm

Công cụ tự chọn 5 mục theo luật tất định — nhỏ nhất · trung vị dưới · lớn nhất · ngoại lệ ·
tin cậy thấp nhất — rồi hỏi hai lượt mỗi mục (mức khó trước, số điểm sau). Dưới đây là kết quả.

| Mục | Vì sao được chọn | Máy chấm | Người chốt | Lý do |
|---|---|---|---|---|
| [#19](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/19) `BL-M1-002` — làm rõ CQ31 (xếp hạng khả năng tiếp cận FEFO) | nhỏ nhất | 1 × 1 → **1** | 1 × 1 → **1** | **Giữ nguyên.** Việc là soạn câu hỏi cho khách và ghi câu trả lời vào tài liệu; không dựng logic, không động vào lược đồ. Mức rất dễ đúng với nội dung. |
| [#193](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/193) `BL-M6-206` — `S1-29` lớp BE | trung vị dưới | 3 × 2 → **5** | 5 × 2 → **8** | **Nâng bề rộng.** Mục này bắc qua ba mục khác của M6 cộng hai module M3 và M9 — luật chấm đếm "một thành phần" nên hụt. Độ khó giữ 2: luật ghi và đo lại nằm ở mục khác, còn các chỗ chưa chốt chỉ hạ độ tin cậy. |
| [#110](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/110) `BL-M6-183` — lược đồ nhóm Nhiệt độ · HACCP | lớn nhất | 8 × 5 → **24** | 8 × 3 → **16** | **Hạ độ khó.** Luật bật cờ "gọi hệ thống ngoài" chỉ vì bảng nguồn trường có một dòng loại *API ngoài* — nhưng đây là mục viết DDL, không gọi hệ thống nào. Hai cờ còn lại (migration, đổi trạng thái) là thật. Bề rộng giữ 8: mạng 35 khoá ngoại bắc qua sáu module. |
| [#92](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/92) `BL-M5-150` — dựng 11 API của M5 (phần 1/2) | ngoại lệ | 8 × 5 → **24** | 8 × 5 → **24** | **Giữ nguyên sau khi cân nhắc nâng lên rất khó.** Phần này là lõi nghiệp vụ và ảnh hưởng rộng, nhưng *mức quan trọng không đi vào điểm* — nó đi vào thứ tự ưu tiên và Iteration; điểm đo khối lượng. 11 endpoint nằm gọn trong một module nên chưa phải cross-system; phần thuật toán nặng đã tách sang `BL-M5-180`, `BL-M5-181`. |
| [#30](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/30) `BL-M1-180` — hiện thực Technical Design `N4-13` | tin cậy thấp nhất | 3 × 2 → **5** | 5 × 2 → **8** | **Nâng bề rộng.** Một thành phần, nhưng phục vụ bốn loại luật và mọi module đều gọi tới; luật chấm chỉ đếm "một Technical Design" nên hụt bề rộng thật. Độ khó giữ 2. |

### 2.1 Ba lệch hệ thống rút ra, và việc đã lan ra đâu

Hai mục giữ nguyên là **xác nhận luật chấm đúng ở lớp đó**, không phải bỏ qua. Ba mục sửa
đều sửa vì **luật đếm sai một thứ đếm được** — nên mỗi mục sửa sinh một luật lan, chỉ áp cho
tầng **trùng khớp đúng chữ ký**, không áp bằng một độ lệch chung:

| Luật | Từ mục | Tầng áp | Đổi gì | Số mục bị đổi |
|---|---|---|---|---|
| `cal-001` | #193 | API · feature · `single-component` · `stateful` | bề rộng +1 bậc | 12 mục khác |
| `cal-002` | #110 | DB · migration · `multi-component + data-migration` · `stateful + migration + external-integration` | độ khó −1 bậc | 0 mục khác — không mục nào trùng đủ cả sáu chữ ký |
| `cal-003` | #30 | Unknown · feature · `single-component` · `stateful` | bề rộng +1 bậc | 10 mục khác |
| | | | **tổng đổi điểm** | **25 mục** (3 mục mẫu + 22 mục lan) |

Tổng đi từ **1182 → 1245**, tăng 5,3 %. Tăng chứ không giảm, vì hai lệch nâng bề rộng
chạm nhiều mục hơn một lệch hạ độ khó.

**Chỗ phương pháp này còn yếu — tự khai.** Chạy ở mức `medium` nên **không có người chấm
độc lập thứ hai**: không có chỗ nào lộ ra hai bên chấm lệch nhau. Bằng chứng đưa vào chấm là
bản rút gọn máy làm (giữ Mục tiêu, nhãn, dòng chỗ hở; các bảng rút thành số đếm), nên mọi mục
mang `detailStatus = partial`. Và năm mục mẫu là **năm**, không phải một mẫu đại diện thống kê —
ba luật lan ở trên chỉ chạm 25 mục, 176 mục còn lại chưa ai soi bằng mắt.

## 3. Cách quy ra giờ

> **Bản nháp — chờ duyệt ở G5a.** Chưa ghi giờ lên bảng công việc.

### 3.1 Chọn quy ước đổi điểm ra giờ, không ước trực tiếp từng mục

Đề cho hai đường: ước giờ thẳng từ nội dung mục, hoặc đặt quy ước đổi SWP ra giờ. Tôi chọn
**quy ước**, vì ba lẽ:

1. **Ước trực tiếp 201 mục là lần đoán thứ hai, không tra ngược được về gì.** SWP đã có đường
   tra ngược — về số đếm trong mục, rồi về bảng nguồn trường và DoD. Đoán lại giờ từ đầu là
   vứt đường đó đi và thay bằng cảm giác lúc đọc.
2. **Phải tất định.** Cùng một mục, chạy lại phải ra cùng một số. Quy ước làm được; ước trực
   tiếp thì hai lần đọc cách nhau một ngày đã ra hai số.
3. **Hai lần nhân độ khó là tính hai lần.** Ma trận SWP đã gộp độ khó vào điểm rồi. Nếu lại
   nhân thêm hệ số theo độ khó khi đổi ra giờ thì một mục khó bị tính độ khó hai lượt.

### 3.2 Hệ số: 6,4 giờ cho một điểm, neo vào chính baseline LAB-1

Hệ số **không bịa**. Nó là thương của hai con số có nguồn:

| Vế | Giá trị | Nguồn |
|---|---:|---|
| Một tháng công bằng bao nhiêu giờ | 20 ngày × 7 giờ = **140 giờ** | `effort-estimation/06-appendix.md` — *Working days in a month 20* · *Working hours in a day 7* [FACT] |
| Phần LAB-1 trùng với backlog | BE 33,06 + FE 20,81 = 53,87 MM = **7.541,8 giờ** | `wbs/00-wbs-by-feature.csv`, cộng 44 dòng feature [FACT] |
| Phần SWP trùng tương ứng | 1.245 − 61 (epic `SETUP`) − 5 (mục làm rõ) = **1.179 điểm** | `work/swp/payload-cal.json` |
| **Hệ số** | 7.541,8 ÷ 1.179 = 6,396 → **6,4 giờ/điểm** | |

**Vì sao chỉ lấy BE + FE làm lát neo.** Backlog này là việc *dựng*: lớp BE, lớp FE, lớp test
của màn, lược đồ bảng, nhóm API. Ba khoản còn lại của LAB-1 **không có mục tương ứng trong
backlog** nên không được kéo vào mẫu số: Design UI/UX 4,10 MM, QA độc lập 25,96 MM (LAB-1 ước
theo 2.363 test case, không theo feature — `wbs.md` tự ghi rõ đó là số *phân bổ*), và hệ số
quản lý 0,2 nằm ngoài BE/FE. Gộp chúng vào sẽ thổi hệ số lên ~9,9 giờ/điểm cho một backlog
không chứa việc đó.

**Làm tròn nửa giờ, nửa lên.** 1 điểm × 6,4 = 6,4 giờ; ghi "6,4" là tự nhận độ chính xác
12 phút mà phép đo không có. Lưới nửa giờ nói đúng mức chắc chắn thật. Luật nằm ở
`work/swp/hours-rule.py`, một hàm thuần không trạng thái — chạy lại ra đúng số cũ.

### 3.3 Chỗ quy ước này tự nó không chứng minh được

**Tổng khớp LAB-1 là do tôi đặt cho khớp, không phải phát hiện.** Hệ số được chia ra từ chính
tổng LAB-1, nên tổng giờ phần trùng bằng 7.545,6 ≈ 7.541,8 là **tất yếu**, không mang thông tin.
Thứ mang thông tin là **phân bố**: module nào trong backlog nặng hơn hay nhẹ hơn LAB-1 — phần
đó để §5.

**`SETUP` vắt qua hai nguồn — tôi không tự chọn một cách hiểu.** Hai đoạn nguồn cùng có lý:
`F0-00` trong 83,73 MM nêu đích danh *API scaffolding* và *hạ tầng PWA* (tức khung dự án), còn
sheet hạ tầng riêng có cột DEVELOPMENT **3,5 MM = 490 giờ** cho việc dựng môi trường (*sheet tự
ghi "dữ liệu chỉ mang tính tham khảo"*). `SETUP` của backlog có cả hai loại việc.

Tôi **để `SETUP` ngoài mẫu số** khi chia hệ số. Hệ quả đo được: nếu đưa vào, mẫu số thành
1.240 điểm và hệ số xuống **6,1 giờ/điểm** — tổng thấp hơn 5 %, thứ tự epic không đổi. Đây là
**điểm còn mở**, không phải điểm đã chốt; nó không đổi kết luận nào ở §5 ngoài dòng "Nền tảng
và môi trường".

Phép thử ít dính hệ số nhất: `SETUP` 390 giờ so với 490 giờ của cột DEVELOPMENT — cùng bậc,
lệch 20 %.

**Giả định nằm dưới cả quy ước:** một điểm SWP tốn như nhau ở mọi module và mọi loại việc.
Thực tế không vậy, và §5 sẽ chỉ ra đúng chỗ nó không vậy.

### 3.4 Áp thử trên epic M1 — Dữ liệu chủ

Chọn M1 vì nó có đủ mọi loại mục của backlog (làm rõ câu hỏi · lược đồ · nhóm API · màn mức B ·
ba lớp BE/FE/test của màn mức A · Technical Design) và trải từ điểm thấp nhất tới gần cao nhất.

| Mục | SWP | SWP × 6,4 | Giờ ghi lên Project |
|---|---:|---:|---:|
| [#31](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/31) `BL-M1-181` — M1_DB — dựng lược đồ nhóm Dữ liệu chủ (phần 1/2) | 16 | 102,4 | **102,5** |
| [#32](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/32) `BL-M1-182` — M1_DB — dựng lược đồ nhóm Dữ liệu chủ (phần 2/2) | 16 | 102,4 | **102,5** |
| [#28](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/28) `BL-M1-150` — M1 — dựng 12 API có spec riêng (phần 1/2) | 12 | 76,8 | **77,0** |
| [#21](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/21) `BL-M1-051` — S1-04 — dựng màn theo spec mức B | 8 | 51,2 | **51,0** |
| [#25](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/25) `BL-M1-055` — S1-48 — dựng màn theo spec mức B | 8 | 51,2 | **51,0** |
| [#26](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/26) `BL-M1-056` — S1-49 — dựng màn theo spec mức B | 8 | 51,2 | **51,0** |
| [#27](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/27) `BL-M1-057` — S1-50 — dựng màn theo spec mức B | 8 | 51,2 | **51,0** |
| [#29](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/29) `BL-M1-151` — M1 — dựng 3 API có spec riêng (phần 2/2) | 8 | 51,2 | **51,0** |
| [#30](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/30) `BL-M1-180` — N4-13 — hiện thực Technical Design | 8 | 51,2 | **51,0** |
| [#155](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/155) `BL-M1-209` — S1-06 — lớp BE | 8 | 51,2 | **51,0** |
| [#22](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/22) `BL-M1-052` — S1-05 — dựng màn theo spec mức B | 5 | 32,0 | **32,0** |
| [#158](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/158) `BL-M1-212` — S1-07 — lớp BE | 5 | 32,0 | **32,0** |
| [#18](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/18) `BL-M1-001` — Thêm cột fefo_access_rank vào bảng location | 4 | 25,6 | **25,5** |
| [#20](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/20) `BL-M1-050` — S1-03 — dựng màn theo spec mức B | 4 | 25,6 | **25,5** |
| [#156](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/156) `BL-M1-210` — S1-06 — lớp FE | 4 | 25,6 | **25,5** |
| [#157](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/157) `BL-M1-211` — S1-06 — lớp test | 4 | 25,6 | **25,5** |
| [#159](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/159) `BL-M1-213` — S1-07 — lớp FE | 4 | 25,6 | **25,5** |
| [#160](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/160) `BL-M1-214` — S1-07 — lớp test | 4 | 25,6 | **25,5** |
| [#19](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/19) `BL-M1-002` — Làm rõ CQ31 — khả năng tiếp cận FEFO xếp hạng theo gì | 1 | 6,4 | **6,5** |
| **Tổng epic M1** | **135** | 864,0 | **862,5** |


## 4. Tổng theo epic

Giờ ở đây là của **mục lá** — không cộng mục cha, vì mục cha đã mang giờ của con nó.
Trường `Est. hours` trên bảng công việc ghi cho **cả hai tầng**, nên mọi phép cộng từ board
phải lọc bỏ tầng cha trước; nếu không sẽ đếm hai lần.

| Epic | Số issue | Tổng SWP | Tổng giờ | Ghi chú |
|---|---:|---:|---:|---|
| `M5` — Vận tải và Giao hàng | 32 | 263 | 1 681,5 | Lõi nghiệp vụ: lập tuyến, giao hàng, chứng cứ ngoại tuyến. Epic nặng nhất, 15 mục là ba lớp của màn mức A |
| `M4` — Đơn và Phân bổ | 21 | 166 | 1 060,5 | Phân bổ 6 bước; 5 mục lược đồ vì nhóm bảng đơn hàng rộng |
| `M1` — Dữ liệu chủ | 19 | 135 | 862,5 | Dữ liệu chủ; chứa 2 mục làm rõ câu hỏi còn treo (`CQ31`) |
| `M3` — Tồn kho và Kho | 20 | 127 | 811,5 | Nhiều màn mức B nhất (10) — tồn kho có nhiều màn tra cứu đơn giản |
| `M9` — Nền tảng và Bảo mật | 20 | 126 | 807,0 | 11 mục việc nền (xác thực, phân quyền, nhật ký audit, job tích hợp) |
| `M6` — Nhiệt độ và HACCP | 18 | 124 | 793,0 | Nhiệt độ · HACCP; 5 mục lược đồ — nhóm bảng đo và cảnh báo rộng |
| `M7` — Truy xuất và Thu hồi | 12 | 101 | 646,0 | Truy xuất và thu hồi; 6 mục là ba lớp của hai màn mức A |
| `M2` — Nhập hàng | 10 | 67 | 428,0 | Nhập hàng; phần lớn nằm ở M3 vì tạo lô thuộc tồn kho |
| `SETUP` — Dựng môi trường | 7 | 61 | 390,0 | Nguồn đối chiếu **còn mở**: một phần ứng với `F0-00` (API scaffolding, hạ tầng PWA) nằm trong 83,73 MM, một phần ứng với sheet hạ tầng nằm ngoài — xem §5.2 |
| `M8` — Báo cáo | 6 | 36 | 230,5 | Báo cáo; 2 mục là câu hỏi còn treo về phạm vi xuất dữ liệu |
| `M10` — Chuyển đổi dữ liệu | 3 | 24 | 153,5 | Chuyển đổi dữ liệu — **mỏng hơn LAB-1 rõ rệt**, xem §5 |
| `DRV` — Ràng buộc giờ tài xế | 2 | 8 | 51,0 | Ràng buộc giờ lái xe 2024 — tách riêng theo yêu cầu đề (hạng mục nghiệp vụ đặc thù) |
| `PLAT` — Quy ước dùng chung | 2 | 7 | 45,0 | Quy ước API dùng chung và nhóm bảng COMMON_DB |
| **Tổng** | **172** | **1245** | **7 960,0** | = 56,9 MM theo đơn vị LAB-1 (140 giờ/MM) |

## 5. So với ước lượng LAB-1

Mốc so là **K1 = 83,73 MM** (chốt ở G0), trong đó lát trùng với backlog là BE + FE =
53,87 MM = 7.541,8 giờ. Ba khoản LAB-1 có mà backlog không có — Design UI/UX, QA độc lập,
hệ số quản lý — **không đưa vào bảng này**; đưa vào sẽ so một thứ với thứ khác.

Hai chỗ phải ghép lại trước khi so, nếu không con số nói sai:

- **`F0-00` của LAB-1 nằm trong module M9** (nền tảng kiến trúc, 568 giờ), còn backlog tách
  thành `SETUP` + `PLAT`. So thẳng M9 ↔ M9 sẽ ra −42 % — đó là **lỗi ghép**, không phải chênh thật.
- **`DRV` tách riêng** theo yêu cầu đề (hạng mục chịu ràng buộc giờ lái xe 2024); ở LAB-1 nó
  nằm trong M5 (`F1-09`, `F1-30`).

| Phần | LAB-1 (giờ) | LAB-5 (giờ) | Chênh | Vì sao |
|---|---:|---:|---:|---|
| M1 — Dữ liệu chủ | 568 | 862,5 | +52 % | Backlog nặng hơn vì **màn mức B là một mục trọn gói BE+FE+test**, còn LAB-1 tách effort theo vai trò và màn tra cứu đơn giản rơi vào đơn giá thấp. 6 màn mức B × 25,5–51 giờ đã là 230 giờ |
| M2 — Nhập hàng | 480 | 428,0 | −11 % | Khớp sát. Nhập hàng ở backlog chỉ còn phần mở phiếu và ghi kiểm; tạo lô đã chuyển sang M3 theo đúng cách LAB-1 chia |
| M3 — Tồn kho và Kho | 910 | 811,5 | −11 % | Khớp sát, dù backlog có tới 10 màn mức B — bù lại không có mục lược đồ lớn nào |
| M4 — Đơn và Phân bổ | 970 | 1 060,5 | +9 % | Khớp sát. Phần thuật toán phân bổ 6 bước nằm ở `BL-M4-180`, `BL-M4-181`, cùng bậc với LAB-1 |
| M5 — Vận tải và Giao hàng · `M5` + `DRV` ↔ M5 | 1 393 | 1 732,5 | +24 % | Cao hơn vì backlog tách `DRV` ra thành hạng mục riêng theo yêu cầu đề, và 15 mục ba-lớp của màn mức A đội giờ lên |
| M6 — Nhiệt độ và HACCP | 743 | 793,0 | +7 % | Khớp sát |
| M7 — Truy xuất và Thu hồi | 385 | 646,0 | +68 % | Cao hơn rõ: 6 mục ba-lớp cho hai màn mức A `S1-33`, `S1-34`. LAB-1 ước thu hồi là một feature gọn, backlog mổ ra thành BE · FE · test nên lộ thêm việc |
| M8 — Báo cáo | 314 | 230,5 | −26 % | Thấp hơn: backlog mới có 3 màn báo cáo, hai câu hỏi phạm vi xuất dữ liệu còn treo nên **chưa break hết** |
| M9 — Nền tảng và Bảo mật · `M9` ↔ M9 trừ `F0-00` | 911 | 807,0 | −11 % | Khớp sát sau khi trừ `F0-00` ra khỏi M9 của LAB-1 — nếu không trừ thì lệch tới −42 %, và đó là lỗi so sánh chứ không phải chênh thật |
| M10 — Chuyển đổi dữ liệu | 298 | 153,5 | −49 % | **Chênh lớn nhất và là chỗ backlog thiếu.** LAB-1 ước 298 giờ cho bảng điều khiển migration (`F1-20`) cộng thực thi + ghi lineage (`F4-02`); backlog mới có 2 màn và 1 lược đồ, chưa có mục nào cho script chuyển đổi, đối soát và chạy thử. Xem §5.1 |
| Nền tảng và môi trường · `SETUP` + `PLAT` ↔ `F0-00` | 568 | 435,0 | −23 % | **Dòng kém chắc nhất bảng.** `F0-00` gồm event store, version hoá rule, RBAC, API scaffolding, hạ tầng PWA; backlog có phần scaffolding và môi trường (`SETUP`) cùng quy ước dùng chung (`PLAT`) nhưng **chưa có mục cho event store append-only và version hoá rule**. Một phần `SETUP` lại thuộc sheet hạ tầng, ngoài 83,73 MM — xem §5.2 |
| **Tổng** | **7 542** | **7 960,0** | **+6 %** | Chênh +6 % **không phải phát hiện**: hệ số được chia ra từ 1.179 điểm của lát trùng (§3.2), còn bảng này cộng cả `SETUP` (390 giờ) và 5 mục làm rõ (32,5 giờ) vào vế LAB-5 |

### 5.1 Chênh −49 % ở M10 là chỗ backlog thiếu, không phải chỗ LAB-1 ước thừa

LAB-1 có hai feature cho chuyển đổi dữ liệu: `F1-20` bảng điều khiển migration và đối soát
(189 giờ) và `F4-02` thực thi migration cùng ghi lineage (109 giờ). Backlog hiện có **3 mục**:
hai màn mức B (`S1-40`, `S1-41`) và một mục lược đồ `M10_DB`. **Không có mục nào** cho script
chuyển đổi, cho đối soát sau khi chạy, hay cho lần chạy thử trên dữ liệu thật.

Đây là **chỗ hở của bước break task**, đã ghi vào `task-breakdown.md`. Không vá bằng cách
nâng giờ của ba mục đang có: việc chưa được chia thì chưa có mục để gắn giờ, nâng số chỉ
làm con số đẹp lên mà backlog vẫn thiếu việc. Phải thêm mục ở vòng break sau.

Hai chênh dương lớn (M1 +52 %, M7 +68 %) **không** phải chỗ thiếu của LAB-1. Chúng đến từ
cách chia: một màn mức B ở backlog là **một mục trọn gói BE + FE + test**, nên giờ dồn vào
một chỗ; LAB-1 rải cùng khối lượng đó qua ba vai trò với đơn giá khác nhau.


### 5.2 Một chỗ hai nguồn cùng có lý — để mở, không tự chọn

Epic `SETUP` ứng với **cả hai**: `F0-00` trong 83,73 MM nêu đích danh *API scaffolding* và
*hạ tầng PWA*, còn sheet hạ tầng riêng có cột DEVELOPMENT 3,5 MM = 490 giờ cho việc dựng
môi trường. Hai đoạn này không mâu thuẫn nhau — chúng chồng lấn, và `SETUP` nằm đúng chỗ
chồng.

Tôi để `SETUP` ngoài mẫu số khi chia hệ số (§3.2). **Hệ quả đo được**: đưa vào thì hệ số
xuống 6,1 giờ/điểm, tổng thấp hơn 5 %, thứ tự epic không đổi. Dòng *Nền tảng và môi trường*
ở bảng trên là dòng duy nhất bị ảnh hưởng, và nó cũng là dòng kém chắc nhất bảng.

Để mở chứ không chốt: cần người nghiệp vụ nói `F0-00` có bao gồm dựng môi trường chạy thử
và đường chạy kiểm tự động hay không. Trước khi có câu trả lời, mọi con số trong mục này
đọc kèm sai số ±5 % ở mức tổng.

