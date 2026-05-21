# Canva Designs đã gen (preview-able)

**Ngày gen:** 2026-05-21
**Brand kit Hồng Ngọc:** CHƯA có trong Canva account — gen-design generic theo prompt
**Total assets:** 6 PNG + 6 Canva edit URL

---

## Quality summary

| # | Asset | PNG local | Canva edit URL | Quality | Issue chính |
|---|---|---|---|---|---|
| 1 | Hero blog V1 | `hero-v1-candidate1.png` (851×315) | [edit](https://www.canva.com/d/rZxTE3FU6tX2UVW) | ✅ Decent | Mất "10 CÁCH" + "Hồng Ngọc" + badge 2026 |
| 2 | C2 AREDS2 dinh dưỡng | `c2-areds2-nutrition.png` (559×397) | [edit](https://www.canva.com/d/fCv7G9WfBwA5SQU) | ⚠️ Generic | Mất 3 cards Sáng/Trưa/Chiều + lutein badges |
| 3 | C3 Red Flags 6 dấu hiệu | `c3-red-flags.png` (880×2000) | [edit](https://www.canva.com/d/Lpqpl3iS0TqqYI3) | ❌ Tệ | Không có 6 cards cụ thể, chỉ số "6" + quote rỗng |
| 4 | C4 Timeline khám theo tuổi | `c4-timeline-age.png` (2000×2000) | [edit](https://www.canva.com/d/iDaaet71W6pp5CZ) | ❌ Rỗng | Timeline icons trống, mất 4 steps 18-39/40-54/55-64/65+ |
| 5 | B1 Tư thế ngồi máy tính | `b1-posture.png` (851×315) | [edit](https://www.canva.com/d/-1NVdxR1axIcJvT) | ⚠️ Mất so sánh | 2 ảnh người ngồi nhưng không có 2 cột ĐÚNG/SAI |
| 6 | B2 3 huyệt giảm mỏi mắt | `b2-acupressure.png` (800×2000) | [edit](https://www.canva.com/d/Wpxnr3JnKfIUaV-) | ⚠️ Mất anatomy | 3 cards đúng tên, mất face diagram với 3 dots |

---

## 4 candidates Hero (chị compare nếu muốn switch)

Em đã pick candidate #1 — 3 cái còn lại preview tại:

- Candidate 2: https://design.canva.ai/PUfuFWJIFy_Hzfr → edit: https://www.canva.com/d/PXSV2yeNZ4c3j4Z
- Candidate 3: https://design.canva.ai/izIOM9MBfdPp0pQ → edit: https://www.canva.com/d/C-L9bCsINctACmn
- Candidate 4: https://design.canva.ai/-vgoQULrHffwP9O → edit: https://www.canva.com/d/aZI0Va8SinE0_ta

---

## Root cause + cách fix

**Root cause:** Canva `generate-design` MCP là AI text-to-design — ra sketch generic, không enforce được:
- Exact layout (6 cards / 4 steps / 2 column ĐÚNG SAI)
- Annotation arrows + leader lines tới điểm cụ thể (face anatomy)
- Badges với data cụ thể (lutein mg, năm tuổi)
- Brand kit Hồng Ngọc (chưa có brand kit Canva)

**3 cách fix:**

1. **Manual edit Canva** — mở 6 edit URL trên, sửa từng asset theo brief gốc trong `brand-asset-briefs.md`. Thời gian: ~30-60 phút/asset.

2. **Setup brand kit Hồng Ngọc trong Canva trước** — vào Canva → Brand → Add brand kit → upload logo + colors #138C3D #D32F2F + font Be Vietnam Pro. Sau đó re-gen 6 assets với `brand_kit_id` → ảnh có brand identity nhất quán hơn.

3. **Tìm brand template có sẵn trên Canva** — `search-brand-templates` với keyword "medical infographic" / "warning poster" → pick template có layout đúng → fill data via `create-design-from-brand-template`. Output sẽ layout chuẩn.

---

## Audit trail

- Job IDs Canva:
  - Hero: 6c025d49-ca8a-4421-9a75-3eeadb11cd2f
  - C2 AREDS2: ef33f370-1287-43fa-b9c8-870769cf5061
  - C3 Red Flags retry: 680f6b1e-3cb7-4a92-b15c-cf0badd205ed
  - C4 Timeline: 990303f8-90e0-4c66-b35d-3e88f7cc825a
  - B1 Posture: 5aaf61a6-bf85-490c-81a1-7f6ed74f83d9
  - B2 Acupressure retry: 7b4d67b1-9dd7-4a05-b68d-1cbf1b2d9797

- Design IDs Canva: DAHKUIekqDI, DAHKUGXo0HA, DAHKUMX7Uqk, DAHKUCuxsB4, DAHKUGDGtbs, DAHKUO57be8

- Failed first attempts (retry với design_type khác đã thành công):
  - C3 Red Flags lần 1 với `infographic`: failed (Canva returned empty)
  - B2 Acupressure lần 1 với `infographic` và 2 với `poster`: failed → retry 3 với simpler prompt + ASCII tiếng Việt thành công
