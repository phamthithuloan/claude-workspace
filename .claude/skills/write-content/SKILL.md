---
name: write-content
description: "Lên outline hoặc viết bài SEO content đáp ứng search intent của keyword chính, mục tiêu rank tốt hơn top 10 Google. Tích hợp Google Drive MCP để load research/brief có sẵn và lưu outline thành Google Doc cho CTV. Use khi user nói: viết outline, lên outline, viết bài SEO, draft bài, tạo brief, content brief cho [keyword], outline cho [keyword]."
user-invokable: true
argument-hint: "<keyword> [intent]"
license: MIT
compatibility: "Requires Google Drive MCP (đã connect trong Claude). Optional: seo-sxo, seo-content-brief, seo-cluster để phân tích top 10 trước khi viết outline."
metadata:
  author: Seongon Content
  version: "1.0.0"
  category: content
---

# Lên outline / viết bài SEO

Bạn là content strategist. Nhận keyword chính, output outline (hoặc bài hoàn chỉnh) đáp ứng search intent và vượt top 10.

## Quy trình

### Bước 1 — Hiểu intent

Hỏi user:
- **Keyword chính** là gì?
- **Intent** ước lượng: informational / commercial / transactional / navigational?
- **Audience**: beginner / intermediate / expert?
- **Mục tiêu kinh doanh**: brand awareness / lead gen / direct conversion?

### Bước 2 — Load research từ Google Drive (nếu có)

Hỏi: "Có file research/brief nào trên Google Drive cần load không?"

Nếu có, dùng Google Drive MCP:
1. `search_files` với query là keyword/tên file → trả về list
2. `read_file_content` với fileId → lấy nội dung
3. Tổng hợp insight từ research vào outline

Nếu không có → bỏ qua bước này.

### Bước 3 — Phân tích top 10 Google

Đề xuất user gọi 1 trong các skill SEO sẵn có:
- **`seo-sxo`** — đọc SERP, phân tích intent + page-type match
- **`seo-content-brief`** — fetch top 10 + extract H2/H3 + word count
- **`seo-cluster`** — nếu cần topic cluster lớn hơn 1 bài

Đợi output, dùng làm input cho bước 4.

### Bước 4 — Tạo outline

Format outline với:

- **Title** (≤ 60 ký tự, có keyword chính, có hook)
- **Meta description** (≤ 160 ký tự, có keyword + CTA + USP)
- **URL slug** (3-5 từ, kebab-case)
- **H1** (chứa keyword chính, khác title nếu cần)
- **Intro** (100-150 từ): trả lời câu hỏi gốc trong 50 từ đầu
- **H2 chính** (5-8 cái), mỗi H2:
  - Word count đề xuất (tổng bài 1500-3500 từ tùy keyword)
  - 2-4 H3 con
  - Bullet/list/table gợi ý
  - Internal link suggestion
- **FAQ** (5-8 câu hỏi từ "People also ask"): nếu intent informational
- **CTA** placement (≥ 2 vị trí trong bài)
- **Search intent fulfillment**: 1 paragraph giải thích outline answer intent như thế nào, ưu thế so với top 10

### Bước 5 — Save lên Google Drive (tùy chọn)

Nếu user muốn lưu outline cho CTV:
1. Format outline thành Markdown clean
2. Dùng MCP `create_file` với:
   - `mimeType`: `application/vnd.google-apps.document`
   - `name`: `Outline - [Keyword] - [YYYY-MM-DD]`
   - `content`: outline markdown
   - `parentFolderId` (hỏi user nếu cần)
3. Trả về link Google Doc cho user gửi CTV

## Quy tắc viết outline

- **Vượt top 10**: outline phải có ≥ 2 điểm KHÁC/HƠN top 10 (angle riêng, data riêng, format khác)
- **Skyscraper + 10x**: cover everything top 10 cover, cộng thêm 30% chiều sâu
- **Internal link chiến lược**: link tới pillar/hub đã có, không link homepage
- **E-E-A-T baked in**: ngay từ outline đã có chỗ cho case study, expert quote, original data
- **Mobile-first**: paragraph ≤ 4 câu, có bullet/visual mỗi 200 từ

## Output Format

Trả về 1 markdown block, đúng theo template trong [examples/sample-outline.md](examples/sample-outline.md). Tóm tắt cấu trúc bắt buộc:

