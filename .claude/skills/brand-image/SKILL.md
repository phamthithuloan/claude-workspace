---
name: brand-image
description: "Tạo hình ảnh (hero blog, social post, OG image, infographic) cho bài viết SEO theo brand guideline. Tích hợp Canva MCP để generate design từ brand template, áp dụng đúng colors/fonts/logo. Use khi user nói: tạo ảnh bài viết, hero image, OG image, social post, ảnh blog, tạo hình theo brand, brand image, infographic, thumbnail bài."
user-invokable: true
argument-hint: "<image-type> <headline>"
license: MIT
compatibility: "Requires Canva MCP (đã connect trong Claude). Brand guideline trong brand-guidelines/ phải được điền trước khi dùng."
metadata:
  author: Seongon Content
  version: "1.0.0"
  category: design
---

# Tạo image theo brand guideline

Bạn là brand designer. Tạo image cho bài SEO, đảm bảo đúng guideline.

## Tham chiếu brand

LUÔN đọc trước khi tạo:
- [brand-guidelines/colors.md](brand-guidelines/colors.md) — palette + ratio dùng
- [brand-guidelines/fonts.md](brand-guidelines/fonts.md) — font cho headline / body / accent
- [brand-guidelines/logo-usage.md](brand-guidelines/logo-usage.md) — clear space, placement, version
- [brand-guidelines/tone.md](brand-guidelines/tone.md) — voice + visual tone

## Templates có sẵn

- [templates/blog-hero.md](templates/blog-hero.md) — 1200×628, OG image / blog hero
- [templates/social-post.md](templates/social-post.md) — 1080×1080, Facebook/Instagram feed
- [templates/infographic.md](templates/infographic.md) — 1080×1920 vertical, Pinterest/Story

## Quy trình

### Bước 1 — Gather requirements
Hỏi user:
- **Loại image**: hero / social / infographic / OG / khác?
- **Bài viết liên quan**: tiêu đề + URL (nếu đã có)?
- **Headline trên ảnh**: ≤ 8 từ (text quá dài → đề xuất user rút)?
- **Style ưu tiên**: photographic / illustration / abstract / typography-heavy?

### Bước 2 — Đọc brand guideline
Đọc 4 file trong `brand-guidelines/` để chốt:
- Primary + secondary color sẽ dùng
- Font cho headline + accent
- Logo version + placement
- Style tone (minimal / bold / playful / corporate)

### Bước 3 — Map template
Theo loại image bước 1, chọn template tương ứng trong `templates/`. Đọc file template để biết spec + composition.

### Bước 4 — Tạo design qua Canva MCP

Sequence call:

1. **Lấy brand kit**:
   ```
   list-brand-kits → chọn brand kit của bạn (gọi `brandKitId`)
   ```

2. **Tìm template phù hợp** (nếu user có brand template trên Canva):
   ```
   search-brand-templates với query là tên loại (vd "blog hero")
   ```
   Nếu có template → bước 5a. Nếu không → bước 5b.

3. **5a. Tạo từ brand template**:
   ```
   create-design-from-brand-template với brandTemplateId + data fill (headline, subheadline, image URL, …)
   ```

4. **5b. Generate design from scratch**:
   ```
   generate-design (hoặc generate-design-structured) với prompt mô tả:
   - Dimensions (theo template)
   - Brand colors (từ guideline)
   - Headline text
   - Style/tone
   - Logo placement bottom-right
   ```

5. **Export**:
   ```
   export-design → PNG quality high
   ```

6. **Trả về user**:
   - Link Canva design (để edit thêm)
   - Link export PNG
   - Preview thumbnail

### Bước 5 — Validate

Trước khi finalize:
- [ ] Headline ≤ 8 từ + readable trên mobile
- [ ] Logo có clear space đủ (xem logo-usage.md)
- [ ] Color contrast ≥ 4.5:1 (text vs background)
- [ ] File size < 500KB (cho web performance)
- [ ] Có alt text suggest cho user paste vào CMS

## Output Format

Trả về cho user theo template:

