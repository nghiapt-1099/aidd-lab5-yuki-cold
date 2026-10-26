# Dùng AI thế nào trong LAB-5

Ba phần theo yêu cầu đề: (1) giao việc cho AI ra sao, (2) soát output ra sao và bắt được gì,
(3) chỗ nào hiệu quả, chỗ nào chưa.

**Ngôi xưng trong bài này.** "Tôi" là **học viên** — người làm bài và là người chịu trách nhiệm
mọi dòng trong bộ nộp. Việc nào do AI làm thì gọi tên rõ: *session chính (Opus)* điều phối và
viết script, *subagent (Sonnet)* làm phần khối lượng, *Codex* soát chéo. Mọi quyết định ở tám
gate `G0`…`G7` là của tôi, ghi ngày và chữ ký trong `work/ai-log.md`.

**Hai con số đặt trước cho dễ đọc phần sau:** sổ lỗi AI của riêng LAB-5 có **223 dòng**, và bộ
kiểm chạy **21 vòng** (`R1`…`R21`). Hai file này nằm trong thư mục làm việc, **không nộp**:
`work/ai-log.md` và `work/test-summary.md`.

## 1. Giao việc cho AI

### 1.1 Chia vai

| Vai | Model | Làm gì |
|---|---|---|
| Session chính | Opus | Điều phối, phán đoán nghiệp vụ, **soát mọi output của subagent trước khi trình gate**, viết script, chạy mọi thao tác ghi lên kho và bảng công việc |
| Subagent | Sonnet | Phần khối lượng: đọc nguồn, viết mô tả issue, đọc mù |
| Review chéo | Codex (`codex:codex-rescue`) | Soát độc lập, không thấy quá trình suy nghĩ của session chính |
| Công cụ chấm điểm | skill `tkm:estimate-swp` | Chấm SWP, chọn mục mẫu, sinh báo cáo HTML |

**Việc tất định thì viết script, không giao AI.** Sinh 212 mục, rót nội dung tham chiếu, đổi
điểm ra giờ, ghi lên bảng công việc, mọi bộ kiểm — đều là script. AI chỉ làm phần cần đọc hiểu.

### 1.2 Khuôn một prompt giao việc

Mọi prompt giao subagent đều có đúng sáu phần: **việc cụ thể** · **chỉ những file được đọc**
(liệt kê đích danh, cấm mở rộng) · **file được sở hữu** (không chạm file của agent khác) ·
**tiêu chí nghiệm thu** · **yêu cầu liệt kê giá trị mock và chỗ mơ hồ** · **kết thúc bằng
`Status: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT`**.

Dưới đây là prompt **nguyên văn** cho từng loại việc, kèm output chính và quyết định nhận hay bác.

### 1.3 Đọc spec LAB-4 để soạn tiền đề (B1)

**Giao cho:** `general-purpose`, model `sonnet` · **Khi nào:** bốn lô chạy song song, mỗi lô một nhóm phát hiện của LAB-4

<details><summary>Prompt nguyên văn (2733 ký tự) — bấm để mở</summary>

````
Nhiệm vụ: trích **hiện trạng chính xác** của bộ thiết kế LAB-4 cho 2 điểm lỗi, để người khác viết tài liệu PREMISE. Bạn CHỈ ghi nhận hiện trạng — KHÔNG đề xuất cách sửa, KHÔNG quyết định gì.

Work context: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader
Reports: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader/LAB-5/work/reviews/
Plans: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader/LAB-5/

CHỈ ĐƯỢC ĐỌC những file này (đường dẫn tương đối từ work context):
- LAB-4/Output/specs/specs/m3-inventory/screen-specification/S2-04_SPEC.md
- LAB-4/Output/specs/specs/system-design/database-design/m1-master-data/M1_DB.md
- LAB-4/Output/specs/specs/system-design/database-design/m2-inbound/M2_DB.md
- LAB-4/Output/specs/specs/system-design/database-design/m3-inventory/M3_DB.md (nếu có)

CHỈ ĐƯỢC GHI file: LAB-5/work/reviews/b1-findings-A.md

Hai điểm cần trích:

**L4-1** — S2-04 hiển thị `fefo_access_rank` (độ dễ lấy theo FEFO) và "thứ tự gợi ý vị trí" nhưng không tài liệu nào định nghĩa cách tính; bảng `location` không có cột tiếp cận.
Cần trả lời chính xác:
1. S2-04 nhắc `fefo_access_rank` ở những dòng nào? Trích nguyên văn câu chứa nó (kèm số dòng).
2. S2-04 nói gì về "thứ tự gợi ý vị trí"? Trích nguyên văn + số dòng.
3. Bảng `location` trong M1_DB có những cột nào? Liệt kê đủ tên cột. Có cột nào liên quan độ dễ tiếp cận / vị trí / tầng / lối đi không?
4. Có file nào trong 4 file trên định nghĩa công thức tính `fefo_access_rank` không? Có/không + bằng chứng.

**L4-2** — S2-04 dùng `lot.qty_available` làm "phần chưa cất"; M2_DB định nghĩa `qty_available` là lượng còn phân bổ được → cất một phần thì số hiển thị sai. S2-04 dòng ~208 tự ghi phần chưa cất chưa có `stock_balance`.
Cần trả lời chính xác:
5. S2-04 dùng `qty_available` ở đâu, cho trường hiển thị nào? Trích nguyên văn + số dòng.
6. M2_DB (hoặc M3_DB) định nghĩa `qty_available` là gì? Trích nguyên văn định nghĩa + tên bảng + số dòng.
7. Dòng ~208 của S2-04 ghi gì về `stock_balance` / "phần chưa cất"? Trích nguyên văn + số dòng đúng.
8. Có bảng/cột nào trong M2_DB, M3_DB cho phép tính "phần đã cất" và "phần chưa cất" không (ví dụ số lượng đã putaway, số lượng nhận)? Liệt kê tên bảng + cột + ý nghĩa nếu có.

