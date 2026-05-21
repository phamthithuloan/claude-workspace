# Template: Blog Hero / OG Image

**Dimensions:** 1200 × 628 px (16:8.4, OG standard)
**File format:** PNG hoặc JPG
**Max file size:** 500KB
**Use case:** Blog post hero, Facebook/Twitter share preview, LinkedIn share

## Composition (grid)

```
┌──────────────────────────────────────────────┐
│                                              │
│   [Headline area — 60% width, left aligned]  │ 70% height
│   "Cách X cho Y năm 2026"                    │
│                                              │
│   [Subheadline / kicker — 40% width]         │
│   "Hướng dẫn 7 bước + checklist PDF"         │
│                                              │
├──────────────────────────────────────────────┤
│  [Visual area — right 40%]      [Logo BR]    │ 30% height
│  (icon / photo / illustration)               │
└──────────────────────────────────────────────┘
```

## Element spec

| Element | Position | Size | Notes |
|---|---|---|---|
| Background | Full | 1200×628 | Neutral light hoặc primary 40% |
| Headline | x=64, y=120 | max 720×280 | Headline font, 64-96px, neutral dark |
| Subheadline | x=64, y=420 | max 720×80 | Subhead font, 24-32px, neutral 60% opacity |
| Visual element | x=760, y=64 | max 376×440 | Illustration / photo / icon group |
| Logo | x=1080, y=556 | width 100px | Bottom-right, padding 32px |
| Brand bar (optional) | Top edge | 1200×8 | Primary color strip |

## Headline rules

- **Max 8 từ** (Vietnamese tends longer than English, strict cap)
- Có **keyword chính** trong headline
- Có hook: số / năm / question / promise
- VD tốt: "Cách viết SEO content 2026", "7 bước tăng traffic"
- VD xấu: "Tổng quan toàn diện về quy trình viết content marketing chuẩn SEO" (dài, không có hook)

## Background options

1. **Solid neutral** (60% case): nền sáng `#F8F9FA`, headline neutral dark
2. **Primary block + neutral block** (split 60/40): primary trái, neutral phải
3. **Subtle gradient** (chỉ 2-tone từ palette): top-left primary → bottom-right secondary
4. **Photo with overlay**: photo blur + overlay neutral dark 60% opacity

KHÔNG dùng full primary color full background.

## Visual area (right 40%)

Chọn 1:
- **Illustration**: từ brand illustration kit hoặc Canva element (line style, theo tone)
- **Photo**: cropped square, theo direction photo trong tone.md
- **Icon group**: 3-5 icon đại diện nội dung
- **Abstract**: geometric shapes với brand color

## Validate trước khi export

- [ ] Headline đọc được trên mobile preview (640px wide test)
- [ ] Logo có clear space đủ (xem logo-usage.md)
- [ ] Contrast headline vs background ≥ 4.5:1
- [ ] Không có element bị cắt khi crop 1.91:1 (FB) hoặc 1.78:1 (Twitter)
- [ ] File < 500KB (export PNG quality 85%)

## Canva MCP prompt template

```
generate-design với:
  dimensions: 1200x628
  style: blog hero, professional, modern minimal
  primary color: [HEX từ colors.md]
  headline text: "[user headline]"
  subheadline: "[user subhead]"
  headline font: [font từ fonts.md]
  composition: text left 60%, visual right 40%, logo bottom-right
  visual element: [illustration/photo/icon theo content]
  background: neutral light with subtle primary accent
```
