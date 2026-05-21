# Frontmatter Guide

Tất cả về frontmatter của `SKILL.md`: name, description, và các field tùy chọn. Dùng ở Step 4 (name) và Step 5 (frontmatter).

Nguồn: Anthropic official docs — https://code.claude.com/docs/en/skills . **Mọi field đều optional**; chỉ `description` được khuyến nghị nên có để Claude biết khi nào dùng skill.

## Contents

- Name
- Description
- when_to_use
- Bảng đầy đủ 15 field
- Các field tùy chọn — chi tiết

---

## Name

`name` là field optional — nếu bỏ, Claude Code lấy tên thư mục skill làm name.

Validate cứng:

- Chỉ gồm chữ thường, số và dấu gạch ngang (kebab-case)
- ≤64 ký tự
- KHÔNG chứa "claude" hoặc "anthropic" (reserved words)
- KHÔNG chứa XML tag
- Nên match tên thư mục skill

Convention đặt tên: **gerund form** mô tả hành động — `processing-pdfs`, `analyzing-spreadsheets`, `reviewing-code`. KHÔNG đặt kiểu `pdf-tool`, `excel-helper`.

**Ngoại lệ — Reference skill**: skill mô tả một domain entity (brand, schema, policy, voice) dùng noun-phrase tự nhiên hơn gerund — `brand-guidelines`, `bigquery-schema-reference`, `company-policies`. Chấp nhận khi noun-phrase mô tả domain rõ hơn (`brand-guidelines` rõ hơn `applying-brand-guidelines`).

**Lỗi thường gặp — reserved word trong name.** `claude-helper`, `anthropic-tool`, `my-claude-skill` đều bị reject. Khi user đưa tên chứa reserved word: nêu rõ "reserved word phát hiện", đề xuất 2-3 tên thay thế.

---

## Description

Field quan trọng nhất. Đây là **Tier 1** của progressive disclosure — `name` + `description` của MỌI skill luôn nằm trong context (khoảng ~100 token/skill). Claude đọc `description` để quyết định khi nào kích hoạt skill.

Quy tắc cứng:

- ≤1024 ký tự
- Non-empty
- KHÔNG chứa XML tag
- Nêu **cả hai**: *what* (skill làm gì) + *when* (khi nào Claude nên dùng)
- **Third-person** — "This skill should be used when...", "Generates commit messages..." — KHÔNG "I can help you...", KHÔNG "Use this when..."
- Đặt use case quan trọng nhất LÊN ĐẦU — phần `description` + `when_to_use` bị cắt ở 1536 ký tự trong skill listing.

Vì sao third-person + nêu when: Claude match `description` với yêu cầu của user để quyết định trigger. Description mơ hồ → Claude undertrigger, skill bị bỏ qua dù task hợp.

Bad:

```yaml
description: Helps with PDFs.
description: A skill for working with documents.
```

Good:

```yaml
description: This skill should be used when the user asks to "extract text from PDF", "merge PDFs", "phân tích PDF", or works with .pdf files. Extracts text and tables, merges documents, fills forms.
```

**Lỗi thường gặp — description vague.** "Helps with X", "For working with Y" → Claude không nhận ra cần dùng skill. Fix: nêu what + when, liệt kê trigger phrases cụ thể.

**Lỗi thường gặp — description >1024 ký tự.** Nhồi 30+ trigger phrase + cả đoạn triết lý. Fix: giữ what + when + 5-8 trigger phrase cốt lõi. Phần trigger phụ chuyển sang `when_to_use`.

**Lỗi thường gặp — dấu `: ` làm vỡ YAML.** `description` (và `when_to_use`) là YAML scalar không đặt trong dấu nháy. Nếu giá trị chứa `: ` (hai chấm + dấu cách) — vd viết "6 subtypes: Research/Workflow/..." — YAML parser hiểu nhầm là mapping lồng nhau và báo lỗi "mapping values are not allowed". Fix: tránh `: ` — đổi sang dấu gạch ngang ("6 subtypes — Research/...") hoặc bỏ dấu cách sau hai chấm.

---

## when_to_use

Field optional, chứa **ngữ cảnh bổ sung về khi nào kích hoạt skill** — trigger phrases, ví dụ yêu cầu. Nội dung được nối vào `description` khi Claude cân nhắc trigger.