Tiêu chí nghiệm thu:
- Mỗi câu trả lời có **số dòng** và **trích nguyên văn ngắn** (≤2 câu mỗi trích).
- Câu nào không tìm thấy thì ghi "KHÔNG TÌM THẤY" kèm cách bạn đã tìm (lệnh grep đã chạy) — không đoán.
- Cuối báo cáo: mục "Giá trị mock / chỗ mơ hồ" liệt kê mọi giá trị trông như số liệu minh hoạ và mọi chỗ spec nói nước đôi.
- Không đề xuất cách sửa.

Kết thúc câu trả lời bằng đúng một dòng: `Status: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT`
````

</details>

**Output chính:** bốn báo cáo, mỗi báo cáo nêu chỗ thiết kế LAB-4 hở kèm số dòng.

**Quyết định — nhận một phần.** Nhận các phát hiện có trích dẫn dòng cụ thể. **Bác** mọi câu
kết luận dạng "nên sửa thành X" khi agent chưa đọc đủ nguồn: bốn bản PREMISE đầu đều hỏng vì
tôi nhận thẳng loại câu đó. Từ bản 5 đổi cách giao: agent **chỉ được đọc và trả stdout nguyên
văn, cấm kết luận**; session chính mới kết luận. Lỗi và cách sửa ghi ở `ai-log` #66, #67.
### 1.4 Thu bằng chứng, cấm kết luận (B1 bản 5 trở đi)

**Giao cho:** `general-purpose`, model `sonnet` · **Khi nào:** sau khi phát hiện họ lỗi 'khẳng định về nguồn mà chưa đọc đủ nguồn'

<details><summary>Prompt nguyên văn (3570 ký tự) — bấm để mở</summary>

````
Nhiệm vụ: ĐỌC nguồn và CHÉP LẠI nguyên văn những gì nguồn nói. Bạn **KHÔNG kết luận, KHÔNG đề xuất, KHÔNG quyết định, KHÔNG đánh giá đúng sai**. Người khác sẽ kết luận từ thứ bạn chép về.

Work context: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader
Reports: {work context}/LAB-5/work/evidence/raw/
Plans: {work context}/LAB-5/

## Luật bắt buộc khi chạy lệnh

1. **CẤM cắt output.** Không dùng `cut -c`, không `head`, không `tail`, không `| head -n`. Nếu output dài thì vẫn ghi đủ. Lý do: vòng trước có một câu trả lời nằm ở cuối dòng bị `cut -c1-160` cắt mất, dẫn tới kết luận sai.
2. **Lệnh phải tái chạy được**: ghi đường dẫn đầy đủ, không rút gọn bằng `…`.
3. Ghi cả **mã thoát** của mỗi lệnh.
4. Khi một câu trả lời nằm trong bảng, đọc **cả bảng cột**, không chỉ khối ERD. Khi nằm trong một thực thể, đọc **cả mục thiết kế** của thực thể đó, không chỉ định nghĩa enum.

## Phạm vi được đọc (cả ba lớp, không chỉ thiết kế)

- RFP bản JA: `Requirements/01_rfp/ja/` — tra theo mã (`FR-…`, `BR-…`) và theo mốc trang `<!-- p.NN -->`
- SSOT: `Requirements/02_business/`, `Requirements/03_requirements/`, `Requirements/04_design/`, `Requirements/05_planning/`
- Thiết kế LAB-4: `LAB-4/Output/specs/specs/` (spec màn, API Design, Technical Design, Database Design, `_common/`)

## File được ghi (chỉ file này)

`LAB-5/work/evidence/raw/ev-a-ton-kho-nhap-hang.md`

## Câu hỏi — tất cả đều là câu hỏi MỞ

Với mỗi câu: ghi **mọi** chỗ nguồn nói về nó, ở **cả ba lớp**. Nếu tìm không ra thì ghi `KHÔNG TÌM THẤY` kèm đủ các lệnh đã chạy.

1. **Nguồn nói gì về "stock unit"** — định nghĩa, nơi tạo, quan hệ với lô, có bảng nào trong Database Design không, kiến trúc có nhắc không?
2. **Nguồn nói gì về thời điểm cất hàng (put-away)** — xảy ra ở bước nào, màn nào, tạo movement lúc nào, quan hệ với bước xác nhận nhận hàng?
3. **Nguồn nói gì về gợi ý vị trí lưu trữ** — tiêu chí nào, xếp hạng theo gì, có trường dữ liệu nào đỡ tiêu chí "khả năng tiếp cận FEFO" không?
4. **Nguồn nói gì về bảng `location`** — liệt kê **đủ mọi cột** trong bảng cột (không chỉ ERD), kèm ghi chú của từng cột. Đặc biệt ghi đủ ghi chú của `capacity_pallets`.
5. **Nguồn nói gì về `qty_received` và `qty_available`** — định nghĩa ở RFP, ở Database Design, ở spec màn nào dùng chúng?
6. **Nguồn nói gì về `stock_balance`** — khoá, sinh khi nào, cập nhật theo gì, có bất biến đối soát nào không? Ghi đủ ghi chú cột `pallet_count`.
7. **Nguồn nói gì về việc sửa sai một movement đã ghi** — có loại movement nào để đảo không, cơ chế đảo nằm ở đâu, join thế nào?
8. **Nguồn nói gì về `v_stock_consistency`** — mọi chỗ nhắc tới, định nghĩa, lịch chạy nếu có?

