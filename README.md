# claude-workspace — Seongon Content

Workspace Claude Code cho team Seongon Content, gồm 2 agents điều phối 4 skills cho pipeline content + visual của bài SEO.

## Cấu trúc

```
.claude/
├── agents/
│   ├── viet-bai.md     # Content pipeline: review-outline → upgrade → write-content
│   └── lam-anh.md      # Visual pipeline: chen-anh-bai (stock) + brand-image (brand-specific)
└── skills/
    ├── review-outline/  # Chấm outline/bài CTV theo rubric 7 chiều (≥56/70 → ACCEPT)
    ├── write-content/   # Lên outline / viết bài SEO vượt top 10 Google
    ├── chen-anh-bai/    # Chèn ảnh tự động vào Google Doc (stock-first, gen khi brand)
    ├── brand-image/     # Hero / infographic / OG theo brand guideline qua Canva MCP
    └── ... (skills SEO khác)
```

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
| `chen-anh-bai` | Chèn ảnh tự động: đọc Doc qua curl export, tính `num_images = max(3, ceil(wordcount/350))`, fetch Unsplash/Pexels hoặc gen Canva/Gemini, viết caption ≤70 ký tự in nghiêng có keyword. |
| `brand-image` | Tạo hero/social/infographic theo brand guideline (colors + fonts + logo + tone) qua Canva MCP. |

## Cách dùng

```bash
# Trong Claude Code (CLI hoặc IDE)
# Giao việc cho agent qua tự nhiên ngôn ngữ:
"Anh @viet-bai duyệt outline này rồi viết bài giúp anh, keyword 'cách chăm sóc mắt': <paste-outline>"
"Anh @lam-anh chèn ảnh + tạo hero cho Doc này: https://docs.google.com/document/d/..."
```

Claude Code sẽ auto-delegate khi description khớp, hoặc user gọi explicit bằng `@<agent-name>`.

## Convention output

- File tạm output dưới `/tmp/<agent>-<timestamp>/` — KHÔNG commit lên repo.
- Final outputs (outline, brief, draft) lưu ngoài repo (Google Drive / local doc) tuỳ workflow.

## License

MIT cho 2 agents + custom skills của Seongon. SEO skills khác giữ license gốc.
