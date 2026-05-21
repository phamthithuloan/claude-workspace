---
name: chen-anh-bai
description: "Chèn ảnh tự động vào bài viết SEO trong Google Doc. User paste link Doc, skill hỏi keyword + brand qua AskUserQuestion, đọc Doc qua curl export URL (không cần MCP auth), tính số ảnh theo wordcount (1 ảnh / 300-400 từ), tìm ảnh Unsplash hoặc gen mới qua brand-image (Canva) / seo-image-gen (Gemini), viết caption ≤70 ký tự in nghiêng có keyword, build HTML có ảnh chèn sẵn, upload lên Drive convert thành Doc mới. Triggers khi user nói chèn ảnh, chèn ảnh bài, minh họa bài, thêm ảnh cho bài, auto-illustrate doc, ảnh cho Google Doc, chèn ảnh bài SEO, hoặc paste link Google Docs kèm yêu cầu chèn ảnh."
user-invokable: true
argument-hint: "<google-doc-url>"
license: MIT
compatibility: "Free — cần Doc share `anyone with link`. Optional: /brand-image (Canva MCP) + /seo-image-gen (Gemini MCP) cho gen ảnh. Google Drive MCP để upload Doc mới (fallback: HTML local cho user drag-drop Drive)."
metadata:
  author: Seongon Content
  version: "1.0.0"
  category: content
---

# chen-anh-bai

Đọc Google Doc bài viết SEO, hỏi user keyword + brand qua AskUserQuestion, tự tính số ảnh cần, tìm hoặc gen ảnh theo brand checklist, trả về insertion plan + ảnh đã upload Drive để user paste vào Doc.

## Khi nào dùng

User gõ một trong các pattern:

- `/chen-anh-bai <google-doc-url>`
- "Chèn ảnh cho bài này: <url>"
- "Minh họa bài viết Doc <url>"
- "Thêm ảnh cho bài SEO <url>"
- Paste link Google Docs trong context nói về việc thêm ảnh

KHÔNG dùng skill này khi:

- Bài viết KHÔNG ở Google Doc (markdown, WordPress, file local) — dùng workflow khác.
- User chỉ muốn 1 ảnh đơn lẻ (gen hero, OG image) — dùng `/brand-image` hoặc `/seo-image-gen` trực tiếp.
- User muốn audit/optimize ảnh đã có trong bài — dùng `/seo-images`.
- Doc chưa share quyền `anyone with link` — skill không đọc được.

## Tiền điều kiện

- [ ] Doc đã share quyền `anyone with link — viewer` (cứng cờ Google, không skill nào bypass được).
- [ ] Skill `brand-image` và `seo-image-gen` available trong workspace cho bước gen.

KHÔNG phụ thuộc Google Drive MCP auth account — skill dùng URL export public.

## Default settings

| Setting | Default | Override khi |
|---|---|---|
| Mật độ ảnh | 1 ảnh / 350 từ (ceil) | User chỉ định "1 ảnh / N từ" hoặc số ảnh cố định |
| Số ảnh tối thiểu | 3 (cho bài ≥1000 từ) | Bài <1000 từ → 1-2 ảnh tùy độ dài |
| Image source priority | Unsplash → Pexels → gen mới | User yêu cầu "gen tất cả" hoặc "chỉ ảnh nguyên bản" |
| Gen tool | Canva (brand-image) cho infographic/social, Gemini (seo-image-gen) cho photo realistic | User chỉ định tool cụ thể |
| Caption length | ≤70 ký tự, in nghiêng | — (cứng theo checklist) |
| Caption keyword | 1 keyword chính HOẶC phụ HOẶC LSI / 1 caption | — |
| Vị trí ảnh | Sau heading H2/H3 hoặc giữa paragraph dài | User chỉ định vị trí cụ thể |
| Brand input | Hỏi qua AskUserQuestion (preset Hồng Ngọc/Seongon/Custom/Skip) | User đã mention brand trong message → skip hỏi |
| Output | Insertion plan markdown + ảnh upload Drive folder | — |
| Auto-proceed | Tất cả bước sau Step 1, chỉ dừng khi Doc đọc fail | User yêu cầu "duyệt trước khi chèn" |