## Thêm: truy vấn "đã có sẵn chưa"

Với mỗi câu trên, chạy thêm một lệnh tìm xem **nguồn đã có sẵn lời giải chưa** trước khi ai đó đề xuất thứ mới. Ví dụ: trước khi ai đó đề xuất thêm một cột, tìm xem đã có cột nào phục vụ mục đích đó chưa. Ghi kết quả dưới tiêu đề `Đã có sẵn chưa:`.

## Khuôn báo cáo trong file

Mỗi câu một mục:

```
## Câu N — <nguyên văn câu hỏi>

### Lệnh đã chạy
$ <lệnh đầy đủ>
(exit <mã thoát>)

### Kết quả (nguyên văn, không cắt)
<stdout đủ>

### Đã có sẵn chưa
$ <lệnh>
(exit <mã>)
<stdout đủ>
```

**Không viết mục "Kết luận".** Nếu thấy hai chỗ trong nguồn có vẻ nói khác nhau, chỉ ghi cả hai kèm số dòng — không nhận xét.

## Trả về cho tôi

Chỉ một **mục lục ngắn**: mỗi câu một dòng, nói đã tìm thấy ở những file/dòng nào (không chép nội dung, không kết luận). Nội dung đầy đủ nằm trong file.

Kết thúc bằng đúng một dòng: `Status: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT`
````

</details>

**Output chính:** ~5,7 MB stdout **không cắt** trong `work/evidence/raw/`, mỗi lệnh kèm
mã thoát và đường dẫn đầy đủ; session chính chắt thành 18 mục `EV-01`…`EV-18`.

**Quyết định — nhận toàn bộ, vì đây là dữ liệu chứ không phải kết luận.** Một reviewer độc lập
chạy lại 10 nhóm lệnh, cả 10 khớp. Nhưng bản 5 **vẫn còn 8 lỗi chặn** — lỗi đã dịch từ bước
*đọc* sang bước *suy*. Đó là lý do có thêm ba luật L5, L6, L7 (đánh dấu mức suy · phản chứng
trước khi chốt · "đã chốt" phải trỏ một quyết định có thật).
### 1.5 Viết mô tả issue (B3 — chia việc)

**Giao cho:** `general-purpose`, model `sonnet` · **Khi nào:** 11 lô, mỗi lô một epic, chạy nền song song

<details><summary>Prompt nguyên văn (3104 ký tự) — bấm để mở</summary>

````
Viết file mô tả issue cho epic **M5 (Vận tải · giao hàng)** của backlog LAB-5. Đây là epic lớn nhất — 35 mục.

Work context: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader
Plans: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader/LAB-5
Reports: /Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader/LAB-5/plans/reports/

Mọi đường dẫn dưới đây tính từ `AIDD Leader/`.

## Đọc trước (chỉ những file này)
1. `LAB-5/work/issue-todo/M5.md` — danh sách 35 mục, mỗi dòng một file.
2. `LAB-5/work/issue-template.md` — khuôn và 8 luật bắt buộc. Đọc kỹ, đây là tiêu chí nghiệm thu.
3. `LAB-5/work/issues/BL-M3-002.md` — **bài mẫu đã đạt**. Viết theo đúng mức chi tiết này.
4. `LAB-5/Output/PREMISE.md` — tiền đề đã duyệt (§1 thay đổi thiết kế, §6 câu hỏi khách còn mở).
5. Spec của chính epic: `LAB-4/Output/specs/specs/m5-transport-delivery/**`.
6. Lược đồ: `LAB-4/Output/specs/specs/system-design/database-design/**` — tra tên bảng và tên cột.
7. Danh sách API: `LAB-4/Output/specs/specs/_common/api-list.md`.

**Lưu ý riêng epic này — ba mục nhạy cảm, đọc PREMISE trước khi viết:**
- `BL-M5-001` (mốc kết thúc ca): `PREMISE.md` §1.7. Chưa chốt `API-M5-028.completed_at_jst` có đồng nhất với `driver_hours_ledger.shift_end_jst` hay không — **viết đúng trạng thái chưa chốt đó**, đừng tự quyết.
- `BL-M5-003` (khách từ chối nhận toàn bộ) và `BL-M5-004` (làm rõ `CQ32`): `PREMISE.md` §6 mục `CQ32`. Mô hình hiện tại **không biểu diễn được** `shipment_line` số lượng 0. Không tự chọn cách biểu diễn.

## File bạn sở hữu (không chạm file nào khác)
`LAB-5/work/issues/BL-M5-*.md` — đúng 35 file, tên lấy từ cột `backlog_id`.

## Tiêu chí nghiệm thu
- Bốn tiêu đề `## Mục tiêu` · `## Nguồn trường` · `## Định nghĩa xong (DoD)` · `## Tham chiếu` viết **đúng chữ**.
- Cột `Loại nguồn` chỉ nhận: `cột DB` · `công thức` · `sự kiện` · `trạng thái cục bộ` · `API ngoài`.
- Mọi tham chiếu `cột DB` phải là `bảng.cột` **có thật** trong thư mục `database-design/`. Bộ kiểm máy sẽ tra lại từng cặp — **tên bịa sẽ bị bắt**. Không tra được thì đừng điền, hãy ghi vào `## Tham chiếu` là chỗ hở.
- Bảng markdown mọi dòng cùng số ô; dấu `|` trong backtick viết `\|`.
- **Không chép nguyên văn RFP** (≥20 từ tiếng Việt hoặc ≥40 ký tự tiếng Nhật liền là lỗi chặn — tài liệu này lên repo public). Dẫn bằng `RFP p.NN` + mã ID.
- DoD mỗi gạch đầu dòng là một thứ **quan sát được**. Cấm "hoạt động đúng", "đã xong".
- Issue gắn nhãn `state-change`: DoD phải có dòng đòi **sự kiện riêng** cho mỗi thực thể bị loại / đổi trạng thái.
- Viết **tiếng Việt**, câu ngắn, tự nhiên, không lạm dụng từ tiếng Anh. Không nhắc tới AI.
- Sub-issue (`type = sub-issue`) viết gọn hơn issue cha, chỉ phần lớp mình (BE / FE / test), nhưng vẫn đủ bốn mục.

