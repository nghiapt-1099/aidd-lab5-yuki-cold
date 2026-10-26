# estimate.md — chấm SWP cho backlog Yuki Cold Logistics

> Trạng thái: §1–§2 đã chốt ở gate G4 (2026-10-26). §3–§5 làm ở bước quy giờ, **chưa có số** —
> không điền tạm để tránh đọc nhầm là đã chốt.

## 1. Báo cáo của công cụ chấm

| Thứ | Giá trị |
|---|---|
| Báo cáo HTML | [`Output/swp-report/swp-lab5-2026-10-26.html`](swp-report/swp-lab5-2026-10-26.html) — tự chứa, mở offline, không gọi tài nguyên ngoài |
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

*Chưa làm — bước B6.*

## 4. Tổng theo epic

*Chưa làm — bước B6 (cần số giờ ở §3).*

## 5. So với ước lượng LAB-1

*Chưa làm — bước B6.*