## Pipeline — 7 bước

### Step 1 — Gather input qua AskUserQuestion

KHÔNG bắt user paste hết 1 lần. Detect input đã có trong message:

- Doc URL: regex `docs.google.com/document/d/[a-zA-Z0-9_-]+`. Thiếu → hỏi 1 câu text.
- Brand mention: keyword "Hồng Ngọc" / "Seongon" / "brand X" trong message.

Thiếu item nào → hỏi GỘP 1 lần qua `AskUserQuestion` (không hỏi tuần tự).

3 câu hỏi gộp 1 message:

**Q1 — Brand**:
- "Hồng Ngọc" (Recommended nếu context nói về mắt/y tế) — load preset từ `references/brand-presets.md`
- "Seongon" — load preset Seongon
- "Custom" — sau khi chọn, hỏi follow-up 1 message (primary HEX, accent HEX, font, tệp KH)
- "Skip brand" — chỉ Unsplash, không gen mới

**Q2 — Keyword chính** (text input, required): từ khóa SEO chính của bài.

**Q3 — Keyword phụ** (text input, optional, cho phép trống): list 2-3 keyword cách nhau dấu phẩy. Trống → skill auto-extract từ H2/H3 Doc.

**Exit condition**: có đủ Doc URL + brand info + keyword chính.

### Step 2 — Đọc Doc content qua export URL

Extract Doc ID từ URL: regex `/document/d/([a-zA-Z0-9_-]+)`.

Fetch raw text qua Bash:

```
curl -sL "https://docs.google.com/document/d/<DOC_ID>/export?format=txt" -o /tmp/doc-<DOC_ID>.txt
```

KHÔNG dùng MCP Drive `read_file_content` — bypass auth account issue. URL export hoạt động nếu Doc share `anyone with link`.

Parse:

- `wordcount` = `wc -w /tmp/doc-<DOC_ID>.txt`.
- `headings` = grep H1/H2/H3 (regex `^[0-9]+\. ` hoặc heading text in caps).
- `paragraphs` = split theo blank line.

**Decision point** — curl trả empty/HTML error page (Doc chưa share): báo user "Doc chưa share `anyone with link`. Vào Doc → Share → General access → Anyone with the link → Viewer." Dừng. KHÔNG fabricate content.

### Step 3 — Tính số ảnh + vị trí anchor

`num_images = max(3, ceil(wordcount / 350))` cho bài ≥1000 từ. Bài <1000 từ: `ceil(wordcount / 350)`.

Phân bổ vị trí:

1. Ưu tiên 1 ảnh ngay sau mỗi H2 chính (trừ H2 "Kết luận", "FAQ", "Tham khảo").
2. Còn slot → chèn vào giữa paragraph dài (>200 từ).
3. KHÔNG đặt 2 ảnh liên tiếp cách nhau <200 từ.

Mỗi anchor lưu: `(position_id, paragraph_text_snippet_30char, image_purpose)`. `image_purpose` lấy từ context paragraph xung quanh.

### Step 4 — Phân loại nguồn ảnh cho từng anchor

**DEFAULT cho mọi anchor: tìm ảnh trên mạng (Unsplash → Pexels → WebSearch).** KHÔNG ưu tiên gen ảnh.

Decision tree (theo thứ tự):

```
1. image_purpose CỤ THỂ tới brand identity (vd: "Elite Dental showcase",
   "quy trình 7 bước Hồng Ngọc", "AACI cert + công nghệ riêng")?
   → gen-canva (brand-image skill) — apply brand template + logo

2. Concept generic có thể minh họa bằng stock photo (vd: "5 yếu tố ảnh hưởng",
   "dấu hiệu bất thường", "cách giảm đau", "quy trình chung", "khách hàng",
   "phòng khám", "scene chung")?
   → web-search (DEFAULT)

3. User explicit cung cấp ảnh nguyên bản từ tổ chức?
   → original — chỉ khi user upload, KHÔNG hỏi nếu không cần
```

