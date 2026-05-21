# Insertion Plan Format

Format chính xác cho output Step 7. Skill render đúng theo format này — KHÔNG biến thể.

## Contents

- Full format
- Field spec
- Anchor snippet rule
- Brand check column
- Source label
- Caption format
- Failure note

---

## Full format

```markdown
## Image insertion plan — <doc-title>

**Doc**: <google-doc-url>
**Words**: <total-wordcount> | **Images planned**: <K>
**Drive folder**: <drive-share-link>
**Keyword chính**: <kw-main>
**Keyword phụ**: <kw-secondary-1>, <kw-secondary-2>

| # | Anchor (sau đoạn) | Image | Caption | Source | Brand check |
|---|---|---|---|---|---|
| 1 | "<30-char-snippet>" | [thumb](<drive-file-link>) | *<caption-italic>* | Unsplash | PASS |
| 2 | "<30-char-snippet>" | [thumb](<drive-file-link>) | *<caption-italic>* | Canva-gen | PASS |
| 3 | "<30-char-snippet>" | `[USER PROVIDES ORIGINAL]` | *<placeholder-caption>* | Original | — |
| ... |

### Cách paste vào Doc

1. Mở Doc gốc tại link trên.
2. Tìm anchor bằng CMD/CTRL+F với chuỗi 30 ký tự trong cột "Anchor".
3. Đặt con trỏ ngay sau đoạn anchor, Insert → Image → Drive → chọn ảnh từ folder.
4. Paste caption ngay dưới ảnh, format italic + căn giữa + font cỡ nhỏ hơn body 1-2pt.

### Brand check summary

- Pass: <X>/<K> ảnh
- Warn: <Y>/<K> ảnh — chi tiết ở failure note bên dưới (nếu có)

### Failure notes (nếu có)

- Anchor #N: <lý do fail> — <action gợi ý>
```

## Field spec

| Field | Format | Rule |
|---|---|---|
| `<doc-title>` | string | Lấy từ `mcp__claude_ai_Google_Drive__get_file_metadata` |
| `<total-wordcount>` | integer | Đếm từ body, loại title/footer |
| `<K>` | integer | Số ảnh đã PLAN (kể cả anchor placeholder) |
| `<drive-share-link>` | URL | Link folder share `anyone with link — viewer` |
| `<kw-main>` | string | User paste |
| `<kw-secondary-1>`, `<kw-secondary-2>` | strings | User paste, ít nhất 1 |

## Anchor snippet rule

- Lấy **30 ký tự cuối** của paragraph ngay trước vị trí chèn ảnh.
- Nếu paragraph <30 ký tự, lấy toàn bộ.
- Đặt trong dấu nháy kép `"..."`.
- KHÔNG escape dấu nháy bên trong — chọn snippet không chứa nháy kép.
- Snippet phải UNIQUE trong Doc (CMD+F ra đúng 1 match). Nếu trùng, dịch sang 30 ký tự kế tiếp.

## Brand check column

3 giá trị:

- `PASS` — đủ 4 brand check (tone màu, logo nếu ảnh gen, nhân vật, không text overlay không cần thiết).
- `WARN` — pass ≥2/4, fail 1-2 item nhỏ. Note trong failure notes.
- `—` (em-dash) — KHÔNG áp dụng cho ảnh tag `original` (user tự kiểm) hoặc anchor placeholder.

KHÔNG dùng `FAIL` — fail thì skill đã re-gen + fallback ở Step 6, đến output đã pass hoặc warn.

## Source label

Một trong:

- `Unsplash` — fetch từ Unsplash.
- `Pexels` — fetch từ Pexels.
- `Web-search` — qua WebFetch (fallback khi Unsplash/Pexels rate limit).
- `Canva-gen` — gen mới qua `/brand-image`.
- `Gemini-gen` — gen mới qua `/seo-image-gen`.
- `Original` — placeholder cho user upload.

KHÔNG label kiểu "stock photo" chung — phải cụ thể nguồn.

## Caption format

- Wrap bằng `*` (markdown italic) trong table.
- ≤70 ký tự (đếm cứng — bao gồm space và dấu câu trong `*...*`).
- 1 keyword duy nhất / caption.
- Trong Doc thực, user format italic + căn giữa + cỡ nhỏ hơn body 1-2pt.

## Failure note

Mỗi anchor fail liệt kê 1 dòng:

```
Anchor #<N>: <reason> — <suggested-action>
```

Ví dụ:

- `Anchor #3: Unsplash rate limit, fallback Gemini fail (quota) — gen tay bằng /seo-image-gen sau khi reset quota.`
- `Anchor #5: Drive upload fail (quota) — ảnh local tại /tmp/blog-images-<doc-id>/img-5.png, upload tay vào Drive.`

KHÔNG ghi vague "ảnh không tạo được" — phải nêu nguyên nhân + action gợi ý.
