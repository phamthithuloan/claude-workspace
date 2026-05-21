# Canva Designs đã gen — 2 vòng (v1 + v2)

**Ngày gen:** 2026-05-21 (v1) + 2026-05-22 (v2)
**Brand:** Bệnh viện Hồng Ngọc
**Logo asset Canva:** `MAHKUTEKxkI` (upload từ Drive https://drive.google.com/file/d/1eGVlYtX8g2fSgTBUR9PboG6YOLrr28Tb)
**Total assets v2:** 6 PNG + 6 Canva edit URL

---

## Vòng 2 (v2) — Re-gen với logo asset_id + prompts cải thiện

| # | Asset | PNG local | Canva edit URL | Quality | Issue còn lại |
|---|---|---|---|---|---|
| 1 | Hero blog | `v2-hero.png` (851×315) | [edit](https://www.canva.com/d/YewgWpHV7PxC9NQ) | ✅ Khá | Logo không insert (Canva AI bỏ qua asset_id), "reallygreatsite.com" placeholder |
| 2 | C2 AREDS2 dinh dưỡng | `v2-c2-areds2.png` (800×2000) | [edit](https://www.canva.com/d/W8NYg8WEGrLz9d2) | ✅ Khá | Title "DINH DƯỠNG BẢO VỆ MẮT" bị crop, logo không insert |
| 3 | C3 Red Flags 3 dấu hiệu | `v2-c3-red-flags.png` (1587×2245) | [edit](https://www.canva.com/d/5HzF-UBojxqSVXh) | ✅ Tốt | Logo apply (text "HOSPIC HOSPITAL" typo) |
| 4 | C4 Timeline khám theo tuổi | `v2-c4-timeline.png` (2000×2000) | [edit](https://www.canva.com/d/YEkvWBSChurA3Ei) | ❌ Tệ | Table rác text — Canva AI không gen được tabular content đúng |
| 5 | B1 Tư thế ngồi máy tính | `v2-b1-posture.png` (1587×2245) | [edit](https://www.canva.com/d/4u4lBJ0EIx6ETjE) | ⚠️ | Có 2 panel posture nhưng "21 tháng 5, 10:00 sáng" placeholder không liên quan |
| 6 | B2 3 huyệt giảm mỏi | `v2-b2-acupressure.png` (559×397) | [edit](https://www.canva.com/d/5eT0nLgIBvVYgDL) | ⚠️ | 3 cards structure OK, nhưng tên huyệt sai (BẤM HUYỆT SỐ 1/SỐ 2 thay vì Tình Minh/Toản Trúc/Thái Dương) |

---

## Vòng 1 (v1) — Gen không logo, prompts complex

Giữ làm reference + audit trail. Xem file PNG v1: `hero-v1-candidate1.png`, `c2-areds2-nutrition.png`, `c3-red-flags.png`, `c4-timeline-age.png`, `b1-posture.png`, `b2-acupressure.png`.

| # | v1 Edit URL | v1 Quality |
|---|---|---|
| Hero | [edit](https://www.canva.com/d/rZxTE3FU6tX2UVW) | "CHĂM SÓC MẮT" only (mất "10 CÁCH") |
| C2 | [edit](https://www.canva.com/d/fCv7G9WfBwA5SQU) | 1 plate generic (mất 3 cards Sáng/Trưa/Chiều) |
| C3 | [edit](https://www.canva.com/d/Lpqpl3iS0TqqYI3) | Chỉ số "6" + quote rỗng |
| C4 | [edit](https://www.canva.com/d/iDaaet71W6pp5CZ) | Timeline icons rỗng |
| B1 | [edit](https://www.canva.com/d/-1NVdxR1axIcJvT) | 2 ảnh người ngồi giống nhau |
| B2 | [edit](https://www.canva.com/d/Wpxnr3JnKfIUaV-) | Cards generic không có face anatomy |

---

## v2 vs v1 — Improvement

| Asset | v1 → v2 Δ |
|---|---|
| Hero | ⬆️ Headline full "10 CÁCH CHĂM SÓC MẮT" + sub "HƯỚNG DẪN TỪ BỆNH VIỆN HỒNG NGỌC" + BS slit-lamp Asian male (v1 chỉ "CHĂM SÓC MẮT") |
| C2 | ⬆️ 3 thực phẩm thẳng row với captions đúng lutein mg + omega-3 (v1 chỉ 1 plate) |
| C3 | ⬆️ 3 warning cards thật + hotline 0919 489 258 (v1 rỗng nội dung) |
| C4 | ↔ Vẫn tệ (Canva AI không gen được tabular schedule với badges) |
| B1 | ⬆️ Có 2 panel DISTACT/POSTURE + VS divider (v1 không có comparison) |
| B2 | ⬆️ Có 3 numbered cards + warning box (v1 không có structure) |

---

## Limitations đã phát hiện

1. **Logo asset_id không guarantee insert** — Canva AI tự decide insert hay không. 4/6 v2 có logo (text-form), 2/6 không có.
2. **Tabular content (C4 schedule)** — Canva AI không gen table đúng dòng/cột, ra rác text. Cần dùng editing-transaction để add table manual.
3. **Specific brand text (tên thuốc, huyệt vị, dosage)** — AI hallucinate hoặc rút gọn. Cần fix qua Canva editor.
4. **Placeholder Canva template content** — Khi gen "poster" / "facebook_cover", Canva insert sẵn date/time placeholder ("21 tháng 5, 10:00 sáng") không relate brief.

## Khuyến nghị fix v3

Cho 3 asset quality chấp nhận được (Hero, C2, C3) — chấp nhận và edit nhẹ Canva editor:
- Hero: xoá "reallygreatsite.com", paste logo qua Uploads tab
- C2: fix title position, paste logo
- C3: sửa text "HOSPIC" → "HOSPITAL"

Cho 3 asset cần rebuild (C4, B1, B2):
- C4: dùng editing-transaction MCP để add table 4 rows × 3 cols manual
- B1: xoá content placeholder Canva template
- B2: sửa tên huyệt qua Canva editor

---

## Audit trail v2

- Logo upload job: f19b2825-53db-4cde-ac24-a2958749aec9 (failed Drive uc URL) → cba50d20-0bee-4639-8c97-7cc181c54a18 (success Google CDN URL `lh3.googleusercontent.com/d/<id>`)
- Logo asset ID Canva: `MAHKUTEKxkI` (image 400×400 PNG, smart_tags: medical, health, healthcare)
- v2 design IDs: DAHKUXzNx8A (Hero), DAHKUc5mGp4 (C2), DAHKUWap9P8 (C3), DAHKUeBe0T8 (C4), DAHKUf6HVww (B1), DAHKUc1Bmg8 (B2)
- v2 generate-design jobs: 023c6c3b (Hero), a9fac38e (C2), 15cd33a7 (C3), 808877b4 (C4), 312f34db (B1 retry poster), 6f9e70b3 (B2)

## Brand Guidelines PDF Drive

- URL: https://drive.google.com/file/d/1eX7JbrbnoQ0Bm73SojuKnxiLIcjxJzXN/view
- Status: fetch via `curl uc?export=download` returned HTML warning ("Can't download file") — cần authenticated session để fetch. Em chưa truy cập được nội dung PDF để verify brand spec chính xác hơn so memory.