**Anti-pattern**: gen Canva cho mọi anchor có chữ "infographic" trong purpose. "5 yếu tố ảnh hưởng" KHÔNG bắt buộc phải là infographic — có thể minh họa bằng ảnh x-ray, dentist consultation, dental tools, v.v. Chỉ gen khi search không tìm được ảnh phù hợp HOẶC nội dung phải có brand identity riêng.

Tag mỗi anchor:
- `web-search` (DEFAULT) — Unsplash/Pexels/WebSearch
- `gen-canva` — CHỈ khi brand-specific (logo, brand colors enforce, brand showcase)
- `gen-gemini` — fallback khi `web-search` thử ≥3 query mà không tìm được ảnh free + phù hợp
- `original` — chỉ khi user explicit upload ảnh tổ chức

### Step 5 — Fetch hoặc gen ảnh

Theo tag từng anchor:

- `web-search`: WebFetch Unsplash/Pexels search API (hoặc WebSearch query "<image_purpose> <brand vibe>"), lấy URL ảnh có aspect ratio 16:9 hoặc 4:3, resolution ≥1200px. Verify license: chỉ lấy free/CC. KHÔNG dùng ảnh có watermark.
- `gen-canva`: invoke `/brand-image` skill với purpose + brand guideline đã parse.
- `gen-gemini`: invoke `/seo-image-gen` skill với prompt = `<image_purpose> + tone màu <brand primary>, không text trên ảnh`.
- `original`: skip fetch, để placeholder `[USER PROVIDES ORIGINAL]` trong plan.

Lưu mỗi ảnh local tạm: `/tmp/blog-images-<doc-id>/img-<position_id>.{ext}`.

**Decision point** — Web search trả 0 kết quả hoặc tất cả ảnh có watermark: fallback sang gen Gemini cho anchor đó, note trong plan.

**Decision point** — Gen tool fail (quota/auth): note "[GEN FAILED — needs manual]" trong plan cho anchor đó, tiếp tục anchor còn lại. KHÔNG dừng pipeline.

### Step 6 — Viết caption + brand check mỗi ảnh

Cho từng ảnh, viết caption:

- ≤70 ký tự (đếm cứng).
- In nghiêng (`*caption text*` trong markdown plan).
- Chứa 1 keyword (chính / phụ / LSI). Phân bổ: ưu tiên keyword chính ở ảnh đầu, keyword phụ cho ảnh giữa, LSI cho ảnh cuối — không lặp cùng 1 keyword 2 ảnh liên tiếp.
- Mô tả rõ nội dung ảnh, không lặp paragraph xung quanh.

Brand check cho ảnh `gen-canva` / `gen-gemini`:

- [ ] Tone màu khớp brand primary/secondary.
- [ ] Có logo ở góc (chỉ cho ảnh tự gen, KHÔNG cho ảnh web-search/original).
- [ ] Nhân vật khớp target customer (giới tính, độ tuổi, ngành).
- [ ] Không text overlay trừ khi infographic.

Fail brand check → re-gen 1 lần. Vẫn fail → note trong plan, vẫn ship.

### Step 7 — Output insertion plan với direct URLs

KHÔNG upload Drive (vì bỏ MCP Drive). Output insertion plan với:

- Ảnh `web-search`: URL direct từ `images.unsplash.com/...` — Google Doc tự fetch khi user paste URL via Insert > Image > By URL.
- Ảnh gen (`gen-canva` / `gen-gemini`): URL Canva design hoặc local path `/tmp/blog-images-<doc-id>/img-<N>.png` — user download + upload tay vào Doc qua Insert > Image > Upload.
- Ảnh `original`: placeholder, user tự cung cấp.

Output format: xem `references/insertion-plan-format.md`.

Trả user:

