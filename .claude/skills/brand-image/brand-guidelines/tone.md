# Visual tone & voice — Hồng Ngọc

> Định nghĩa ngày 2026-05-18 từ phân tích logo + ngành y tế Việt. Refine khi có brand book chính thức.

## Brand tone spectrum

```
Corporate    ──────●──────────────────────────  Playful
Minimal      ────────●────────────────────────  Maximalist
Serious      ────────●────────────────────────  Bold
Traditional  ──────────────●──────────────────  Modern
Cool / blue  ────────────────────────●────────  Warm / red
Tech         ────────────────────●────────────  Human
```

→ **Định vị**: corporate-leaning, minimal-modern, serious với touch human warmth. Cân bằng giữa "đáng tin cậy như bệnh viện lớn" và "gần gũi như BS gia đình".

## Voice keywords

3-5 từ mô tả tone chính:

- **Tin cậy** (trustworthy) — top priority cho YMYL y tế
- **Chuyên nghiệp** (professional) — không suồng sã
- **Gần gũi** (approachable) — không hàn lâm xa cách
- **Sắc bén** (sharp / clear) — thông tin chính xác, không vòng vo
- **Ấm áp** (warm) — touch cảm xúc, không lạnh lùng như AI

## Visual mood

| Element | Direction |
|---|---|
| **References to look like** | Mayo Clinic (clean, trustworthy), Cleveland Clinic (modern medical), Vinmec.vn (corporate Việt) |
| **References to AVOID** | Phòng khám tư nhân nhỏ (chợ-style), bệnh viện công cũ (formal-stale), spa làm đẹp (playful-overhyped) |
| **Style keywords** | Clean, spacious, professional, warm-but-not-cute |

## Photo / illustration direction

| Element | Direction |
|---|---|
| **People** | Real photo, bác sĩ thật mặc áo blu trắng, smile nhẹ (không over-pose). Bệnh nhân: candid, đa dạng độ tuổi (đặc biệt người trung niên — primary persona). KHÔNG stock photo "doanh nhân cười với laptop" |
| **Medical scenes** | Phòng khám sạch sẽ, ánh sáng tự nhiên, thiết bị hiện đại nhưng không quá tech-cold |
| **Product (thiết bị mắt)** | Flat lay top-down, background neutral light, không photoshop quá đà |
| **Abstract / shapes** | Geometric đơn giản (circle, rounded rectangle), subtle gradient từ light green → primary green. KHÔNG blob organic shapes |
| **Icons** | Line style 2px stroke, consistent set (đề xuất: Phosphor Icons hoặc Tabler Icons — đều free, có y tế icons). KHÔNG mix style flat + line + 3D |
| **Eye-specific imagery** | Eye anatomy minimal-realistic (không cartoon over-cute). Diagrams clean với labels rõ. Photo mắt real (không close-up gây sợ) |

## Đặc biệt cho Red Flags / warning content

- **Tone**: serious-urgent NHƯNG không hoang mang
- **Color emphasis**: Red (#D32F2F) ở icon ⚠️, NHƯNG không full red background (gây panic)
- **Typography**: Bold weight cao (700–800), letter spacing chặt
- **No smiling faces** trên warning content
- **CTA**: clear-direct ("Đi khám ngay" thay vì "Bạn có muốn đặt lịch không?")

## Cấm tone-wise

- KHÔNG emoji trang trí (😊 ❤️ ✨) — y tế cần serious. EXCEPT: ⚠️ 🚨 ✓ ✗ cho warning/checklist là OK
- KHÔNG stock photo lộ liễu "bác sĩ cầm bảng tablet cười nhe răng"
- KHÔNG gradient hồng tím / cam neon — không match ngành y tế
- KHÔNG drop shadow / inner shadow / glow nặng — flat hoặc subtle elevation max
- KHÔNG comic-style / hand-drawn illustration (trừ children content, có guideline riêng)
- KHÔNG religious / superstition symbols (kiểu chữa bệnh tâm linh)

## Mood-to-Canva-prompt mapping

Khi gọi Canva `generate-design`, dùng tone keywords sau trong prompt:

```
"[image type] for medical content about [topic],
professional minimal modern medical clinic aesthetic,
clean spacious layout with generous white space,
primary color #138C3D (Hồng Ngọc green), accent #D32F2F (medical red),
typography: Be Vietnam Pro bold for headlines,
mood: trustworthy professional warm but serious,
style references: Mayo Clinic, Cleveland Clinic infographic,
NOT cartoon, NOT playful, NOT overly stylized"
```

## Content type → tone mapping

| Content | Tone dominant | Tone secondary |
|---|---|---|
| Hero blog 10 cách chăm sóc mắt | Warm + approachable | Trustworthy |
| Infographic 20-20-20 | Clean + actionable | Friendly |
| **Infographic Red Flags 6 dấu hiệu** | **Serious + urgent** | Trustworthy |
| Sơ đồ massage huyệt | Educational + clear | Warm |
| Timeline khám mắt | Informative + structured | Professional |
| Bài giới thiệu BV (about us) | Authority + premium | Warm |