## Bắt buộc báo cáo lại
Liệt kê rõ: (a) mọi giá trị bạn **đoán hoặc giả định** vì spec không nói; (b) mọi chỗ **mơ hồ** mà spec nói hai kiểu; (c) mọi trường bạn **không tra được nguồn**. Đây là dữ liệu cho gate, không phải phần phụ.

Kết thúc bằng đúng một dòng: `Status: DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT`
````

</details>

**Output chính:** 188 file mô tả issue, mỗi file bốn mục bắt buộc.

**Quyết định — nhận, sau khi bộ kiểm máy tra lại từng cặp `bảng.cột`.** Prompt nói thẳng
*"bộ kiểm máy sẽ tra lại từng cặp — tên bịa sẽ bị bắt"*, và điều đó cần thiết: có agent đã
viết `cột x của bảng y` để lách luật kiểm khi cột chưa tồn tại. Hai agent khác gặp đúng chỗ
đó thì **tự khai** thay vì lách — nhờ vậy tôi biết luật kiểm của mình thiếu cách diễn đạt
"cột sẽ được tạo ở mục khác" và đã thêm (`ai-log`, họ lỗi "luật kiểm tạo ra động cơ xấu").
### 1.6 Đọc mù — đo xem mô tả có đủ để làm không

**Giao cho:** `general-purpose`, model `sonnet` · **Khi nào:** sau mỗi vòng viết mô tả; tổng 15 lượt trên 15 mục lấy mẫu

<details><summary>Prompt nguyên văn (1409 ký tự) — bấm để mở</summary>

````
Bạn là lập trình viên mới vào dự án, sắp code bốn việc dưới đây. **Đọc mù**: bạn CHỈ được đọc bốn file mô tả issue, không đọc gì khác.

Thư mục gốc: `/Users/pham.trong.nghia/Desktop/00_Nghia/01_Takumi/02_Other PJs/AIDD Leader/`

## Chỉ đọc đúng bốn file này
- `LAB-5/work/issues/BL-M7-207.md`
- `LAB-5/work/issues/BL-M4-150.md`
- `LAB-5/work/issues/BL-M9-180.md`
- `LAB-5/work/issues/BL-M7-208.md`

**TUYỆT ĐỐI KHÔNG** mở spec LAB-4 (`LAB-4/...`), không mở `Requirements/`, không mở `backlog.csv`, không mở file mô tả nào khác, không grep ra ngoài bốn file trên. Mục đích của việc đọc mù là đo xem **bản thân mô tả** có đủ để bắt tay vào làm hay không. Mở thêm tài liệu là làm hỏng phép đo.

## Việc của bạn
Với **từng file**, trả lời:
1. **Hiểu được gì:** một hai câu nói việc này là gì, ai dùng, làm xong thì cái gì chạy.
2. **Câu phải hỏi trước khi code:** liệt kê từng câu. Mỗi câu là một thứ mô tả **không nói**, mà thiếu nó thì bạn không viết được dòng code đầu tiên, hoặc sẽ phải đoán. Ghi rõ nó chặn phần nào.
3. **Chỗ mô tả tự mâu thuẫn** hoặc nói nước đôi, nếu có.
4. **DoD có kiểm được không:** chỉ ra gạch đầu dòng nào bạn **không biết cách kiểm** vì nó mơ hồ.
5. **Đánh giá:** `ĐỦ ĐỂ LÀM` · `LÀM ĐƯỢC NHƯNG PHẢI ĐOÁN` · `KHÔNG LÀM ĐƯỢC`, kèm một câu vì sao.

Đừng khen, đừng tóm tắt lại nội dung file. Chỉ nêu chỗ thiếu. Viết **tiếng Việt**, câu ngắn.

Kết thúc bằng đúng một dòng: `Status: DONE`
````

</details>

**Output chính:** với mỗi mục, một đánh giá `ĐỦ ĐỂ LÀM` · `LÀM ĐƯỢC NHƯNG PHẢI ĐOÁN` ·
`KHÔNG LÀM ĐƯỢC` kèm danh sách câu phải hỏi trước khi code.

**Quyết định — nhận, và đây là phép đo hữu ích nhất của cả bài.** Vòng đầu: **9 KHÔNG LÀM ĐƯỢC
· 6 PHẢI ĐOÁN · 0 ĐỦ** — trong khi bộ kiểm máy của tôi đã xanh 7/7. Hai phép đo nói hai điều
khác nhau, và phép đo đúng là phép đo của người đọc. Sau khi rót nội dung tham chiếu vào mô tả
(hướng C): **6 KHÔNG · 9 ĐOÁN · 0 ĐỦ**.

Vòng ba, trước khi nộp, đổi hai thứ: mỗi mục thêm khối **"Bắt đầu từ đâu"** tách *làm được ngay*
khỏi *chờ*, và lược đồ phản hồi của từng API đưa vào bình luận của mục. Đo lại trên 8 mục khó
nhất, chấm theo phần *làm được ngay*: **0 KHÔNG · 6 ĐOÁN · 2 ĐỦ**. Lần đầu có mục đạt `ĐỦ ĐỂ LÀM`.