- Link Drive folder chứa ảnh đã upload.
- Insertion plan markdown table.
- 1 dòng instruction copy-paste cho user.
- Brand check summary (pass/warn).

## Decision points

| Step | Auto-proceed khi | Hỏi user khi |
|---|---|---|
| 1 | Input đủ Doc URL + brand + keyword chính | Thiếu item nào → AskUserQuestion gom 1 lần (brand preset + keyword chính + keyword phụ) |
| 2 | Doc đọc OK | Doc fail → báo lỗi + dừng |
| 4 | Image purpose match decision tree | Image purpose tag `original` → hỏi user có ảnh không |
| 5 | Fetch/gen OK | Tất cả ảnh fail liên tiếp ≥3 → dừng + báo |
| 7 | Drive upload OK | Drive quota hết → fallback output URL local + plan |

## Error Handling (Vết xe đổ)

| Error | Cause | Resolution |
|---|---|---|
| Doc URL invalid / "Requested entity not found" | Doc chưa share `anyone with link` HOẶC URL sai HOẶC Doc đã bị xóa | Hướng dẫn user: Doc → Share → General access → Anyone with link → Viewer. Verify URL format `docs.google.com/document/d/<ID>`. Dừng, KHÔNG fabricate. |
| Doc wordcount <300 | Bài quá ngắn (note, draft, snippet) | Cảnh báo, chuyển sang 1-ảnh hero mode thay vì 3-6 ảnh |
| curl trả empty hoặc HTML login page | Doc share mode "Restricted" hoặc cần Google login | Báo lỗi share permission, dừng |
| Unsplash/Pexels API rate limit | Quá 50 req/h trên free tier | Fallback WebSearch + WebFetch. Note source kém tin cậy hơn |
| Unsplash trả toàn ảnh premium (`plus.unsplash.com`) | Niche query (dental, medical specific) | Fallback gen-gemini cho anchor đó |
| Canva MCP fail / unavailable | Canva extension chưa install hoặc token expired | Fallback Gemini cho anchor `gen-canva`. Note trong plan. |
| Gemini quota hết | Nanobanana extension quota daily | Note `[GEN FAILED — needs manual]` cho anchor. Continue anchors khác. |
| Google Drive MCP token expired | Token Drive hết hạn giữa pipeline | Báo user re-auth qua `/mcp`. Output HTML local + hướng dẫn drag-drop Drive. |
| `create_file` upload fail | Drive quota hết hoặc network | Output `/tmp/<doc-id>-with-images.html` + hướng dẫn drag-drop manual |
| Tất cả 6 anchors fail | Đa nguyên nhân tích lũy | Output partial plan + liệt kê anchors failed + lý do từng cái. KHÔNG ship plan giả pretend complete. |
| Apps Script Run từ user fail | User chạy với account Viewer-only | Hướng dẫn run với account có quyền Edit Doc |

## Pre-Delivery Review (MANDATORY)

Trước khi return final output cho user, skill PHẢI tự verify checklist sau. KHÔNG ship nếu fail bất kỳ item nào — báo gap rõ ràng thay vì giả pretend complete.

### Doc content integrity
- [ ] Doc đã đọc thành công qua curl (`wc -w > 0`)
- [ ] Wordcount real, không phải estimate
- [ ] Headings parsed đúng số H2/H3
- [ ] KHÔNG fabricate paragraph khi read fail

### Số ảnh + vị trí
- [ ] `num_images = max(3, ceil(wordcount/350))` cho bài ≥1000 từ — verified math
- [ ] Mỗi anchor có position_id, snippet 30 ký tự unique trong Doc, image_purpose rõ
- [ ] KHÔNG anchor ở H2 "Kết luận" / "FAQ" / "Tham khảo" / "Lưu ý y khoa"
- [ ] KHÔNG 2 anchor liên tiếp <200 từ

### Caption quality
- [ ] Mỗi caption ≤70 ký tự (đếm cứng, bao gồm space + dấu câu)
- [ ] Mỗi caption có ĐÚNG 1 keyword (chính HOẶC phụ HOẶC LSI)
- [ ] KHÔNG 2 caption liên tiếp dùng cùng 1 keyword
- [ ] Caption italic format trong output (`*text*` markdown HOẶC `<em>` HTML)

