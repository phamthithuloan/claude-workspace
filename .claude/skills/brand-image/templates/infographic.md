# Template: Infographic (Vertical)

**Dimensions:** 1080 × 1920 px (9:16)
**File format:** PNG (giữ độ nét cho text-heavy)
**Max file size:** 1MB (cao hơn vì nhiều content)
**Use case:** Pinterest pin, Instagram Story/Reel cover, blog embed long, LinkedIn document post

## Composition (3-zone vertical)

```
┌──────────────────────┐
│   [LOGO TL]          │
│                      │
│   ╔════════════════╗ │
│   ║   TITLE         ║│   ZONE 1 — Title (15%)
│   ║   Subtitle      ║│
│   ╚════════════════╝ │
│                      │
├──────────────────────┤
│                      │
│   ① Step 1           │
│   text + visual      │
│                      │
│   ② Step 2           │   ZONE 2 — Body (70%)
│   text + visual      │   3-7 sections
│                      │
│   ③ Step 3           │
│   text + visual      │
│                      │
│   ④ ...              │
│                      │
├──────────────────────┤
│                      │
│   CTA + brand URL    │   ZONE 3 — Footer (15%)
│   [LOGO BC]          │
└──────────────────────┘
```

## Element spec

| Element | Position | Size | Notes |
|---|---|---|---|
| Background | Full | 1080×1920 | Neutral light, có subtle pattern hoặc divider lines |
| Logo top | x=64, y=64 | width 180px | Header logo, primary version |
| Title | x=540 (center), y=160 | max 880×200 | Headline font 72px bold |
| Subtitle | x=540 (center), y=280 | max 880×80 | Subhead font 28px |
| Section block (×N) | full width, height 250-350 | mỗi block | Number + headline + body + icon/visual |
| Divider | between sections | 1080×2 | Primary color line, 60% opacity |
| CTA footer | x=540, y=1800 | max 880×60 | "Đọc full tại brand.com/[slug]" |
| Logo footer | x=540 (center), y=1860 | width 100px | Optional, nếu cần emphasize |

## Section block structure

Mỗi của 3-7 section:

```
┌────────────────────────────────────────┐
│ ┌──┐                                   │
│ │ 1│  HEADLINE SECTION                 │
│ └──┘  Body text 2-3 dòng, mô tả        │
│       cụ thể step này                  │
│                            [icon 80px] │
└────────────────────────────────────────┘
```

- Number badge: 64×64 px, primary background, neutral light text, bold
- Section headline: 36px, headline font, neutral dark
- Body: 18-20px, body font, neutral dark 80% opacity, max 3 dòng
- Icon: 80×80px, ở góc phải, line style theo tone.md
- Divider giữa các section: line 2px primary 60% opacity

## Cấm

- Không > 7 section (gây overwhelm, user không đọc hết)
- Không text < 16px (không đọc được khi shrink Pinterest preview)
- Không nhồi nhét — mỗi section tối thiểu 80px khoảng trống trên/dưới
- Không dùng 5+ màu — stick với primary + neutral, accent chỉ trong number badge

## SEO bonus

Vì infographic thường embed lên blog post:
- **File name khi save**: `[keyword-chinh]-infographic.png`
- **Alt text suggest**: "Infographic [tiêu đề]: [3-5 step liệt kê]. Brand: [tên brand]"
- **Caption embed**: link về bài blog đầy đủ

## Pinterest optimization (nếu publish lên Pinterest)

- Title trong design có **keyword Pinterest** (search-friendly)
- Description (caption) khi pin: 200-500 ký tự, có keyword + 2-3 hashtag
- URL về landing page có infographic embed

## Validate

- [ ] Đọc được toàn bộ text ở zoom 50% (Pinterest preview size)
- [ ] Brand logo xuất hiện ≥ 1 lần (top hoặc bottom)
- [ ] Number/icon style consistent across all sections
- [ ] File < 1MB (PNG quality 85%)
- [ ] Có CTA + URL về site

## Canva MCP prompt template

```
generate-design-structured với:
  dimensions: 1080x1920
  type: infographic, vertical, [N] steps
  title: "[user title]"
  subtitle: "[user subtitle]"
  sections: [
    { number: 1, headline: "...", body: "...", icon: "..." },
    { number: 2, headline: "...", body: "...", icon: "..." },
    ...
  ]
  primary color: [HEX]
  background: neutral light
  fonts: headline=[font], body=[font]
  logo: top-left header + bottom-center footer
  CTA footer: "Đọc full tại [URL]"
```
