# Template: Social Post (Square)

**Dimensions:** 1080 × 1080 px (1:1)
**File format:** PNG hoặc JPG
**Max file size:** 500KB
**Use case:** Facebook feed, Instagram feed, LinkedIn post

## Composition

```
┌──────────────────────────┐
│  [Logo TL]   [Date TR]   │ 10% top — header bar
├──────────────────────────┤
│                          │
│                          │
│   [HEADLINE]             │
│   Center stage           │ 60% middle — main message
│   Max 3 dòng             │
│                          │
│                          │
├──────────────────────────┤
│  [CTA / @brand handle]   │ 15% bottom
│  [Decoration / accent]   │ 15% accent
└──────────────────────────┘
```

## Element spec

| Element | Position | Size | Notes |
|---|---|---|---|
| Background | Full | 1080×1080 | Primary HOẶC neutral dark (high engagement) |
| Logo | x=48, y=48 | width 100px | Top-left, reversed nếu bg tối |
| Date / category | x=932, y=72 | max 100×40 | Top-right, caption font, 14px |
| Headline | center | max 880×400 | Headline font 56-72px, max 3 dòng |
| CTA / handle | x=540 (center), y=920 | max 600×40 | "Tìm hiểu thêm →" hoặc "@brandhandle" |
| Decoration | bottom 15% | full width | Pattern / shape / dot accent với secondary color |

## Headline rules

- **Max 10 từ** (square format có không gian hơn hero)
- Trick: dùng line break để tạo emphasis
  ```
  "7 lỗi SEO
  bạn đang mắc
  (và cách sửa)"
  ```
- Số / câu hỏi / curiosity gap thường engage cao hơn
- VD tốt:
  - "5 dấu hiệu content thin"
  - "Tại sao bài bạn không rank?"
  - "Checklist 12 mục pre-publish"

## Background style

Square post chịu được màu mạnh hơn hero:

1. **Primary solid**: full primary color, headline neutral light → cao engagement
2. **Neutral dark solid**: bg `#202124`, headline neutral light → premium feel
3. **Split 50/50**: top primary / bottom neutral → modern
4. **Pattern accent**: bg neutral light + secondary color pattern ở 4 góc

## Variants cho carousel

Nếu là 1 trong series 5-10 slide:
- Slide 1: cover (theo template trên)
- Slide 2-N: bg consistent, headline nhỏ hơn (40px), thêm body text 24px
- Slide cuối: CTA full ("Lưu bài này", "Tag bạn bè", "Link in bio")

## Validate

- [ ] Headline đọc được trên preview Instagram (350px square)
- [ ] Logo + CTA không bị che bởi UI Facebook/Instagram (avoid top-left 60×60 cho profile pic overlay nếu là post Story)
- [ ] Contrast ≥ 4.5:1
- [ ] Vibe match brand tone trong tone.md
- [ ] File < 500KB

## Canva MCP prompt template

```
generate-design với:
  dimensions: 1080x1080
  style: social media post, [tone keywords từ tone.md]
  background: [primary HEX hoặc neutral dark]
  headline: "[user headline]", centered, 3 lines max
  headline font: [font từ fonts.md], 64px bold
  logo placement: top-left, [reversed/primary] version
  CTA text: "[user CTA]", bottom center, caption font 18px
  accent: [secondary HEX] pattern at bottom 15%
```
