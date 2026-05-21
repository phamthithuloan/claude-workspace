# Brand Asset Briefs — Manual Canva Generation

**Note:** Canva MCP không khả dụng trong subagent session này. Tất cả Bucket B + C được mô tả thành design brief đầy đủ để user gen manual tại canva.com hoặc trong future session có Canva MCP.

**Brand constants (áp dụng cho tất cả):**
- Primary color: `#138C3D` (Hồng Ngọc Green)
- Accent color: `#D32F2F` (Hồng Ngọc Red)
- Background neutral: `#FFFFFF` hoặc `#F8F8F8`
- Font heading: Be Vietnam Pro Bold / SemiBold
- Font body: Be Vietnam Pro Regular / Medium
- Logo: BV Hồng Ngọc — đặt góc dưới phải, clear space tối thiểu = 1× chiều cao chữ logo
- Style tổng quan: medical professional, clean, warm tone, không quá clinical-cold

---

## BUCKET C — Standalone Assets (4 items)

### C1 — Hero Blog (V1)

**ID:** Hero-V1
**Type:** Hero blog header
**Dimensions:** 1200 × 628 px (landscape OG/blog standard)
**Used at:** Đầu bài, trước mục lục — thay cho `<!-- VISUAL: Hero image -->`

**Content brief:**
- Headline chính (overlay text): **"10 Cách Chăm Sóc Mắt"** — font Be Vietnam Pro Bold, white, ~72pt
- Sub-headline: **"Hướng dẫn từ BS Chuyên khoa Mắt Hồng Ngọc"** — font Regular, white, ~28pt
- Background: Ảnh bác sĩ đang khám mắt cho bệnh nhân (warm clinic setting, slit lamp) — màu overlay gradient dark green (#138C3D, 40% opacity) từ trái sang phải
- Nếu không có ảnh gốc BV: dùng stock Pexels eye exam (https://images.pexels.com/photos/6749698/pexels-photo-6749698.jpeg) làm background
- Logo Hồng Ngọc: góc dưới phải, white version, height ~40px
- Badge "2026" hoặc ngày cập nhật: góc trên phải, rounded pill #D32F2F

**Canva steps (manual):**
1. Canva → Create design → Custom size 1200×628
2. Background: upload stock Pexels 6749698 hoặc ảnh BV gốc
3. Add overlay: Rectangle full-width, color #138C3D, opacity 40-50%
4. Text layer headline + sub-headline (màu white, font Be Vietnam Pro)
5. Logo Hồng Ngọc white version góc dưới phải
6. Export PNG

**Alt text:** "10 cách chăm sóc mắt sáng khỏe — hướng dẫn từ bác sĩ chuyên khoa Mắt Hồng Ngọc 2026"

---

### C2 — Infographic Dinh Dưỡng AREDS2 (V6)

**ID:** Infographic-V6
**Type:** In-article infographic (square)
**Dimensions:** 1080 × 1080 px
**Used at:** Sau bảng thực phẩm lutein (dòng 108) — thay cho `<!-- VISUAL: Infographic "1 ngày dinh dưỡng cho mắt" -->`

**Content brief:**
- Title: **"1 Ngày Dinh Dưỡng Bảo Vệ Mắt"** — Be Vietnam Pro Bold, #138C3D, ~36pt
- Subtitle: **"Theo công thức AREDS2"** — Regular, #333, ~20pt
- Layout: 3 cột dọc hoặc 3 hàng — Sáng / Trưa / Chiều

  **Sáng:**
  - Icon: trứng + cải bó xôi
  - Text: "Trứng (lòng đỏ) + cải bó xôi xào"
  - Badge: "Lutein ~7mg"

  **Trưa:**
  - Icon: cá hồi + cải xoăn
  - Text: "Cá hồi + cải xoăn (kale)"
  - Badge: "Omega-3 + Lutein ~10mg"

  **Chiều:**
  - Icon: ngô + đậu Hà Lan
  - Text: "Ngô vàng + đậu Hà Lan"
  - Badge: "Lutein ~3mg"

- Footer: "Mục tiêu: 10mg lutein + 2mg zeaxanthin mỗi ngày (AREDS2)"
- Footer-right: Logo Hồng Ngọc + "hongngochospital.vn"
- Color scheme: Background white, card background #F0FAF3 (light green tint), badge text white on #138C3D, accent dots #D32F2F

**Canva steps:**
1. Create design 1080×1080
2. Background #FFFFFF, title bar top với #138C3D strip
3. 3 card rectangles với #F0FAF3, rounded corners 12px
4. Icon: dùng Canva icon library (food icons) hoặc emoji flat-style
5. Badge pill: background #138C3D, text white
6. Footer strip bottom: background #138C3D, logo white + URL white

**Alt text:** "Công thức dinh dưỡng tốt cho mắt theo AREDS2: lutein 10mg mỗi ngày qua trứng, cá hồi, cải xoăn"

---

### C3 — Infographic Red Flags (V9)

**ID:** Infographic-V9
**Type:** In-article infographic (square)
**Dimensions:** 1080 × 1080 px
**Used at:** Sau danh sách 6 dấu hiệu red flag (sau dòng 313) — thay cho `<!-- VISUAL: Infographic "Red flags 6 dấu hiệu đi khám ngay" -->`

**Content brief:**
- Title: **"6 Dấu Hiệu Mắt Cần Đi Khám Ngay"** — Be Vietnam Pro Bold, white trên #D32F2F header bar
- Subtitle: **"Đừng chờ — mỗi giờ đều quan trọng"** — Regular, white, italic
- Layout: 2×3 grid (6 cards), mỗi card gồm:
  1. 🚨 Icon mắt/cảnh báo + **"Mờ mắt đột ngột"** — sub: "Không hồi phục sau nghỉ"
  2. 🚨 **"Đau mắt dữ dội + buồn nôn"** — sub: "Nghi tăng nhãn áp cấp — cấp cứu"
  3. 🚨 **"Chớp sáng / ruồi bay tăng nhanh"** — sub: "Nghi bong võng mạc"
  4. 🚨 **"Nhìn đèn có quầng sáng"** — sub: "Nghi glaucoma góc đóng"
  5. 🚨 **"Chấn thương mắt"** — sub: "Kể cả nhẹ: UV hàn xì, dị vật"
  6. 🚨 **"Đỏ mắt > 3 ngày"** — sub: "Kèm chảy nước mắt/mủ/nhạy sáng"
- Card style: background white, border-left 4px solid #D32F2F, title #D32F2F Bold, sub-text #555 Regular
- Footer: "Hotline Hồng Ngọc: 0919 489 258" | Logo góc dưới phải

**Alt text:** "6 dấu hiệu mắt cần đi khám bác sĩ ngay — sai lầm khi nhỏ mắt tự xử lý tại nhà có thể mất thị lực"

---

### C4 — Timeline Khám Mắt Theo Tuổi (V10)

**ID:** Timeline-V10
**Type:** In-article infographic (square)
**Dimensions:** 1080 × 1080 px
**Used at:** Đầu section H2-3 / trước FAQ (dòng 315-322) — thay cho `<!-- VISUAL: Timeline khám mắt theo độ tuổi -->`

**Content brief:**
- Title: **"Lịch Khám Mắt Định Kỳ Theo Tuổi"** — Bold #138C3D
- Subtitle: **"Theo khuyến nghị Bộ Y tế Việt Nam"** — Regular #555, italic
- Layout: 4 horizontal timeline steps với connector line (màu #138C3D)

  **Step 1 — Icon người trẻ:**
  - "18–39 tuổi"
  - Badge: "2–4 năm/lần"

  **Step 2 — Icon người trưởng thành:**
  - "40–54 tuổi"
  - Badge: "1–3 năm/lần"

  **Step 3 — Icon người trung niên:**
  - "55–64 tuổi"
  - Badge: "1–2 năm/lần"

  **Step 4 — Icon người cao tuổi:**
  - "Từ 65 tuổi"
  - Badge: "Mỗi năm 1 lần"

- Warning box bên dưới (border #D32F2F): "Khám hằng năm bất kể tuổi nếu: đeo lens áp tròng, tiền sử gia đình glaucoma, tiểu đường, tăng huyết áp"
- Footer: Logo Hồng Ngọc + "Đặt lịch khám: hongngochospital.vn"
- Badge style: rounded pill, background #138C3D, text white, Bold

**Alt text:** "Lịch khám mắt định kỳ theo tuổi: từ 18 đến 65+ theo khuyến nghị Bộ Y tế Việt Nam"

---

## BUCKET B — Brand-in-Article (2 items)

### B1 — Sơ đồ Tư Thế Ngồi Màn Hình (V4)

**ID:** Diagram-V4
**Type:** In-article comparison diagram
**Dimensions:** 1200 × 600 px (landscape, 2:1 ratio) hoặc 1080×720
**Used at:** Sau đoạn thiết lập màn hình (dòng 63) — thay cho `<!-- VISUAL: Sơ đồ tư thế ngồi máy tính -->`

**Content brief:**
- Title: **"Tư Thế Ngồi Máy Tính Đúng Cách"** — Be Vietnam Pro Bold, #138C3D
- Layout: 2 panel side-by-side

  **Panel trái — "ĐÚNG" (green checkmark):**
  - Illustration người ngồi thẳng
  - Arrow annotation: "50–70 cm" (khoảng cách mắt-màn hình)
  - Arrow: "Mắt nhìn vào 1/3 trên màn hình"
  - Arrow: "Màn hình thấp hơn tầm mắt ~15°"
  - Background tint: #F0FAF3
  - Header badge: ✓ ĐÚNG — green #138C3D

  **Panel phải — "SAI" (red X):**
  - Illustration người cúi sát màn hình
  - Arrow annotation: "< 30 cm — quá gần!"
  - Arrow: "Màn hình ngang mắt — gây khô mắt"
  - Background tint: #FFF5F5
  - Header badge: ✗ SAI — red #D32F2F

- Divider center: dashed line màu #CCCCCC
- Footer note: "Chuẩn AAO: 50–70 cm | Mắt nhìn 1/3 trên | Màn hình thấp hơn 15°"
- Logo Hồng Ngọc: góc dưới phải, small version

**Alt text:** "Thiết lập màn hình đúng chuẩn: khoảng cách mắt 50-70cm, nhìn vào 1/3 trên màn hình"

---

### B2 — Sơ Đồ 3 Huyệt Vị (V7)

**ID:** Diagram-V7
**Type:** In-article annotated diagram
**Dimensions:** 1080 × 1080 px (square) hoặc 900×700 (landscape)
**Used at:** Sau danh sách huyệt vị và trước warning box (dòng 149) — thay cho `<!-- VISUAL: Sơ đồ 3 huyệt Tình Minh — Toản Trúc — Thái Dương -->`

**Content brief:**
- Title: **"Bài Tập Chăm Sóc Mắt: 3 Huyệt Giảm Mỏi"** — Be Vietnam Pro Bold, #138C3D
- Illustration: khuôn mặt front-view (line art style hoặc flat illustration), gender-neutral
- 3 annotation dots + leader lines:

  **Dot 1 — Tình Minh (Jingming):**
  - Vị trí: góc trong khóe mắt, sát sống mũi
  - Label: "① Tình Minh — giảm đỏ mắt"
  - Color: dot #138C3D

  **Dot 2 — Toản Trúc (Cuanzhu):**
  - Vị trí: đầu trong lông mày, chỗ giáp sống mũi
  - Label: "② Toản Trúc — giảm nhức trán"
  - Color: dot #138C3D

  **Dot 3 — Thái Dương (Taiyang):**
  - Vị trí: hõm thái dương hai bên, giữa đuôi mắt và chân tóc mai
  - Label: "③ Thái Dương — giảm đau đầu"
  - Color: dot #138C3D

- Instruction box bên dưới (background #F0FAF3, border #138C3D):
  "Ấn nhẹ 5 giây × 5 lần mỗi huyệt | Tổng ~5 phút | Làm cuối giờ trưa hoặc trước ngủ"
- Warning strip (background #FFF3CD, border #FF8C00):
  "⚠ Không massage khi đang viêm kết mạc cấp hoặc vừa phẫu thuật mắt"
- Logo Hồng Ngọc: góc dưới phải

**Illustration tip:** Sử dụng Canva Elements → search "face anatomy" hoặc "face outline" → chọn flat/line art style → thêm annotation dots manually.

**Alt text:** "Bài tập chăm sóc mắt: vị trí 3 huyệt Tình Minh, Toản Trúc, Thái Dương để giảm mỏi mắt"

---

## Checklist trước khi gen manual

- [ ] Đã có logo Hồng Ngọc file local (white version + color version)
- [ ] Đã cài font Be Vietnam Pro trong Canva (Add font → Google Fonts → "Be Vietnam Pro")
- [ ] Colors đã lưu vào Brand Kit Canva: #138C3D, #D32F2F, #FFFFFF, #F0FAF3
- [ ] Export PNG (không JPG cho infographic có text để giữ nét)
- [ ] File naming: `hongngoc-[asset-id]-[date].png` (vd: `hongngoc-hero-v1-20260521.png`)