Phần `PHẢI ĐOÁN` còn lại **không** phải chép thiếu, mà là **thiết kế chưa có**: biểu thức CHECK,
thuật toán `etag`, thứ tự kiểm lỗi, ma trận vai × trạng thái. Ghi vào `task-breakdown.md` §4.12
chứ không bịa vào mô tả — bịa thì người đọc mù chấm `ĐỦ`, còn người dựng thật dựng sai.
### 1.7 Review chéo độc lập

**Giao cho:** `codex:codex-rescue`, model `(mặc định)` · **Khi nào:** sau mỗi bản PREMISE; tổng 6 vòng

<details><summary>Prompt nguyên văn (3552 ký tự) — bấm để mở</summary>

````
review LAB-5/Output/PREMISE.md, LAB-5/work/evidence/premise-evidence.md và LAB-5/work/errata-draft.json --fresh

Đây là phép thử của một phương pháp, không chỉ là review một tài liệu. Xin đọc kỹ phần bối cảnh.

## Bối cảnh

Bốn bản PREMISE trước đều mắc cùng một họ lỗi: khẳng định về nguồn mà chưa đọc đủ nguồn. Ví dụ thật đã xảy ra:
- đề xuất bỏ một tiêu chí mà RFP p.22 FR-INV-02 bắt buộc;
- "sửa" một mã vị trí vốn có trong RFP p.77;
- viết "RFP không có luồng trả hàng" trong khi RFP p.30 FR-OUT-07 có;
- bịa rằng "stock unit" chính là bản ghi lô, trong khi RFP có mục từ điển riêng;
- bịa một vai `warehouse_admin` không tồn tại, trong khi câu trả lời nằm giữa một dòng dài bị cắt mất khi hiển thị;
- bắt sinh 3 dòng sổ giờ vì enum có 3 giá trị, trong khi Technical Design ghi rõ một dòng.

Chẩn đoán: truy vấn mang tính xác nhận và hẹp, nên dừng ngay khi đủ xác nhận. Bốn luật đã đặt ra (LAB-5/LAB-5 Planning.md §2.4):
- **L1** tách đọc khỏi kết luận — subagent đọc với prompt mở, cấm kết luận, trả stdout nguyên văn; người viết chỉ kết luận từ thứ agent mang về;
- **L2** trước khi đề xuất bất kỳ thứ gì mới, bắt buộc truy vấn "nguồn đã có lời giải chưa";
- **L3** đọc trọn đơn vị, cấm cut/head/tail trong lệnh, trích dẫn có lược phải kèm số dòng và trỏ bằng chứng gốc;
- **L4** câu hỏi phải mở ("nguồn nói gì về X"), cấm dạng "X có đúng không".

Bản 5 là bản đầu tiên làm theo bốn luật đó.

## Bằng chứng có thể kiểm chứng độc lập

`LAB-5/work/evidence/raw/` có 4 file, tổng ~5,7 MB, là stdout **không cắt** của 4 subagent đọc nguồn, mỗi lệnh kèm mã thoát và đường dẫn đầy đủ. `premise-evidence.md` có 15 mục EV-01..EV-15 chắt từ đó. Mỗi dòng §1/§2 của PREMISE trỏ một mã EV.

## Xin kiểm theo thứ tự này

1. **Lấy mẫu ít nhất 6 mục EV và TỰ CHẠY LẠI lệnh.** Kết quả có khớp thứ EV ghi không? Đây là phép kiểm quan trọng nhất.
2. **Kết luận của mỗi EV có vượt quá bằng chứng của chính nó không?** Một EV ghi đúng lệnh, đúng kết quả, nhưng kết luận rộng hơn thì vẫn là lỗi cũ khoác áo mới.
3. **Còn khẳng định nào trái RFP hoặc SSOT không?** Tra theo trang và mã, đừng dừng ở chương đầu tìm thấy.
4. **Ba chỗ bản 5 đảo chiều so với bản 4 — đúng hay sai?**
   (a) §3.2: giữ `subtype: technical-design`, không sửa về `detail-design`, lý do là `quy-chuan-thu-muc-spec-github.md:224, :311` nói bộ chuẩn đã đổi tên ở 0.19.0 và việc còn nợ là đổi tên khuôn (ISS-67).
   (b) §1.8: từ chối giao toàn phần thì không chèn dòng `shipment_line` nào, lý do `M4_DB:739` có `line_kind` enum `delivered · reversal` và `API-M5-026:118` chèn một dòng mỗi lô giao.
   (c) §1.1: `stock_unit` có đặc tả ở tầng hành vi, chỉ thiếu bảng trong Database Design.
5. **Ba câu hỏi khách đã rút (§6.1) — rút có đúng không?** `CQ29` (thiếu trường truy xuất: chặn hay cách ly), `CQ33` (chỗ lưu số lượng khi từ chối), `CQ34` (vai sở hữu dữ liệu chủ vị trí).
6. **Còn khẳng định nào nấp ngoài §1/§2 mà không có EV không** (§3 hướng đã chốt, §4 góp ý cần đọc lại, §5 errata)?
7. **Errata E11 có an toàn áp không**, và cách áp ghi ở §5 có đúng không?

## Điều tôi cần nhất

Các vòng trước tìm được 5 đến 12 lỗi chặn mỗi vòng. Nếu vòng này ít hơn, xin nói rõ **vì sao** — do tài liệu thật sự tốt hơn, hay do bạn đang kiểm dễ hơn. Nếu bản 5 vẫn mắc cùng họ lỗi thì nói thẳng; đó là thông tin có giá trị nhất, vì nó cho biết bốn luật kia chưa đủ.

