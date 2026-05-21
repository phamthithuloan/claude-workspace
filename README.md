# claude-workspace — Seongon Content

Workspace Claude Code cho team Seongon Content, gồm 2 agents điều phối 4 skills cho pipeline content + visual của bài SEO.

## Cấu trúc

```
.claude/
├── agents/
│   ├── viet-bai.md     # Content pipeline: review-outline → upgrade → write-content
│   └── lam-anh.md      # Visual pipeline: chen-anh-bai (stock) + brand-image (brand-specific)
└── skills/             # Đúng 4 skills phục vụ 2 agents (BTVN scope)
    ├── review-outline/ # Chấm outline/bài CTV theo rubric 7 chiều (≥56/70 → ACCEPT)
    ├── write-content/  # Lên outline / viết bài SEO vượt top 10 Google
    ├── chen-anh-bai/   # Chèn ảnh tự động vào Google Doc (stock-first, gen khi brand)
    └── brand-image/    # Hero / infographic / OG theo brand guideline qua Canva MCP

outputs/                  # Output từ giao việc cho agents (BTVN output #3)
├── viet-bai-2026-05-21/  # 5 files: review + outline ACCEPT + draft 3,608 từ + final
└── lam-anh-2026-05-21/   # 4 files: visual analysis + insertion plan + brand briefs
```

> Note: 28 skills SEO/utility khác (seo-audit, seo-page, create-skill, ...) đã move sang `~/.claude/skills/` (user-level global) để vẫn dùng được mọi project mà không làm bụi repo BTVN.

## 2 Agents

### `viet-bai` — Content pipeline

Nhận outline (file / text / Google Doc URL) + keyword chính → duyệt qua `review-outline` → nếu chưa ACCEPT thì patch loop (tối đa 3 vòng) → chuyển qua `write-content` xuất outline final / draft hoàn chỉnh.

**Trigger:** "duyệt rồi viết", "review xong viết bài", "nâng cấp outline rồi viết".

### `lam-anh` — Visual pipeline

Nhận Google Doc URL + keyword + brand → phân loại anchor vào 3 bucket (stock / brand-in-article / brand-asset standalone) → điều phối `chen-anh-bai` (in-article images) + `brand-image` (hero / infographic) → output 1 visual brief duy nhất.

**Trigger:** "chèn ảnh + tạo hero", "minh họa bài + làm infographic", "visual cho bài SEO".

## 4 Skills tham chiếu

| Skill | Mục đích |
|---|---|
| `review-outline` | Chấm outline theo rubric 7 chiều (search intent, depth, structure, E-E-A-T, originality, internal links, CTA). Verdict ACCEPT/REVISE/REJECT. |
| `write-content` | Lên outline hoặc viết draft đầy đủ, vượt top 10 Google ≥ 2 angle. |
| `chen-anh-bai` | Chèn ảnh tự động: đọc Doc qua curl export, tính số ảnh theo wordcount (1 ảnh / 350 từ), fetch Unsplash/Pexels hoặc gen Canva/Gemini, viết caption ≤70 ký tự in nghiêng có keyword. |
| `brand-image` | Tạo hero/social/infographic theo brand guideline (colors + fonts + logo + tone) qua Canva MCP. |

## Cách dùng

```
# Trong Claude Code (CLI hoặc IDE)
# Giao việc bằng ngôn ngữ tự nhiên, Claude (main) tự delegate sang agent:

"Duyệt outline này rồi viết bài giúp em, keyword 'cách chăm sóc mắt': <paste outline>"
→ Claude spawn agent viet-bai

"Chèn ảnh + tạo hero theo brand Hồng Ngọc cho Doc này: <Doc URL>"
→ Claude spawn agent lam-anh
```

Hoặc gõ `/agents` trong Claude Code session để xem danh sách + chọn template prompt.

## Demo output BTVN buổi 4

| Folder | Files | Note |
|---|---|---|
| `outputs/viet-bai-2026-05-21/` | review-1.md, outline-final.md, upgrades-applied.md, draft.md (3,608 từ), final.md | Outline "cách chăm sóc mắt v2" → score 62/70 ACCEPT → draft YMYL hoàn chỉnh |
| `outputs/lam-anh-2026-05-21/` | visual-analysis.md, insertion-plan.md, brand-asset-briefs.md, visual-brief.md | 10 visual notes: 4 stock Pexels confirmed + 6 brand asset brief (Canva MCP không khả dụng trong subagent) |

## License

MIT cho 2 agents + 4 skills custom của Seongon Content.
