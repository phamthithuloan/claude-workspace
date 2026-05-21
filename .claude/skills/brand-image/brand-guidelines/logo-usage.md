# Logo usage — Hồng Ngọc Hospital

## Logo assets

| Version | Use case | File / Asset |
|---|---|---|
| **Primary (color)** | Background sáng (white, Gray 50), hero, infographic body | Local cache: `/Users/phamthuloan/.claude/image-cache/a9cb0fb2-8c05-4927-b1e9-84889fa947a9/10.png` (user shared 2026-05-18) |
| Reversed (white) | Background tối (Green #138C3D), Red Flags warning section | **[FILL IN — TODO]** — chưa có. User cần xuất từ Canva hoặc tools khác |
| Mark only (icon) | Favicon, watermark nhỏ < 60px | **[FILL IN — TODO]** |
| Wordmark only | Banner ngang | **[FILL IN — TODO]** |

## Workflow upload logo lên Canva

Logo file user share đang ở **local cache** của Claude — KHÔNG public URL → Canva không thể download trực tiếp.

**Cách upload lên Canva** (user làm thủ công 1 lần, lưu cố định):

1. Mở Canva account đang connect (account agency hiện tại)
2. Vào **Brand Hub** → **Logos** → **Upload** → chọn file logo (`.png` user share)
3. Sau khi upload, logo có **Canva asset ID** — có thể dùng trong design
4. **Cách 2**: hoặc tạo Brand Kit mới tên "Hồng Ngọc" → upload logo vào đó → tôi sẽ thấy qua `list-brand-kits` lần sau
5. **Cách 3 (đơn giản nhất cho generate flow)**: sau khi tôi `generate-design` xong, mở design trong Canva → drag-drop logo từ Uploads vào vị trí bottom-right → save

## Placement rules (sau khi user add logo)

### Hero (1200×628)
- Position: **bottom-right**
- Size: width = **120px** (≈ 10% canvas width)
- Padding from edge: **32px**
- Version: **primary (color)** vì hero thường nền sáng

### Social square (1080×1080)
- Position: **top-left** HOẶC **bottom-center**
- Size: width = **140px**
- Padding: **48px**
- Version: primary nếu nền sáng, white version nếu nền green/red

### Infographic vertical (1080×1920)
- Position: **top-left** (header) + có thể duplicate ở **bottom-center** (footer)
- Size: width = **180px** (lớn hơn vì canvas dài)
- Padding: **64px**
- Version: primary
- **Đặc biệt Red Flags infographic**: vì nền sẽ dominant red, nên **logo dùng version trắng/reversed** — nếu chưa có, dùng primary trên 1 block white nhỏ làm "frame" 100×100px

## Clear space

Logo Hồng Ngọc có cấu trúc: vương miện xanh + chữ "HONG NGOC" trên đỏ + "Hospital" script.

Clear space tối thiểu quanh logo = **chiều cao chữ "HONG NGOC"** trong logo.

```
┌──────────────────┐
│                  │  ← clear space ≥ X-height
│   [LOGO]         │
│                  │
└──────────────────┘
```

KHÔNG đặt text / element / dấu chấm decoration trong vùng clear space.

## Cấm

- KHÔNG stretch / squash logo — luôn giữ tỉ lệ aspect ratio
- KHÔNG xoay logo
- KHÔNG đổi màu logo (vd: bỏ đỏ, đổi xanh nhạt) — dùng đúng version primary hoặc reversed
- KHÔNG đặt logo trên photo cluttered → dùng overlay tối/sáng tăng contrast ≥ 3:1
- KHÔNG đặt 2 logo cạnh nhau (trừ partnership có guideline riêng)
- KHÔNG dùng logo Hồng Ngọc trên content không liên quan BV (vd quảng cáo thuốc third-party)
- KHÔNG thêm drop shadow / glow effect lên logo

## Đặc biệt cho YMYL content y tế

- Logo PHẢI xuất hiện rõ ràng trên image cuối bài (CTA section) → user dễ verify nguồn
- Trên hero image, logo có thể nhỏ (120px) — nhưng PHẢI có → trust signal
- Trên infographic Red Flags, logo PHẢI ở footer → user biết warning đến từ source uy tín
