# Tongjiang Lab — bản source tùy chỉnh được (bài đầu tiên: địa hình sông)

Bài đầu tiên được chọn: **river-step — Sự phát triển của địa hình sông**,
bài Địa lí đầu tiên trong danh sách của repo gốc. Lý do chọn nó làm mẫu:
cấu trúc của nó là cấu trúc chung của cả họ bài học (content + world +
model + console), và cảnh địa hình là dạng cảnh dễ kế thừa nhất cho các
bài Địa lí khác (karst, kiến tạo, chu trình nước...).

## Nguồn và giấy phép — đọc trước khi dùng

- Nguồn: https://github.com/smithjohn78390/tongjiang-lab
- Tác giả gốc: Tongjiang Lab (https://lab.tongjiang.app)
- Giấy phép nội dung (bài giảng, bìa, catalog): **CC BY-NC 4.0** —
  được dùng, chia sẻ và chỉnh sửa cho mục đích **phi thương mại**, với
  điều kiện ghi nguồn "Tongjiang Lab (https://lab.tongjiang.app)" và
  ghi rõ đã sửa đổi. Xem `LICENSE-NOTE.md`.
- three.js đi kèm trong engine giữ giấy phép **MIT** riêng.
- Bản này là **bản chuyển đổi cho mục đích học tập / dùng nội bộ,
  phi thương mại**. Không dùng cho sản phẩm thương mại khi chưa có
  giấy phép khác từ tác giả gốc.

## Phát hiện quan trọng về file gốc

File gốc `courses/river-step.html` (~909 KB) trông như một file duy nhất,
nhưng thực chất gồm 2 phần nối trong cùng một `<script type="module">`:

1. **Engine (khoảng 840 KB, dòng 10 → giữa dòng cuối):** three.js +
   engine giao diện (bảng điều khiển, bước học, kiểm tra, `window.__lab`).
   Tên biến đã bị minify, tên hàm entry là `Zp`.
2. **Bài học (khoảng 68 KB, cuối file):** chỉ 4 khối, và đây là toàn bộ
   thứ cần sửa để tùy chỉnh một bài:
   - `th` — nội dung (content): **dữ liệu thuần, không có hàm**.
   - `cd` — world: giao diện engine cần (`CAMERA`, `LAYERS`, `NORTH`,
     `TONE`, `build`).
   - `od` — model: giao diện engine cần (`CONST`, `GEO`, `alongStream`,
     `bankVectors`, ...), kèm hằng số `pi` và các hàm địa hình.
   - `cm` — console: slider, switch, biểu đồ, bài kiểm tra cuối bài.
     Nhãn của console lấy từ `th.panel`, nên đổi nhãn thì sửa `th`,
     không sửa `cm`, trừ khi đổi thang đo / điều kiện đạt.

Hệ quả: tùy chỉnh bài = sửa content + world/model + console, rồi build
lại. Engine vốn là hộp đen, **nhưng đã phải sửa một lần để Việt hoá
giao diện chung** (30/10): bản tiếng Việt ở `engine.js`, bản gốc giữ
nguyên ở `engine.zh.js`, dịch lại bằng `tools/translate-engine-vi.mjs`.
Các lần sửa engine sau này (nếu có) phải đi qua script đó và ghi rõ
vào `TRANSLATION.md`, không sửa tay trực tiếp vào `engine.js`.

## Cấu trúc thư mục này

```
tongjiang-vn/
  README.md, LICENSE-NOTE.md, catalog.vn.json
  src/shared/
    engine.js     # engine của river, nay là BẢN TIẾNG VIỆT (dịch 30/10,
                  # tools/translate-engine-vi.mjs). Bản gốc: engine.zh.js, không sửa.
    shell.html    # khung HTML: <canvas id="gl"> + chỗ chèn script. Tiêu đề lấy từ content.json.
  src/lessons/river/
    content.json      # th, BẢN TIẾNG VIỆT đầy đủ (238 chuỗi, 30/10).
                      # Sửa bài ở đây là chính. Bản gốc: content.zh.json.
                      # Dịch lại bằng tools/translate-content-vi.mjs.
    world-model.js    # cd + od + pi + ie + hàm mô hình, đã format bằng prettier,
                      # nhãn 3D đã dịch sang tiếng Việt. Sửa tham số ở đây.
    console.js        # je + cm + lời gọi Zp(...). Chỉ sửa khi đổi thang đo / kiểm tra.
  src/lessons/_template/
    README.md, content.schema.md   # mẫu và schema để làm bài mới
  tools/
    build.mjs           # nối source -> dist/*.html, kèm kiểm tra cú pháp
    extract-lesson.mjs  # tách một bài gốc khác thành cùng cấu trúc này
    new-lesson.mjs      # copy river làm mẫu cho bài tương tự
  dist/
    river-step.html     # bản build ra, mở trực tiếp bằng trình duyệt, chạy offline
```

Các file `world-model.js` / `console.js` là **fragment** (mảnh code),
không chạy độc lập — chúng dùng chung scope với engine trong file build.
Đừng mở chúng như một trang web riêng; luôn chạy qua `tools/build.mjs`.

## Cách build và chạy

```bash
node tools/build.mjs
# -> dist/river-step.html, kèm kiểm tra cú pháp (node --check)
```

Mở file `dist/river-step.html` trực tiếp bằng Chrome/Edge (double-click
là chạy) — **không cần server**. Bản build không gọi mạng ngoài
(không fetch, không XHR, không script CDN) — giống file gốc, mở offline
được trên máy chiếu / bảng tương tác. Quyết định ngày 30/10 của Danh:
sau khi làm xong chỉ build thành HTML, không chạy server để kiểm tra.

## Cách tùy chỉnh cho chương trình THPT Việt Nam

### Mức 1 — Đổi chữ, không đổi cảnh (làm trước, rủi ro thấp nhất)

Sửa `src/lessons/river/content.json`:

- `meta.title`, `top.title`, `top.total`
- `mainline.q` và `mainline.chain[]` (mạch chính của bài)
- `units[]` (4 đơn vị địa hình), `steps[]` (8 bước, mỗi bước có
  `tag`, `q`, `act`, `expect`, `points`, `concl`, `exam`, `ref`...)
- `panel.*` (nhãn bảng điều khiển: tên slider, tên trạng thái, tên bài
  kiểm tra...), `quiz.list[]` (5 câu trắc nghiệm), `help`, `foot`
- `layers.*` (tên lớp hiển thị)

Sau đó chạy lại `node tools/build.mjs`. Không chạm engine, không chạm
world-model, trừ khi cần đổi nhãn 3D (xem mức 2).

`content.json` nay **đã là bản tiếng Việt đầy đủ** (30/10/2026): 238
chuỗi đã dịch — tiêu đề, mạch chính, 8 bước, panel, 5 câu quiz, trợ
giúp, chân trang — và nhãn 3D trong `world-model.js` cũng đã dịch.
Chi tiết thuật ngữ, quyết định dịch và phần chưa dịch xem
`TRANSLATION.md`. Bản gốc tiếng Trung giữ ở `content.zh.json`,
dịch lại bằng `node tools/translate-content-vi.mjs`.

Lưu ý còn lại: `textbook_vn` đang để trống vì **chưa đối chiếu SGK
Địa lí GDPT 2018**. Số trang "sách gốc tr.33–37" trong bài là của sách
PEP Trung Quốc. Cần giáo viên điền chương/bài tương ứng (KNTT / CTST /
CD) trước khi dùng chính thức.

### Mức 2 — Đổi tham số mô hình và nhãn 3D

Sửa `src/lessons/river/world-model.js`:

- `pi`: hằng số vật lí/thủy văn (`G`, `RHO_W`, `RHO_S`, `MANNING_N`,
  `SHIELDS_CRIT`, `FLOOD`...).
- `ie`: hình học thung lũng (`XS/XE`, `DEPTH`, `WIDTH`, `RATIO`,
  `MEANDER_A`...). Đây là chỗ đổi "dáng sông" mà không viết lại cảnh.
- Nhãn 3D (nay là chữ thật): nguồn, biển, bờ lõm/lồi, quạt bồi tích,
  làng trên bậc thềm, bãi bồi... — tìm trực tiếp bằng chữ trong file.

### Mức 3 — Đổi luật kiểm tra

Sửa `src/lessons/river/console.js`, khối `exam`: `ok` (điều kiện đạt),
`hold` (giữ bao lâu, hiện 15), `score` (hiện 15), `solve` (đáp án mẫu
mà nút gợi ý dùng), và `sliders` (min/max/step/ref). Nhãn của chúng vẫn
nằm ở `content.json` → `panel`, đừng sửa nhãn ở đây.

### Lưu ý về ví dụ gốc

Ví dụ dẫn dắt gốc là sông Trường Giang đoạn Trấn Giang (Trung Quốc).
Giữ nguyên để dạy quy luật chung là an toàn nhất. Nếu muốn thay bằng
sông Việt Nam (ví dụ sông Hồng, sông Tiền), đó không còn là "đổi chữ":
phải sửa cả mô hình 3D, các bước và bài kiểm tra cho khớp ví dụ mới,
rồi kiểm chứng lại toàn bộ — xem mức 2 và mức 3.

## Làm bài mới — "kế thừa" nghĩa là gì ở đây

"Kế thừa" trong dự án này có 3 tầng, và cần nói rõ tầng nào là thật:

1. **Kế thừa cấu trúc và quy trình (luôn đúng):** mọi bài đều dùng
   cùng khung `content.json + world-model.js + console.js + build.mjs`,
   cùng schema `th` (meta/top/mainline/units/steps/layers/panel/quiz/
   help/foot/ui). Bài mới chỉ cần điền đúng schema này.
2. **Kế thừa engine và cảnh cho bài TƯƠNG TỰ (đúng với họ địa hình):**
   copy thư mục river làm mẫu, giữ engine, sửa content + tham số
   `pi`/`ie` + nhãn 3D:
   ```bash
   node tools/new-lesson.mjs <slug-moi> "<tieu-de>"
   node tools/build.mjs --lesson <slug-moi>
   ```
3. **Không kế thừa nhị phân engine giữa các bài gốc khác nhau:**
   mỗi bài gốc được minify riêng, tên entry và tên biến nội bộ khác
   nhau (river: `Zp`, karst: `Lp`, ...). Vì vậy, để port một bài gốc
   khác (ví dụ karst), dùng:
   ```bash
   node tools/extract-lesson.mjs <file-html-goc> <slug>
   node tools/build.mjs --lesson <slug> --engine src/lessons/<slug>/engine.js
   ```
   Tool này tách bài đó thành cùng cấu trúc, nhưng giữ engine của
   chính nó. Đừng ép bài karst chạy bằng engine của river khi chưa
   đối chiếu tên hàm nội bộ — đó là lỗi dễ gặp nhất khi port.

Bài khác hẳn về cảnh (hóa học, vật lí) thì chỉ kế thừa tầng 1; cảnh 3D
phải port từ bài gốc tương ứng (tầng 3), không copy cảnh sông.

## Toàn bộ Địa lí 10 KNTT — bài nào tạo được — xem `DANH-SACH-DIA-LI-10-KNTT.md`

Liệt kê đủ 40 bài KNTT Địa lí 10 và đánh giá từng bài: port được 7 bài
(5, 6, 7, 9, 11, 12, 14) + chuyên đề 10.1; dùng lại cảnh viết nội dung mới
2 bài (13 sông Hồng, 18 địa đới); cần cảnh 3D mới 4 bài (4, 8, 15, 17);
không hợp 3D 27 bài (kĩ năng bản đồ/biểu đồ và toàn bộ Địa lí kinh tế –
xã hội, Bài 19–40). Thứ tự nên làm: karst → Bài 9 → Bài 12 → Bài 5 →
Bài 11 → Bài 6 → Bài 14.

## Bài 5 KNTT đã làm hoàn chỉnh — xem `BAI-5-KNTT.md`

Đã làm xong **Bài 5: Hệ quả địa lí các chuyển động của Trái Đất (tr.17–20)**,
cả hai bài gốc: `dist/earth-motion-step.html` ("Ý nghĩa địa lí của chuyển
động Trái Đất", 9 bước, 8 câu quiz) và `dist/insolation-step.html`
("Chuyển động hồi quy của điểm Mặt Trời chiếu thẳng, bốn mùa và năm đới",
9 bước, 9 câu quiz). Dịch đầy đủ sang tiếng Việt (nội dung, nhãn 3D, giao
diện chung, bảng điều khiển, tên mùa/năm đới), đối chiếu từng bước với mục 1
(tự quay, giờ, múi giờ) và mục 2 (quanh Mặt Trời, bốn mùa) của Bài 5. Lưu ý:
bước 8–9 của insolation (năm đới) thuộc **Bài 10**, giữ lại để bài trọn vẹn,
dạy Bài 5 thì bỏ qua. Đã kiểm chứng headless (0 chữ Hán hiển thị, không lỗi
Uncaught), ảnh ở `verification-insolation-vi.png` và
`verification-earth-motion-vi.png`.

Hai lỗi đã sửa khi làm Bài 5: (1) hàm phụ `th` trong world-model của
earth-motion trùng tên biến nội dung → đổi thành `thH`; (2) bước custom
(insolation bước 9, earth-motion bước 4) mất hàm ok/demo khi tách JSON →
tạo `src/lessons/<slug>/act-fns.js`, build.mjs nối ngay sau `var th`,
extract-lesson.mjs nay cảnh báo khi content chứa hàm. Xem `BAI-5-KNTT.md`
mục 5.

## Bài karst (Bài 7, mục 2) đã làm xong 31/10 — xem `KE-HOACH-KNTT-DIA-LI-10.md`

`dist/karst-step.html` ("Địa hình karst hình thành và nhận biết như thế
nào", 7 bước, 8 câu quiz): hoà tan → rãnh → thung lũng (ba tử) → cụm đỉnh,
rừng đỉnh → đỉnh đơn độc, đồi tàn dư → hang động (thạch nhũ ↓, măng đá ↑,
cột đá). Dịch đầy đủ sang tiếng Việt bằng 3 script mới dùng chung cho các
bài sau (`tools/translate-karst-vi.mjs`, `translate-lesson-engine-vi.mjs`,
`translate-lesson-aux-vi.mjs` — xem `TRANSLATION.md`). Đã kiểm chứng
headless (0 chữ Hán hiển thị, không lỗi Uncaught), ảnh ở
`verification-karst-vi.png`. Ví dụ Quảng Tây / Quý Châu / Vân Nam giữ nguyên
như sách gốc; ví dụ Việt Nam (Hạ Long, Phong Nha, Tràng An, Sơn Đoòng) ghi
trong tài liệu đối chiếu.

## Bản thử nghiệm theo SGK KNTT — xem `KE-HOACH-KNTT-DIA-LI-10.md`

Đã làm xong 1 bài thử nghiệm theo **SGK Địa lí 10, Kết nối tri thức, Bài 7:
Nội lực và ngoại lực (tr.24–26)**: `dist/river-kntt-step.html` — giữ 4/8
bước (sa bàn, quạt bồi tích (nón phóng vật), bãi bồi, châu thổ), 4 câu quiz
(trong đó có câu Vận dụng 1 về ĐBSH / ĐBSCL), câu hỏi chính là chính câu
Vận dụng 1 của SGK. Tạo bằng `node tools/make-river-kntt.mjs`, đã kiểm chứng
headless (0 chữ Hán hiển thị, không lỗi Uncaught). Nguồn SGK đối chiếu từ
5 trang giải bài độc lập — chưa đối chiếu với sách giấy, cần giáo viên chốt
thuật ngữ "nón phóng vật" trước khi dùng chính thức.

## Đánh giá cho SGK Việt Nam — xem `DANH-GIA-SGK-VN.md`

Đánh giá lại ngày 30/10/2026 (sau khi dịch xong): Tongjiang là thư viện mô
phỏng trực quan rất tốt cho **Địa lí tự nhiên lớp 10** (14/14 bài Địa lí
khớp khái niệm), nhưng **độ sâu vượt SGK** — bài river là một bài riêng
trong chương trình Trung Quốc, còn ở Việt Nam nó chỉ là một phần của bài
ngoại lực lớp 10 (KNTT Bài 7 / Cánh Diều Bài 6 / CTST Chương 3), và SGK
chỉ yêu cầu "trình bày khái niệm, phân tích sơ đồ", không có số định lượng.
Cách dùng đúng: chọn lọc bước, cắt gọn, đổi ví dụ sang sông Việt Nam, dùng
như trạm thực hành / bài mở rộng — không thay 1:1 cho một bài SGK.

## Trạng thái hiện tại — nói thẳng (cập nhật 31/10/2026)

**Bộ Địa lí 10 KNTT đã xong (17 bài, `catalog.vn.json`):** river,
river-kntt, insolation, earth-motion, karst + 10 bài nhóm A dịch đầy đủ
(tectonic, heating, thermal, wind, circulation, water-cycle, currents,
enso, soil, carbon) + 2 bài nhóm B viết mới (song-hong, dia-doi). Tất cả
đều đã kiểm chứng headless: 0 chữ Hán trong văn bản hiển thị, không lỗi
Uncaught, ảnh `verification-*-vi.png` đã xem. Chữ Trung còn lại chỉ nằm
trong code (thông báo lỗi cho người làm bài và chuỗi riêng của bài khác),
không hiện ra màn hình — xem `TRANSLATION.md`.

- ✅ Dấu tiếng Việt hiển thị đúng trong ảnh chụp headless (font hệ
  thống fallback từ font tiếng Trung sang system-ui vẫn đọc được dấu).
  Trên máy Windows của Danh (Segoe UI) sẽ càng ổn.
- ⚠️ **Chưa đối chiếu sách giấy cho bất kỳ bài nào.** Đối chiếu hiện
  tại dựa vào nguồn giải bài độc lập (xem `NGHIEN-CUU-KNTT-PHAN-2.md`,
  mục 10 liệt kê toàn bộ điểm cần kiểm lại). Đặc biệt: bộ số 12 tháng
  Bảng 13 (bài song-hong) là suy luận đối chiếu từ Niên giám Thống kê
  2023, chưa xác minh trực tiếp từng số.
- ⚠️ Bài river đầy đủ vẫn dùng ví dụ sông Trường Giang đoạn Trấn Giang
  — muốn thay bằng sông Việt Nam phải sửa cả mô hình 3D, các bước và
  bài kiểm tra. Bài song-hong đã giải quyết riêng cho sông Hồng bằng
  cách viết nội dung mới trên cảnh sông mô phỏng chung (ghi rõ trong
  bài là cảnh không phải sông Hồng thật).
- ⚠️ Nhóm C (Bài 4, 8, 15, 17 — cần cảnh 3D mới) và nhóm D (27 bài —
  không hợp 3D) chưa làm, xem `DANH-SACH-DIA-LI-10-KNTT.md`.
- 📋 Trang danh sách: `index.html` — liệt kê cả 17 bài theo chương
  SGK, mỗi bài có link mở file `dist/` tương ứng; tạo tự động từ
  `catalog.vn.json` bằng `node tools/make-index.mjs` (sửa catalog rồi
  chạy lại script để cập nhật).

## Bằng chứng kế thừa: từ bài thứ hai tới cả bộ Địa lí 10

Cách làm đã chứng minh qua karst (7 bước, entry gốc `Lp`, content var
gốc `Uc`, build bằng engine của chính nó) rồi mở rộng thành cả bộ: 10
bài nhóm A port bằng `tools/extract-lesson.mjs` (đã sửa để xử lý biến
ngoài nội dung, khai báo gộp, thứ tự vùng khác nhau) + 2 bài nhóm B viết
mới trên cảnh đã có. Xem `TRANSLATION.md` mục giai đoạn 2 để biết các
lỗi đã gặp và cách sửa (sentinel `__EXTREF__`, `act-fns.js`,
`content-fns.js`, va chạm tên `th`).

## Kiểm chứng đã làm

- Tách engine tại đúng điểm `var th={` (duy nhất trong file gốc);
  engine và bài tách riêng đều qua `node --check`, nối lại cũng qua.
- `th` được đánh giá bằng Node và xác nhận là dữ liệu thuần, không
  chứa hàm — vì vậy chuyển sang JSON không làm mất logic.
- Build ra `dist/river-step.html` (~928 KB script, so với ~908 KB gốc;
  chênh lệch là do format lại code, thêm header ghi nguồn và trường
  `vn` — không phải do thêm tính năng).
- Kiểm tra file build: có `<canvas id="gl">`, có tiêu đề gốc, không
  fetch/XHR, không script CDN; các URL ngoài duy nhất là namespace
  SVG/XHTML, link ghi nguồn GitHub/lab trong header comment và một
  tham chiếu tài liệu shader — không phải request lúc chạy.
- Kiểm tra bằng trình duyệt live qua server đã **dừng theo quyết định
  của Danh ngày 30/10** (chỉ build HTML, không cần server). Thay vào
  đó, bản build đã được kiểm chứng bằng trình duyệt headless mở
  trực tiếp file `file://` (không server): river hiện đủ giao diện —
  ảnh chụp bản tiếng Trung ở `verification-river-rebuilt.png`, ảnh
  chụp bản tiếng Việt ở `verification-river-vi.png` — và karst cũng
  chạy (DOM sau JS ~952 KB, có tiêu đề, không lỗi Uncaught). Danh tự
  mở file `dist/river-step.html` trên máy mình để nhìn là bước kiểm
  tra cuối còn lại trên máy thật. Lưu ý: lệnh chụp ảnh của
  chrome-headless-shell chập chờn với WebGL, cần thêm cờ
  `--disable-gpu` mới chạy ổn; `dump-dom` thì ổn định.

## Lỗi màn hình đen (30/10) — đã sửa, ghi lại để không tái phạm

Nguyên nhân: `tools/build.mjs` dùng `String.replace("__SCRIPT__", script)`.
`String.replace` xử lý đặc biệt các mẫu `$` trong chuỗi thay thế, và
code minify có đúng 7 chỗ `$&` (thực chất là biến `$` rồi toán tử `&&`,
ví dụ `$&&y`). 7 chỗ đó bị biến thành `__SCRIPT__&y`. Script vẫn qua
`node --check` (vì `__SCRIPT__&y` vẫn là cú pháp hợp lệ) nhưng chạy
sai, canvas đen, không có nội dung.

Cách sửa: dùng hàm thay thế trong `replace` (giá trị trả về không bị
xử lý mẫu `$`), và build sẽ lỗi ngay nếu file ra còn placeholder.
Bài học chung: **không bao giờ dùng `String.replace` với chuỗi thay
thế chứa code/mã nguồn mà không dùng hàm thay thế.**

Lỗi phụ cùng họ, phát hiện khi kiểm tra karst: tên biến nội dung gốc
khác nhau theo bài (river: `th`, karst: `Uc`). Build chỉ định nghĩa
`th` thì karst lỗi `ReferenceError: Uc is not defined`. Build nay tự
tạo alias `var Uc = th;` (và mọi tên tương tự trong lời gọi entry /
`.panel`) cho bài port.