```
## ✓ Image created

**Type:** [hero | social | infographic | OG]
**Dimensions:** [W×H px]
**File size:** [XXX KB]

**Links:**
- 🎨 Canva design (edit-able): [URL]
- 📥 PNG export: [URL]
- 🖼️ Preview thumbnail: [URL]

**Alt text** (paste vào CMS):
> [descriptive alt text 100–125 chars, có keyword bài viết]

**Brand compliance check:**
- [✓/✗] Colors khớp palette (primary + secondary)
- [✓/✗] Font headline đúng (theo fonts.md)
- [✓/✗] Logo placement + clear space đúng (theo logo-usage.md)
- [✓/✗] Contrast text vs background ≥ 4.5:1
- [✓/✗] File size < 500KB (hero/social) hoặc < 1MB (infographic)
- [✓/✗] Headline ≤ 8 từ (hero) / 10 từ (social) / 12 từ (infographic)

Nếu có ✗ → đề xuất user gọi lại skill hoặc dùng MCP `perform-editing-operations` để sửa.
```

## Pre-delivery checklist (trước khi gửi link cho user)

- [ ] Đã ĐỌC **cả 4 file** trong `brand-guidelines/` (colors, fonts, logo-usage, tone)
- [ ] Đã chọn đúng template (`templates/blog-hero.md` / `social-post.md` / `infographic.md`) và đọc spec
- [ ] Headline đã rút gọn về limit theo loại image (≤ 8 / 10 / 12 từ)
- [ ] Primary color + secondary color HEX khớp `colors.md`
- [ ] Font headline + body khớp `fonts.md`, có support tiếng Việt
- [ ] Logo version đúng (primary cho light bg, reversed cho dark bg)
- [ ] Logo placement + clear space đúng `logo-usage.md`
- [ ] Đã chạy contrast check ≥ 4.5:1 (WCAG AA)
- [ ] File export < 500KB (hero/social) hoặc < 1MB (infographic)
- [ ] Đã suggest alt text cho user (theo Output Format)

## Common pitfalls (vết xe đổ)

| Lỗi hay mắc | Why bad | Fix |
|---|---|---|
| Quên đọc `brand-guidelines/*` trước khi gọi Canva MCP | Output lệch brand identity, phải redo | LUÔN đọc 4 file brand-guidelines TRƯỚC bước gọi `generate-design` |
| Headline > 8 từ trên hero | Mobile preview nhỏ → không đọc được | Đề xuất user rút, phần dài chuyển sang subheadline |
| Logo đè lên content / text quan trọng | Visual clutter, brand lệch | Theo padding + position rules trong `logo-usage.md`, kiểm tra clear space |
| Generate xong không validate contrast | Text khó đọc, fail accessibility (WCAG) | Chạy pre-delivery checklist TRƯỚC khi `export-design` |
| Dùng font không hỗ trợ tiếng Việt | Thiếu dấu (ư/ơ/ấ/ẵ), text vỡ | Chỉ dùng font có note "support VN" trong `fonts.md`; test 5 ký tự `ưỡĩậẵ` |
| Export PNG full quality (100%) | File > 1MB → ảnh hưởng Core Web Vitals (LCP) | Quality 85% PNG, hoặc convert WebP nếu CMS hỗ trợ |
| Gradient nhiều màu sặc sỡ | Chống lại brand tone (most brands là minimal/modern) | Chỉ subtle 2-tone từ palette; check `tone.md` |
| Đặt 2 logo cạnh nhau (partnership content) | Không theo guideline, có thể violate brand đối tác | Hỏi user có partnership guideline riêng không |
| Dùng pure black `#000000` cho text | Quá harsh, không theo brand | Dùng neutral dark trong `colors.md` (vd `#202124`) |
| Tự đoán brand colors khi `colors.md` còn `[FILL IN]` | Output không khớp brand thực | Dừng lại, hỏi user điền `brand-guidelines/colors.md` trước |

## Lưu ý chuyển tiếp

- Image xong → đề xuất user dùng skill `seo-images` để check optimization (alt text, file size, format)
- Cần generate illustration/photo AI thuần (không Canva) → đề xuất `seo-image-gen` thay vì skill này

## Khi nào KHÔNG dùng skill này

- Cần generate hình AI (illustration/photo) hoàn toàn → dùng `seo-image-gen` thay vì Canva
- Edit ảnh đã có → upload và edit thẳng trên Canva, skill này chỉ tạo mới
- Logo design / brand identity từ đầu → skill này assume brand đã định hình
