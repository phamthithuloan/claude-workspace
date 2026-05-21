# viet-bai pipeline — cách chăm sóc mắt

**Agent**: `viet-bai`
**Run date**: 2026-05-21
**Client**: Bệnh viện Hồng Ngọc — bài YMYL chuyên khoa Mắt
**Outline input**: `Outline - Cach cham soc mat v2 - 2026-05-18.md` (367 dòng, v2)

---

## Summary

| Metric | Value |
|---|---|
| **Initial review** | 62/70 — **ACCEPT** (lần đầu) |
| **Final review** | 62/70 — ACCEPT (không cần re-review) |
| **Upgrade rounds** | 0 (verdict ACCEPT lần đầu) |
| **Draft word count** | 3,608 từ (target 3,000–3,300; vượt ~10% giữ chất lượng informational) |
| **Keyword density** | 4 mention "cách chăm sóc mắt" / ~0.33% (an toàn dưới 1%) |
| **YMYL compliance** | ✓ Trust signal + 6 citation source (AAO, AREDS2, Cochrane, Lancet, Mayo Clinic, Bộ Y tế VN) + 2 Warning box |
| **CTA placements** | 3 (sau Nhóm A — sau Sai lầm #3 — cuối FAQ) |
| **Visual notes** | 10 marker `<!-- VISUAL -->` chờ agent `lam-anh` |
| **CTV placeholders** | 10 marker `[CTV xin info BV Hồng Ngọc]` cho author/reviewer/ngày/số ca |

---

## Pipeline steps đã chạy

### Bước 1 — review-outline ✓

- Skill `review-outline` chạy trên outline gốc
- Score 62/70 (search intent 9 + depth 9 + structure 8 + E-E-A-T 8 + originality 9 + readability 9 + CTA 10)
- 5 issues phát hiện = **gap fix trước publish** (không phải lỗi cấu trúc cần rebuild)
- Output: `review-1.md`

### Bước 2 — decide branch ✓

- Verdict ACCEPT (≥56/70) → **skip upgrade loop**, tiến trực tiếp sang Bước 4
- Output: `upgrades-applied.md` (0 vòng upgrade, lý do + 5 issues handoff cho CTV)

### Bước 3 — upgrade loop ⏭ skipped

- Không kích hoạt vì ACCEPT lần đầu

### Bước 4 — write-content ✓

- Skill `write-content` chạy trên outline ACCEPT (general-purpose agent delegate vì viet-bai subagent không có MCP Drive — output markdown thẳng thay vì save Doc)
- Expand outline → draft 3,608 từ
- Apply 5 fixes inline (TOC + H3 kính áp tròng + Warning box ×2 + AAO citation + placeholder marker rõ)
- Giữ 10 visual notes cho pipeline visual sau
- Output: `draft.md`

---

## File outputs

| File | Size | Mục đích |
|---|---|---|
| `review-1.md` | 5.4 KB | Output review vòng 1: score + 3 strengths + 5 issues + verdict ACCEPT |
| `outline-final.md` | 22 KB | Outline ACCEPT (= outline gốc, vì pass lần đầu, không upgrade) |
| `upgrades-applied.md` | 1.2 KB | Audit trail: 0 vòng upgrade, mapping 5 issues → CTV owner |
| `draft.md` | 28 KB | Bài viết hoàn chỉnh 3,608 từ, sẵn handoff CTV fill placeholder + agent `lam-anh` chèn ảnh |
| `final.md` | this file | Consolidate report |

Folder path: `/Users/phamthuloan/Documents/claude-workspace/outputs/viet-bai-2026-05-21/`

---

## Handoff next steps

1. **CTV fill 10 placeholder** `[CTV xin info BV Hồng Ngọc]` — author/reviewer/ngày/số ca/năm kinh nghiệm
2. **Verify 2 citation DOI** trong Reference section (Lancet 2022 + Blinking exercises) — agent đã đánh dấu `[CTV verify DOI]`
3. **Chạy agent `lam-anh`** trên Doc final để chèn 10 ảnh (8 stock + 1 hero brand + 1 infographic brand) — cần Google Doc URL sau khi CTV fill xong
4. **Final E-E-A-T review** trước publish: medical reviewer sign-off + author bio link tới trang BS trên website BV Hồng Ngọc

---

## Honest notes

- Subagent `viet-bai` chỉ chạy được Bước 1 (review) trong session đầu do em không có `SendMessage` tool để continue subagent multi-turn — em handle Bước 2/3/4 manually + delegate write phần draft cho general-purpose agent. Output files đầy đủ, đúng spec agent definition.
- Word count vượt target ~10% (3,608 vs upper 3,300) — agent honest report, có thể trim 300 từ nếu cần strict nhưng sẽ hy sinh dosage/citation/warning detail. **Recommend giữ nguyên** vì YMYL.
- 2 DOI và quote BS giữ placeholder thay vì fabricate — đúng anti-pattern "KHÔNG bịa khi fail".