`description` + `when_to_use` cộng lại tối đa **1536 ký tự** (giới hạn trong skill listing).

Dùng `when_to_use` khi danh sách trigger phrases dài: giữ `description` gọn cho phần *what + when chính*, đẩy trigger phrases mở rộng sang `when_to_use`. Skill đơn giản thì để hết trong `description` cũng được.

---

## Bảng đầy đủ 15 field

Mọi field optional. Default frontmatter của một skill thường chỉ cần `description` (và `name` nếu muốn khác tên thư mục).

| Field | Purpose | Giá trị |
|---|---|---|
| `name` | Tên skill | kebab-case, ≤64 ký tự |
| `description` | What + when, để Claude quyết định trigger | text ≤1024 ký tự |
| `when_to_use` | Trigger context bổ sung, nối vào description | text (gộp ≤1536) |
| `argument-hint` | Gợi ý argument hiện khi autocomplete | text, vd `[issue-number]` |
| `arguments` | Tên positional argument cho thay thế `$name` trong body | chuỗi cách nhau bởi space, hoặc YAML list |
| `disable-model-invocation` | `true` = Claude không tự kích hoạt, chỉ user gọi `/skill` | `true` / `false` (default false) |
| `user-invocable` | `false` = ẩn skill khỏi menu `/` | `true` / `false` (default true) |
| `allowed-tools` | Tool được pre-approve khi skill active | space-separated hoặc YAML list |
| `model` | Model dùng khi skill active | `sonnet`/`opus`/`haiku`/`inherit` |
| `effort` | Mức effort khi skill active | `low`/`medium`/`high`/`xhigh`/`max` |
| `context` | Ngữ cảnh chạy skill | `inline` (default) / `fork` |
| `agent` | Subagent type dùng khi `context: fork` | tên built-in/custom agent |
| `paths` | Glob pattern giới hạn khi nào skill auto-activate | comma-separated hoặc YAML list |
| `hooks` | Hook scoped theo lifecycle của skill | YAML theo schema hooks |
| `shell` | Shell cho block `` !`command` `` | `bash` (default) / `powershell` |

Field KHÔNG tồn tại (đừng generate): `allowed_tools` (gạch dưới — sai, đúng là `allowed-tools`), `license`, `metadata`, `version`.

---

## Các field tùy chọn — chi tiết

Mặc định BỎ field tùy chọn. Chỉ set khi skill thật sự cần.

### `disable-model-invocation`

`true` → Claude KHÔNG tự nạp skill; chỉ chạy khi user gõ `/skill-name`. Dùng cho skill có side effect (ghi file, deploy, gọi API) mà không nên tự kích hoạt.

### `user-invocable`

`false` → ẩn skill khỏi menu `/`. Dùng cho Reference skill chứa background knowledge mà user không cần gọi trực tiếp — Claude tự consult.

### `allowed-tools`

Liệt kê tool được pre-approve (không hỏi permission) khi skill active. Vd: `Read Grep` hoặc `Bash(git *)`. Lưu ý: field này KHÔNG giới hạn tool nào dùng được — nó chỉ pre-approve các tool liệt kê.

### `model` / `effort`

Override model / effort level cho phần còn lại của lượt hiện tại khi skill active. `model: inherit` giữ model đang dùng. KHÔNG lưu vào settings.

### `context` + `agent`

`context: fork` → chạy toàn bộ skill trong một subagent context riêng (isolate khỏi conversation chính). `agent` chỉ định subagent type khi `context: fork` — built-in (`Explore`, `Plan`, `general-purpose`) hoặc custom agent.

### `paths`

Glob pattern giới hạn khi nào skill tự kích hoạt — Claude chỉ auto-load skill khi đang làm việc với file khớp pattern. Vd: `paths: "*.ts, *.tsx"`.

### `argument-hint` / `arguments`

`argument-hint` — text gợi ý hiện khi user autocomplete `/skill-name`. `arguments` — đặt tên cho positional argument để thay thế `$name` trong body skill.

### `hooks`

Hook scoped theo lifecycle của skill. Theo schema hooks chung của Claude Code.

### `shell`

Shell cho dynamic context block `` !`command` `` trong body. `bash` mặc định; `powershell` cần env `CLAUDE_CODE_USE_POWERSHELL_TOOL=1`.
