# Search intent classification guide

> **Load-on-demand**: chỉ đọc khi keyword ambiguous hoặc user không chắc intent.

## 4 loại intent (Google framework)

| Intent | Mô tả | Modifier signals | SERP signals |
|---|---|---|---|
| **Informational** | User muốn HỌC / HIỂU | "là gì", "cách", "tại sao", "what is", "how to", "guide" | Featured snippet, People also ask, Wikipedia, blog top, video |
| **Commercial investigation** | User so sánh trước khi mua | "tốt nhất", "best", "review", "vs", "so sánh", "alternative", "đánh giá" | Listicles ("Top 10"), review sites, comparison pages |
| **Transactional** | User SẴN SÀNG mua / hành động | "mua", "giá", "buy", "order", "đặt", "đăng ký", "tải" | Shopping ads, product pages, pricing pages |
| **Navigational** | User tìm 1 brand / page cụ thể | tên brand, "[brand] login", "[product] customer service" | Brand site #1, sitelinks, knowledge panel |

## Decision tree

```
Có tên brand trong query?
├── YES → Navigational
└── NO ↓

Có modifier mua hàng / hành động (mua/giá/đặt/buy/order)?
├── YES → Transactional
└── NO ↓

Có modifier so sánh (best/vs/review/so sánh)?
├── YES → Commercial investigation
└── NO ↓

Có modifier học hỏi (là gì/cách/how/why)?
├── YES → Informational
└── NO → MIXED / Ambiguous → cần check SERP
```

## Khi keyword AMBIGUOUS

Một số keyword không có modifier rõ ràng (vd "content marketing", "SEO"). Cách xử lý:

1. **Đọc top 10 hiện tại** (gọi `seo-sxo`)
2. **Đếm tỉ lệ page type**:
   - >60% blog/guide → Informational dominant
   - >40% listicle → Commercial dominant
   - >40% product/pricing → Transactional dominant
   - Mixed → Hybrid intent → outline cần cover cả 2

## Hybrid intent strategy

Nếu SERP mixed (vd 5 blog + 5 product), outline cần:
- **First half** (above the fold): trả lời informational ("X là gì + cách hoạt động")
- **Second half**: chuyển sang commercial ("Cách chọn X + top picks") hoặc transactional ("Mua X ở đâu")
- CTA ở "transition point" giữa 2 phần

## Word count theo intent

| Intent | Word count target | Reason |
|---|---|---|
| Informational how-to | 2000–3500 | Cần depth, comprehensive |
| Informational concept | 1500–2500 | Đủ giải thích, không lan man |
| Commercial investigation | 1200–2000 | Comparison table + verdict, không cần dài |
| Transactional product | 800–1200 | Focus value prop + social proof + CTA |
| Navigational | 400–800 | Brief, direct, link tới page cần |

## Ví dụ classify cụ thể

| Keyword | Intent | Reason |
|---|---|---|
| "content marketing là gì" | Informational | Modifier "là gì" |
| "best CRM software 2026" | Commercial | Modifier "best" + năm |
| "mua iPhone 17 Pro" | Transactional | Modifier "mua" |
| "salesforce login" | Navigational | Brand + action page |
| "SEO" (no modifier) | Ambiguous → check SERP | Quá broad |
| "cách viết content SEO" | Informational | Modifier "cách" |
| "Ahrefs vs Semrush" | Commercial | Modifier "vs" |
| "Notion pricing" | Transactional | Brand + pricing |
| "content marketing 2026" | Hybrid (info + commercial) | Top 10 mix blog + listicle |

## Anti-patterns

- KHÔNG: classify chỉ dựa keyword, bỏ qua SERP. SERP là source of truth.
- KHÔNG: assume "informational" làm default khi unsure → cần check
- KHÔNG: viết outline transactional dài 3000 từ → user bounce
- KHÔNG: viết outline informational quá ngắn (<1500 từ) → không depth → không rank
