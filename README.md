# Backlog quản lý dự án — Yuki Cold Logistics

| | |
|---|---|
| **Dự án** | 食品コールドチェーン業務支援システム — hệ thống hỗ trợ nghiệp vụ chuỗi lạnh thực phẩm |
| **Học viên** | nghiapt-1099 (Phạm Trọng Nghĩa) |
| **Đề** | Product B — Yuki Cold Logistics |

> Đây là bài tập của khoá AIDD Leader. **Không phải sản phẩm thương mại, không chứa dữ liệu thật.**

## 1. Tổng quan

Backlog chia từ **bộ thiết kế chi tiết LAB-4 nộp ngày 2026-10-15**, cộng trừ những gì khai ở
[`PREMISE.md`](PREMISE.md) — **có thay đổi**: 10 dòng sửa thiết kế (§1), 6 giả định bổ sung (§2) và 5 câu hỏi khách còn mở (§6).

| Số | Đếm cái gì |
|---:|---|
| **13** | epic trên Project |
| **151** | issue (không tính epic, không tính sub-issue) |
| **48** | sub-issue của màn mức A (ba lớp BE · FE · test) |
| **183** | mục lá được chấm điểm trực tiếp = 201 − 29 mục cha. Mục cha gồm 13 epic và 16 issue của màn mức A (đã có sub-issue nên không chấm trực tiếp) |
| **1288** | SWP cộng trên mục lá; mục cha lấy tổng của con nên **không cộng vào đây** |
| **8 235,5** | giờ ước lượng cộng trên mục lá = 58,8 tháng công (140 giờ/tháng) |

**Sprint giả lập đã chạy:** `Inc 0 — nền tảng` (7 mục, xong) và `Sprint 1`
(10 mục · 5 xong · 2 đang làm · 3 trượt sang sprint sau). Báo cáo ở
[`progress-report.md`](progress-report.md).

## 2. Link