### Source labels
- [ ] Mỗi anchor có source label rõ: `Unsplash` | `Pexels` | `Web-search` | `Canva-gen` | `Gemini-gen` | `Original`
- [ ] KHÔNG label vague kiểu "stock photo"
- [ ] Web-search anchors có image URL kiểm tra license (chỉ free/CC, không premium/watermark)

### Brand check (cho ảnh gen)
- [ ] Tone màu khớp brand primary/secondary
- [ ] Logo CHỈ apply cho `gen-canva` / `gen-gemini`, KHÔNG cho `web-search` / `original`
- [ ] Nhân vật khớp target customer
- [ ] Brand check column = PASS | WARN (KHÔNG dùng FAIL — đã re-gen ở Step 6)

### Output format
- [ ] Insertion plan có table 6 cột đúng spec `references/insertion-plan-format.md`
- [ ] Có instructions copy-paste cho user
- [ ] Có brand check summary count (pass/warn)
- [ ] Nếu có anchor fail → liệt kê failure notes với reason + suggested action

Fail ≥1 item → KHÔNG return final, fix gap hoặc note rõ "INSUFFICIENT DATA" thay vì ship plan giả.

## Output

```
Image insertion plan — <doc-title>

Doc: <url>
Words: <N> | Images planned: <K>
Drive folder: <drive-link>

| # | Anchor (sau đoạn) | Image | Caption | Source | Brand check |
|---|---|---|---|---|---|
| 1 | "...30 ký tự cuối paragraph..." | <thumb-link> | *<caption>* | Unsplash | PASS |
| 2 | "..." | <thumb-link> | *<caption>* | Canva-gen | PASS |
| 3 | "..." | [USER PROVIDES ORIGINAL] | *<placeholder>* | Original | — |

Cách paste vào Doc:
1. Mở Doc gốc <url>.
2. Tìm anchor bằng CMD/CTRL+F với 30 ký tự ở cột "Anchor".
3. Insert > Image > Drive, chọn ảnh từ folder trên.
4. Paste caption ngay dưới, format italic + căn giữa.

Brand check: <pass-count>/<total> ảnh pass.
[Nếu có anchor fail: liệt kê + lý do]
```

## Anti-patterns

- KHÔNG bịa nội dung Doc nếu read_file_content fail — phải báo lỗi và dừng.
  - BAD: "Doc nói về abc..." khi chưa đọc được.
  - GOOD: "Doc fail đọc do permission. Share `anyone with link` rồi gọi lại skill."
- KHÔNG chèn logo vào ảnh `web-search` hoặc `original` — logo chỉ apply cho ảnh tự gen.
- KHÔNG copy ảnh có watermark hoặc nguồn không rõ license. Skip thay vì dùng tạm.
- KHÔNG dùng cùng 1 keyword trong 2 caption liên tiếp — vi phạm SEO over-optimization.
- KHÔNG đặt ảnh vào H2 "Kết luận" / "FAQ" / "Tham khảo".
- KHÔNG output insertion plan khi <50% anchor có ảnh — note rõ thay vì giả pretend complete.
- KHÔNG hỏi user tuần tự từng item ở Step 1 — gom mọi item thiếu vào 1 AskUserQuestion call.

## Skill files

| File | Purpose | Load when |
|---|---|---|
| `references/image-checklist.md` | Full checklist hình ảnh (UX, vị trí, caption, brand định vị) | Step 4, 6 — chi tiết rule |
| `references/insertion-plan-format.md` | Markdown table format chính xác cho output | Step 7 — render output |
| `references/brand-presets.md` | Preset brand Hồng Ngọc + Seongon + template Custom | Step 1 — load khi user chọn preset |
| `EVALS.md` | 3 test scenario (Golden/Edge/Anti) | Manual test sau khi edit SKILL.md |