Nền để đọc: CLAUDE.md mục 8 (luật C1–C10); SSOT là `Requirements/`; RFP bản JA ở `Requirements/01_rfp/ja/` có mốc trang `<!-- p.NN -->`; bộ thiết kế LAB-4 ở `LAB-4/Output/specs/` đã đóng băng; bộ khung đề phát ở `LAB-4/spec-standard/`.
````

</details>

**Output chính:** danh sách phát hiện có trích dẫn, mỗi phát hiện một mức nghiêm trọng.

**Bảng nhận / bác theo từng vòng** — C9 đòi reviewer chéo phải có bảng này:

| Vòng | Phát hiện chính | Nhận / Bác | Lý do | Đã sửa ở đâu |
|---|---|---|---|---|
| PREMISE bản 1–4 | Khẳng định về nguồn mà chưa đọc đủ nguồn (6 ví dụ) | **Nhận** | Kiểm lại từng dẫn chiếu, Codex đúng cả sáu | Đổi cách làm: bằng chứng trước, khẳng định sau (`ai-log` #66, #67) |
| bản 2–4 | Token `gh` không hợp lệ | **Bác hai lần** | `gh api user` trả `nghiapt-1099`, mã thoát 0 | Không sửa; ghi lý do bác vào `ai-log` |
| bản 5 | 8 lỗi lớp suy luận (nguồn nói A, kết luận là B) | **Nhận** | Đọc lại từng dòng, cả tám đều vượt quá nguồn | Thêm ba luật L5, L6, L7 vào `LAB-5 Planning.md` §2.4.1 |
| bản 6 | 5 lỗi chặn | **Nhận** | Có trích dẫn dòng cụ thể | Vào thẳng PREMISE bản 7 |
| bản 6 | 3 phát hiện về luồng cất hàng | **Nhận** | Bốn nguồn của chính LAB-4 đều nói tách | PREMISE §1.4, errata `E11` |
| trước nộp (G7) | 26 phát hiện, 13 CHẶN | **Nhận 24, bác 2** | Bác: "`SETUP` nên tách theo capability" (không đủ dữ liệu để chia có căn cứ → chuyển thành trình hai kịch bản) và "đọc mù phải đạt ĐỦ mới được chốt" (giữ, nhưng chấm theo phần *làm được ngay*) | `task-breakdown.md` §2, §3 · `PREMISE.md` §3 · `estimate.md` §3.2, §5.2 · `README.md` §5 · `ai-log` #217–#219 |

**Quyết định — nhận phần lớn, bác có lý do phần còn lại.** Ví dụ bác: Codex báo token `gh`
không hợp lệ; tôi chạy `gh api user` trả `nghiapt-1099`, mã thoát 0 → **bác hai lần**. Ví dụ
nhận: 5 phát hiện ở bản 6 và 3 phát hiện về luồng cất hàng, đều vào thẳng bản 7. Sổ lỗi có
**ít nhất 60 dòng** ghi nguồn phát hiện là Codex.
### 1.8 Chấm SWP — công cụ, không phải prompt tự do

Dùng skill `tkm:estimate-swp` với nguồn là **chính bảng công việc** (`https://github.com/users/nghiapt-1099/projects/1`).
Skill có hợp đồng riêng: tiền kiểm từng lệnh, bộ chọn mục tất định, bộ chấm, bộ hiệu chỉnh,
bộ dựng báo cáo HTML. Tôi không viết prompt cho nó mà **chạy đúng hợp đồng của nó**.

Một chỗ phải hạ cấp, ghi rõ: skill mặc định chạy mức `high` — có **người chấm độc lập thứ hai**
để lộ ra chỗ hai bên chấm lệch nhau. Gói bằng chứng của 172 mục quá lớn để đưa qua prompt
(330 nghìn token; rút gọn bằng máy còn 66 nghìn vẫn không giao được vì harness cắt output lớn
thành file). Người duyệt chọn **hạ xuống mức `medium`, session chính tự chấm**. Hệ quả — mất
người chấm thứ hai — ghi thành caveat trong báo cáo HTML và trong `estimate.md` §2.1, không giấu.

### 1.9 Ghi SWP lên bảng công việc bằng Agent

Đề yêu cầu *"dùng AI Agent ghi SWP đã chốt vào trường point"*. Prompt nguyên văn và ranh giới:

<details><summary>Ghi chú nguyên văn — bấm để mở</summary>

````
# Lời giao việc nguyên văn — ghi SWP đã chốt lên trường `Point` của GitHub Project

> **Nói rõ trước:** bước này **không giao cho subagent**. Nó là thao tác **ghi** lên tài khoản
> thật của người dùng, mà `CLAUDE.md` §5 bắt thao tác đụng dữ liệu dùng chung phải do session
> chính chạy và báo rõ. Vì vậy "prompt" ở đây là **lời giao việc session chính tự nhận và làm
> theo**, chép nguyên văn, kèm lệnh đã chạy. Không bịa ra một prompt subagent không tồn tại.

**Loại việc:** ghi dữ liệu lên bảng công việc thật.
**Ai chạy:** session chính (Opus), bằng script `work/push-points.py`.

## Lời giao việc

```
Ghi SWP đã chốt lên trường `Point` của Project 1, cho cả 201 mục.

Nguồn số: `work/swp/payload-cal.json`, trường `finalSWP` của từng mục — bản SAU khi người
duyệt kiểm 5 mục mẫu và hiệu chỉnh đã lan theo tầng. Không lấy `initialSWP`.

Luật:
- Mục cha lấy `finalSWP` do validator tính = tổng con. Không chấm lại mục cha.
- Mục nào đã có `Point` đúng giá trị thì bỏ qua, không ghi đè.
- Ghi xong PHẢI đọc ngược từ Project để đối chiếu. Mã thoát 0 của lệnh ghi không phải
  bằng chứng đã ghi đúng.
- Không đụng vào trường nào khác.

Ranh giới: skill `tkm:estimate-swp` tự khai read-only với mọi tracker — nó cấm tạo hay sửa
trường, mục, issue. Việc ghi `Point` vì thế NẰM NGOÀI skill, là bước riêng của LAB-5 theo
`LAB-5 Planning.md` §B5. Skill chỉ sinh báo cáo; script này mới ghi.
```

## Lệnh đã chạy

```bash
python3 work/push-points.py                 # ghi 201 mục
python3 work/checks/check_swp.py            # đọc ngược từ board, đối chiếu
```

## Kết quả

`push-points.py` → `đã ghi 201 mục`, mã thoát 0.
`check_swp.py` → **XANH 5/5**: 201/201 `Point` khớp `finalSWP` · 29/29 mục cha bằng tổng con ·
0 mục `needs-breakdown` · tổng trên board 2787 (1245 mục lá + 1542 mục cha).

## Quyết định nhận / bác

**Nhận kết quả**, vì đã đọc ngược từ chính bảng công việc chứ không tin mã thoát. Một chỗ phải
sửa giữa chừng: `gh api graphql -F` chỉ ép được số nguyên và boolean, gặp số thập phân thì gửi
thành chuỗi và máy chủ từ chối — ghi ở `work/ai-log.md` #196 và #204.
````

</details>

**Điểm đáng nói:** skill chấm điểm tự khai **read-only** với mọi tracker. Ghi `Point` vì thế
**nằm ngoài skill**, phải tách thành script riêng chạy sau. Làm trong skill là phá chính ranh
giới nó tuyên bố. Cùng cách đó, `Est. hours` ghi bằng `work/push-hours.py`, trạng thái và
Iteration ghi bằng `work/push-sprint.py` — cả ba đều do session chính chạy, và **đều đọc ngược
từ bảng công việc để đối chiếu**, không tin mã thoát của lệnh ghi.

### 1.10 Quy giờ và báo cáo tiến độ — không giao AI

Hai việc này **không có prompt nào**, vì chúng tất định:

- **Quy giờ:** một hàm thuần trong `work/swp/hours-rule.py`, hệ số 6,4 giờ/điểm chia ra từ hai
  con số có nguồn. Giao cho AI sẽ ra một con số không tra ngược được.
- **Báo cáo tiến độ:** `work/sprint/gen-progress.py` đọc bảng công việc qua API rồi ghép bảng.
  Phần nhận định (burndown lệch ở đâu, vì sao) là của người viết, nhưng mọi con số trong đó
  do script cộng từ board.

## 2. Soát output của AI

### 2.1 Người soát ở những bước nào

| Bước | Người làm gì |
|---|---|
| Mỗi gate `G0`…`G7` | Người duyệt đọc bằng chứng rồi nói "Duyệt". **Không gộp gate** — 8 gate, 8 lần dừng |
| Sau mỗi output của subagent | Session chính (Opus) soát **trước khi** trình gate |
| Chấm SWP | Người duyệt kiểm **5 mục mẫu**, hai lượt mỗi mục (mức khó trước, số điểm sau) |
| Quy giờ | Người duyệt duyệt **quy tắc** trước khi áp cho toàn backlog |
| Kịch bản sprint | Người duyệt duyệt **trước khi** đụng vào bảng công việc public |

### 2.2 Phát hiện gì

Sổ lỗi 223 dòng. Phân loại theo từ khoá ở cột *"Phát hiện bằng gì"*:

| Phát hiện bằng | Số dòng |
|---|---:|
| Review chéo Codex | ít nhất 62 |
| Bộ kiểm máy | ít nhất 20 |
| Đọc mù | ít nhất 18 |
| Chính agent tự khai | ít nhất 16 |
| Người duyệt | ít nhất 12 |

*Khoảng 95 dòng còn lại không khớp từ khoá nào — phần lớn là "session chính đọc lại nguồn rồi
thấy sai", cộng vài dòng bị lệch cột vì dấu `|` nằm trong backtick. Nên các con số trên là
**cận dưới**, không phải thống kê chính xác.*

### 2.3 Ba lỗi đáng kể nhất, và cách xử lý

**(a) Tôi tự làm hỏng một dòng đã được duyệt.** PREMISE §1.3 tính "phần chưa cất" từ tồn kho
hiện tại — sai, vì tồn hiện tại giảm khi xuất hàng. Bản 6 tính theo movement là đúng; **tôi
đổi ở bản 7 và làm hỏng**, rồi thu gọn §1.5 bằng chính lý do sai đó. Sáu vòng Codex và 11 lô
agent đều không thấy. Người phát hiện: một lượt **đọc mù**. Sửa: trả công thức về movement,
thêm hai ca test bắt đúng lỗi này, ghi cả chuyện "đây là sửa một dòng đã duyệt" vào
`task-breakdown.md` §4.9.

**(b) Luật chấm điểm bật nhầm cờ.** Mục viết DDL bị cộng độ khó vì bảng nguồn trường có **một
dòng** loại *API ngoài* — trong khi mục đó không gọi hệ thống nào. Người duyệt bắt khi kiểm mục
mẫu lớn nhất. Sửa bằng luật lan chỉ áp cho tầng trùng **đủ sáu chữ ký**, không áp độ lệch chung.

**(c) Bộ kiểm kiểm chính giả định của nó.** Ba lần, cùng một họ: bộ kiểm đếm bằng một biểu thức
khác biểu thức đã kiểm (#201); bộ kiểm đọc dấu chấm trong khi bảng đã đổi sang dấu phẩy (#205);
bộ kiểm so trạng thái với tập `{Done, In progress, Todo}` **do tôi gõ ra**, còn bảng công việc
ghi `In Progress` chữ P hoa (#209). Lần thứ ba suýt ghi hỏng board public. Cách sửa chung:
**bộ kiểm phải đọc điều kiện từ nguồn thật**, không gõ sẵn thứ mình tưởng.

### 2.4 Chỗ phương pháp còn yếu — tự khai

- **Năm mục mẫu là năm.** Ba luật hiệu chỉnh chạm 25 mục; **176 mục còn lại chưa ai soi bằng mắt**.
- **Không có người chấm độc lập thứ hai** (chạy mức `medium`), nên không chỗ nào lộ ra hai bên chấm lệch.
- **Chưa mục nào đạt `ĐỦ ĐỂ LÀM`** ở phép đọc mù, kể cả sau vòng rót nội dung.
- **Tổng giờ khớp ước lượng LAB-1 là do đặt cho khớp**, không phải phát hiện (`estimate.md` §3.3).

## 3. Điểm đặc biệt trong cách dùng AI

### 3.1 Chỗ tin là hiệu quả

**Đỏ trước, xanh sau — áp cho cả tài liệu, không riêng code.** Mỗi bước viết bộ kiểm **trước**
khi có dữ liệu, chạy cho nó đỏ, rồi mới làm. 21 vòng kiểm, không vòng nào ghi đè vòng trước.
Nhờ vậy "xong" là một mã thoát, không phải một câu tự nhận.

**Đọc mù là phép đo đắt nhất và đáng nhất.** Nó đo thứ bộ kiểm máy không đo được: *một người
chưa biết gì về dự án có làm được việc này không*. Chính nó bắt lỗi công thức mà sáu vòng
review chéo bỏ qua. Bài học: bộ kiểm hình thức xanh **không** có nghĩa là tài liệu dùng được.

**Việc tất định thì viết script.** Mọi con số trong bộ nộp đều do script cộng từ một nguồn duy
nhất; bảng trong tài liệu do máy ghép. Nhờ vậy khi đổi khuôn số sang dấu phẩy kiểu Việt, bộ
kiểm đỏ ngay thay vì để lọt một bảng gõ tay.

**Đọc ngược sau mỗi lần ghi.** Không bước nào tin mã thoát của lệnh ghi. 201 trường `Point`,
201 trường `Est. hours`, 17 mục đổi trạng thái — đều đọc lại từ bảng công việc rồi mới báo xong.

### 3.2 Chỗ chưa tốt, làm thật thì phải khác

**Bộ kiểm của tôi hay mô tả hình thức muốn thấy thay vì điều kiện cần kiểm.** Ba lần trong một
bài là nhiều. Làm thật thì mỗi luật kiểm nên bắt đầu bằng câu hỏi *"điều kiện này đọc từ đâu
ra"* — nếu câu trả lời là "tôi gõ ra" thì luật đó đáng ngờ.

**Luật kiểm có thể tạo ra động cơ xấu.** Một agent đã **cố tình tránh viết đúng cụm từ** mà
bộ kiểm hai chiều của tôi tìm, và nói thẳng ra như vậy. Luật kiểm càng cứng về chữ thì càng
dễ bị đi vòng. Làm thật thì phải kiểm cái *có* trong tài liệu, không kiểm cái *viết thế nào*.

**Giao việc nặng cho subagent gặp trần cửa sổ.** Gói bằng chứng 172 mục không lọt prompt, nên
mất người chấm thứ hai. Làm thật thì phải tính kích thước gói **trước** khi chọn mức chạy, và
chuẩn bị đường lùi (chia nhỏ lô, hoặc để worker tự đọc file thay vì nhận gói qua prompt).

**Backlog chia chưa đều.** Epic `M10` chỉ bằng một nửa ước lượng cũ vì thiếu hẳn mục cho script
chuyển đổi và đối soát. Phát hiện này đến từ **phép so với ước lượng LAB-1** — tức bước cuối.
Làm thật thì phép so đó phải chạy **ngay sau khi chia xong**, như một bộ kiểm, không để tới lúc
viết báo cáo.

### 3.3 Chỗ chưa có ý tưởng

**Làm sao biết một mô tả issue là đủ mà không cần người đọc.** Đọc mù tốn một lượt agent cho
mỗi vài mục; 188 mục thì không kham nổi. Tôi lấy mẫu 15 mục, nhưng không có cách nào biết
15 mục đó đại diện cho 173 mục còn lại.

**Làm sao bắt lỗi ở lớp suy luận bằng máy.** Lỗi công thức §1.3 không phải lỗi hình thức: mọi
trích dẫn đều đúng, mọi bảng đều liền mạch, mọi tên cột đều có thật. Cái sai nằm ở bước *suy
từ nguồn ra kết luận*. Ba luật L5–L7 là cách tôi xử lý bằng tay; chưa có bộ kiểm máy nào kiểm
được chúng.

**Hệ số quy giờ vẫn là một con số duy nhất cho mọi loại việc.** Phép so §5 cho thấy nó lệch tới
±60 % theo module. Tôi chưa có cách đặt hệ số theo loại việc mà không rơi vào chỗ tự chọn con số.