| Thứ | Ở đâu |
|---|---|
| Kho này | https://github.com/nghiapt-1099/aidd-lab5-yuki-cold |
| Bảng công việc | [GitHub Project](https://github.com/users/nghiapt-1099/projects/1) |
| Báo cáo chấm điểm HTML | [`swp-report/swp-lab5-2026-10-26.html`](swp-report/swp-lab5-2026-10-26.html) |

**Đã kiểm mở được không cần đăng nhập:** gọi từng file bằng `curl` **không kèm thông tin đăng
nhập** — cả 9 file trả `HTTP 200`; trang kho và bảng công việc cũng `200`. Trước đó đã mở thử
bằng cửa sổ ẩn danh.
Riêng file HTML, GitHub trả về dưới dạng mã nguồn — tải về rồi mở bằng trình duyệt thì xem
được đầy đủ, file tự chứa, không gọi tài nguyên ngoài.

## 3. Cấu trúc kho

| File / thư mục | Nội dung |
|---|---|
| `README.md` | File này — tổng quan, link, bảng Spec → epic |
| [`PREMISE.md`](PREMISE.md) | Tiền đề: backlog khác bản LAB-4 đã nộp ở đâu, vì sao |
| [`task-breakdown.md`](task-breakdown.md) | Nguyên tắc chia việc, định nghĩa xong, chỗ hở phát hiện khi chia |
| [`epic-mau-M2.md`](epic-mau-M2.md) | Một epic đầy đủ làm mẫu, kèm một mô tả issue nguyên văn |
| [`estimate.md`](estimate.md) | Chấm SWP, năm mục người kiểm, cách quy ra giờ, so với ước lượng LAB-1 |
| [`progress-report.md`](progress-report.md) | Báo cáo tiến độ Sprint 1 |
| [`burndown-sprint-1.svg`](burndown-sprint-1.svg) | Ảnh burndown của Sprint 1 |
| [`AI-report.md`](AI-report.md) | Dùng AI thế nào, soát ra sao, prompt nguyên văn |
| `swp-report/` | Báo cáo HTML của công cụ chấm điểm |

**Thứ kho này không chứa:** đề bài gốc, trích đoạn RFP, tài liệu thiết kế chi tiết, dữ liệu
thật, dữ liệu cá nhân, khoá bí mật. Mục nào cần tới thiết kế thì **dẫn bằng mã** (`S2-04`,
`API-M3-029`, `M3_DB.md`) chứ không chép nội dung.

**Đường dẫn nội bộ.** Các tài liệu dưới đây được nhắc tới nhưng **không nộp** — chúng nằm trong
thư mục làm việc của học viên, người ngoài không mở được:

| Đường dẫn | Là gì |
|---|---|
| `work/ai-log.md` | Sổ lỗi AI, ghi ngay lúc bắt được |
| `work/test-summary.md` | Sổ vòng kiểm `R1`…`R19` |
| `work/checks/` | Bộ kiểm máy và kết quả từng vòng |
| `work/prompts/` | Prompt nguyên văn và output gốc của từng lượt giao AI |
| `work/backlog.csv`, `work/issues/`, `work/issue-comments/` | Nguồn sinh ra 212 mục trên bảng |
| `work/swp/`, `work/sprint/` | Dữ liệu chấm điểm, quy giờ và kịch bản sprint |
| `work/push-*.py`, `work/reset-board.py` | Script ghi lên bảng công việc và script đưa về ban đầu |
| `LAB-5 Planning.md` | Kế hoạch các bước `B0`…`B9` và gate `G0`…`G7` |
| `LAB-4/Output/specs/` | Bộ thiết kế chi tiết — mốc để chia việc |
| `Requirements/` | Tài liệu yêu cầu gốc (SSOT) và ước lượng LAB-1 |

## 4. Spec → epic

| Spec nguồn (LAB-4) hoặc dòng PREMISE | Epic trên Project | Mục lá | SWP | Ghi chú |
|---|---|---:|---:|---|
| `m1-master-data/` · `M1_DB.md` · `api-list.md` (API-M1-*) | [#5](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/5) — `BL-M1-000` Dữ liệu chủ | 19 | 135 | Dữ liệu chủ: khách, SKU, vị trí kho, phương tiện, tài xế |
| `m2-inbound/` · `M2_DB.md` | [#1](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/1) — `BL-M2-000` Nhập hàng | 11 | 68 | Nhập hàng và ghi kiểm; tạo lô nằm ở M3 |
| `m3-inventory/` · `M3_DB.md` | [#7](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/7) — `BL-M3-000` Tồn kho | 20 | 127 | Tồn kho, lô, cất hàng, kiểm kê, cách ly |
| `m4-outbound/` · `M4_DB.md` | [#8](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/8) — `BL-M4-000` Xuất hàng | 23 | 168 | Đơn hàng, phân bổ 6 bước, lấy hàng, xếp hàng |
| `m5-transport-delivery/` · `M5_DB.md` | [#9](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/9) — `BL-M5-000` Vận tải · giao hàng | 33 | 264 | Lập tuyến, giao hàng, chứng cứ POD ngoại tuyến |
| `m6-temperature-haccp/` · `M6_DB.md` | [#10](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/10) — `BL-M6-000` Nhiệt độ · HACCP | 19 | 125 | **Nhãn `rb-haccp`** — nguồn pháp lý ngoài, xem `task-breakdown.md` |
| `m7-traceability-recall/` · `M7_DB.md` | [#11](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/11) — `BL-M7-000` Truy xuất · thu hồi | 13 | 102 | Truy xuất ngược xuôi, thu hồi |
| `m8-reporting-audit/` · `M8_DB.md` | [#12](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/12) — `BL-M8-000` Báo cáo · kiểm toán | 7 | 37 | Báo cáo, xuất dữ liệu, nhật ký kiểm toán |
| `m9-platform-security/` · `M9_DB.md` | [#13](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/13) — `BL-M9-000` Nền tảng · bảo mật | 21 | 134 | Xác thực, phân quyền, 11 chức năng nền `N4-*` |
| `m10-data-migration/` · `M10_DB.md` | [#6](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/6) — `BL-M10-000` Chuyển dữ liệu | 5 | 49 | **Chia còn thiếu mục** — `task-breakdown.md` §4.11 |
| `_common/api-conventions.md` · `COMMON_DB` | [#14](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/14) — `BL-PLAT-000` Quy ước dùng chung | 3 | 10 | Quy ước dùng chung mọi module |
| `PREMISE.md` §1.6 · `m5-transport-delivery/` (`N4-18`) | [#4](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/4) — `BL-DRV-000` Ràng buộc giờ lái 2024 | 2 | 8 | **Ràng buộc giờ lái xe tải từ 2024** — hạng mục nghiệp vụ đặc thù, tách riêng theo yêu cầu đề |
| `PREMISE.md` §2 (giả định) · `ARCHITECTURE_DESIGN.md` | [#15](https://github.com/nghiapt-1099/aidd-lab5-yuki-cold/issues/15) — `BL-SETUP-000` Nền tảng dự án và dữ liệu mẫu | 7 | 61 | Dựng môi trường, công cụ, dữ liệu mẫu — **không có trong thiết kế LAB-4**, sinh ra khi chia việc |
| | **Tổng** | **183** | **1288** | |

## 5. Thao tác không hoàn tác được, và cách đưa về ban đầu

| Thao tác | Mất gì | Khôi phục |
|---|---|---|
| Đóng / mở lại issue | Không mất gì | Mở lại được, lịch sử giữ nguyên |
| Đổi `Status`, `Iteration`, `Point`, `Est. hours` trên bảng | **Giá trị cũ không xem lại được** qua giao diện | Hai đường: *về trạng thái đã chốt* — chạy lại `work/push-points.py`, `work/push-hours.py`, `work/push-sprint.py`; *về trạng thái ban đầu* — xem mục dưới |
| Xoá một mục khỏi Project | Mất liên kết mục ↔ bảng, issue vẫn còn | Thêm lại bằng `work/push-backlog.py` (nội bộ) |
| Xoá issue trên kho | **Mất hẳn**, không khôi phục được | Không có đường lùi — dựng lại bằng `work/gen-backlog.py` sẽ ra issue **số mới** |

### Đưa dữ liệu về ban đầu

**Trạng thái ban đầu** của bảng công việc là: 212 issue đều `open`, không mục nào có `Status`,
`Iteration`, `Point` hay `Est. hours`. Script `work/reset-board.py` (nội bộ) đưa về đúng trạng
thái đó: mở lại mọi issue đã đóng và xoá giá trị bốn trường trên. Nó **không xoá** issue, không
gỡ mục khỏi Project, không đụng nội dung.

Script **không chạy nếu thiếu cờ `--toi-chac-chan`** — gọi trơn thì chỉ in ra sẽ làm gì. Đây là
bước xác nhận bắt buộc trước một thao tác không xem lại được.

**Phạm vi môi trường:** chỉ có **một** môi trường — Project 1 và kho công khai của tài khoản
`nghiapt-1099`. Không có bản thử riêng. Mọi script `work/push-*.py` và `reset-board.py` đều
ghi thẳng vào đó, nên không chạy chúng khi chỉ muốn thử.

Kho này chỉ chứa tài liệu; không có lệnh nào trong kho xoá hay đổi dữ liệu. Mọi thao tác
đổi dữ liệu đều nằm ở bảng công việc.

## 6. Cách đọc mã trong các file

| Mã | Nghĩa |
|---|---|
| `BL-{epic}-{nnn}` | Mã ổn định của mục trong backlog. `nnn` là khối số: `000` epic · `001–009` thay đổi khai ở `PREMISE.md` và mục làm rõ câu hỏi khách · `010–019` dựng môi trường · `020–039` chức năng nền · `050–149` màn · `150–179` nhóm API · `180–199` lược đồ và Technical Design · `200+` sub-issue |
| `S1-03`, `S2-04` | Mã màn hình trong thiết kế LAB-4 |
| `API-M3-029` | Mã API; `M3` là module |
| `M3_DB.md` | File thiết kế cơ sở dữ liệu của module M3 |
| `N4-01`, `N4-13`, `N4-18` | Mã **chức năng nền** trong thiết kế. Không phải mã nào cũng là job chạy ngầm: `N4-01` tạo lô, `N4-13` là bộ luật dùng chung cho mọi module, `N4-18` tính luỹ kế giờ tài xế |
| `CQ30`, `CQ31`, `CQ32`, `CQ35`, `CQ36` | Năm câu hỏi **đang chờ khách trả lời**. `CQ33` và `CQ34` đã rút, lý do ở [`PREMISE.md`](PREMISE.md) §6.1 |
| `ACC-DATE-01` | Cổng nghiệm thu của một increment trong lịch tổng |
| Mức spec **A** / **B** | A: màn có spec riêng đầy đủ, tách ba lớp BE · FE · test. B: màn spec gọn, một mục trọn gói |
| SWP | Điểm khối lượng = Bề rộng × Độ khó, **tra ma trận dưới đây** |

### Ma trận SWP — để người đọc tính lại được

Bề rộng ∈ {1, 2, 3, 5, 8, 13} · Độ khó ∈ {0, 1, 2, 3, 5, 8}. Ô là **tích đã làm tròn nửa lên**,
không phải tổng hai trục:

| | C0 | C1 | C2 | C3 | C5 | C8 |
|---|---:|---:|---:|---:|---:|---:|
| **S1** | 1 | 1 | 2 | 2 | 3 | 5 |
| **S2** | 2 | 3 | 3 | 4 | 6 | 10 |
| **S3** | 3 | 4 | 5 | 6 | 9 | 15 |
| **S5** | 5 | 6 | 8 | 10 | 15 | 25 |
| **S8** | 8 | 10 | 12 | 16 | 24 | 40 |
| **S13** | 13 | 16 | 20 | 26 | 39 | 65 |

Giờ = SWP × 6,4, làm tròn nửa giờ gần nhất — `estimate.md` §3.

### Thứ gì là thật, thứ gì là giả lập

| Thật | Giả lập |
|---|---|
| 212 issue và nội dung của chúng · quan hệ cha–con · nhãn · `Point` · `Est. hours` · báo cáo chấm điểm | Kết quả Sprint 1: mục nào xong, mục nào trượt, ngày xong từng mục |
| Năm mục mẫu người duyệt kiểm và sửa | Trạng thái `Done` và việc đóng issue — **là một phần của giả lập**, không phải đã code xong |
| Sức chứa sprint, ngày tháng (lấy từ lịch dự án của LAB-1) | Người phụ trách: trường `Owner (giả định)` là **vai**, không phải người thật |

Dự án **chưa khởi động**; không dòng mã nào được viết. Backlog chia từ bộ thiết kế đã có.