```
# Outline: [keyword chính]

**Word count target:** [theo intent]
**Intent:** [informational | commercial | transactional | navigational]
**Primary keyword:** [kw]
**Secondary keywords:** [kw1], [kw2], [kw3]

## Title (≤60 chars)
## Meta description (≤160 chars)
## URL slug
## H1
## Intro (100-150 từ)
## H2 #1: [tên] ([word count])
   - H3 children
   - Internal link suggestion
   - Visual suggestion
## H2 #2 … H2 #N
## FAQ (≥3 câu nếu intent informational)
## CTA placement (≥2 vị trí)
## Search intent fulfillment (1 paragraph giải thích vượt top 10 thế nào)
```

Đọc [examples/sample-outline.md](examples/sample-outline.md) để xem ví dụ đầy đủ.

## Pre-delivery checklist (trước khi trả outline)

- [ ] Đã xác nhận **keyword chính + intent** (informational / commercial / transactional / navigational)
- [ ] Đã đọc top 10 (qua `seo-sxo` hoặc `seo-content-brief`) HOẶC user xác nhận skip
- [ ] Outline có **≥ 2 điểm KHÁC top 10** (angle riêng, data riêng, format khác) — note rõ trong "Search intent fulfillment"
- [ ] **Word count phù hợp intent**:
  - Informational how-to: 2000–3500 từ
  - Commercial/comparison: 1200–2000 từ
  - Transactional/product: 800–1200 từ
  - Navigational: 400–800 từ
- [ ] Có ≥ 3 internal link suggestion, **không link homepage**
- [ ] Có ≥ 1 chỗ E-E-A-T (case study / expert quote / original data / screenshot)
- [ ] FAQ ≥ 3 câu (bắt buộc nếu intent informational, optional cho intent khác)
- [ ] Title ≤ 60 chars, meta ≤ 160 chars, slug ≤ 5 từ
- [ ] Có section "Search intent fulfillment" giải thích outline answer intent + ưu thế vs top 10

## Common pitfalls (vết xe đổ)

| Lỗi hay mắc | Why bad | Fix |
|---|---|---|
| Copy structure top 10 1:1 | Không có differentiation → không vượt được top 10 | Bắt buộc có ≥ 2 angle/section top 10 KHÔNG có |
| Word count quá dài cho intent transactional | User bounce, không convert | Transactional 800–1200 từ, focus value prop + CTA |
| Skip xác định search intent | Outline mismatch → rank không bền, bounce cao | Hỏi/xác định intent TRƯỚC khi tạo outline |
| Internal link toàn về homepage | Lãng phí link equity | Link tới pillar/sub-pillar relevant, anchor natural |
| Save Google Doc không hỏi parentFolderId | Doc rơi vào My Drive root, CTV khó tìm | Hỏi folder hoặc dùng convention `Outlines/[YYYY-MM]/` |
| Keyword stuffing (kw lặp > 3% density) | Google penalize, đọc tệ | Mention: 1× H1, 1× intro, 1–2× H2; LSI tự nhiên |
| FAQ generic không match "People also ask" | Không trigger FAQ rich result | Fetch PAA từ SERP (skill `seo-sxo`), copy nguyên văn câu hỏi |
| Outline không có CTA (intent commercial/transactional) | Bỏ lỡ conversion | Bắt buộc ≥ 2 CTA placement cho intent có conversion goal |
| Không classify intent khi keyword ambiguous | Đoán sai → outline lệch | Đọc [intent-guide.md](intent-guide.md) để classify chính xác |

## Lưu ý chuyển tiếp

- Sau khi user accept outline → đề xuất gọi `brand-image` để tạo hero image cùng bộ keyword.
- Outline xong → có thể chuyển CTV draft, sau đó user gọi `review-outline` để chấm bản viết.

## Load-on-demand references

Đọc CHỈ KHI cần (không load mặc định trong mỗi invocation):

- [intent-guide.md](intent-guide.md) — Decision tree chi tiết 4 loại intent + signals nhận diện. Đọc khi user không chắc về intent hoặc keyword ambiguous.
- [examples/sample-outline.md](examples/sample-outline.md) — Outline mẫu đầy đủ cho keyword how-to (informational). Tham chiếu khi user yêu cầu "cho xem ví dụ" hoặc khi cần check format.
