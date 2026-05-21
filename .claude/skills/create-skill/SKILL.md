---
name: create-skill
description: This skill should be used when the user asks to "create a new skill", "tạo skill mới", "build a Claude Code skill", "đóng gói thành skill", "/create-skill [name]", "skill mới cho [task]", or wants to package a recurring workflow into a reusable Claude Code skill at .claude/skills/. Generates production-grade SKILL.md following Anthropic's official skill docs — progressive disclosure 3 tiers, third-person description, imperative body under 500 lines, complete frontmatter, references separated from SKILL.md. Classifies skills along 2 axes (Reference vs Task by deliverable; if Task, one of 6 subtypes — Research/Workflow/Generator/Analyzer/Voice/Discovery) and ships 3 eval scenarios (Golden/Edge/Anti).
---

# create-skill

Tạo Claude Code skill mới đạt chuẩn production, tuân thủ Anthropic official docs về skills.

Docs reference: https://code.claude.com/docs/en/skills

## Khi nào dùng

User nói:

- `/create-skill [name]` hoặc `/create-skill`
- "tạo skill mới cho [task]"
- "build a Claude Code skill that..."
- "đóng gói quy trình này thành skill"
- "tôi muốn skill làm X mỗi lần tôi gõ /Y"

KHÔNG dùng skill này khi:

- User chỉ muốn 1 prompt 1 lần (prompt thẳng nhanh hơn)
- User muốn sửa skill có sẵn (đọc skill đó rồi edit, không generate mới)
- User muốn cài skill từ repo có sẵn (clone + copy, không generate)

## Default settings

| Setting | Default | Override |
|---|---|---|
| Scope | Project (`.claude/skills/<name>/`) | "Skill toàn cục" → `~/.claude/skills/<name>/` |
| Ngôn ngữ body | Tiếng Việt | User chỉ dùng tiếng Anh trong yêu cầu |
| Description | Trigger phrases mix VN + EN | User chỉ định 1 ngôn ngữ |
| Name format | gerund (`processing-pdfs`) | Reference skill → noun-phrase chấp nhận được |
| Body length | 1500-2000 từ, ≤500 dòng | Phức tạp → tách bớt sang `references/` |

## Pipeline — 8 bước

Theo thứ tự, không skip. Sau Step 1, KHÔNG hỏi user xác nhận từng bước — tự quyết theo default, report cuối.

### Step 1 — Hỏi 2 concrete examples (HARD GATE)

HARD GATE — kể cả session có no-clarify directive, vẫn fail-closed nếu input không pass. Mục đích skill là tạo SKILL.md chất lượng; input mơ hồ → skill rác.

Hỏi đúng 1 message gồm 2 câu:

```
1. Skill này NHẬN gì → TRẢ gì? (1 câu)
2. Cho 2 ví dụ thật về cách user sẽ gọi skill này.
```

Skip phần hỏi chỉ khi input ban đầu đã đủ: mô tả cụ thể NHẬN gì → TRẢ gì (không vague kiểu "helps with X"), và có ≥1 ví dụ user sẽ gọi skill. Với Reference skill, một "context condition" (vd "khi user viết content trong project này") thay được cho ví dụ gọi.

### Step 2 — Classify skill type

Đọc `references/skill-taxonomy.md`. Phân loại theo deliverable:

- **Reference skill** — Claude áp dụng kiến thức/style trong khi làm một task khác; user KHÔNG nhận artifact riêng từ skill.
- **Task skill** — Claude chạy chuỗi bước cụ thể tạo ra deliverable mới độc lập. Nếu là Task, chọn tiếp 1 trong 6 dạng: Research, Workflow, Generator, Analyzer, Voice/Style, Discovery.

Hybrid → chọn dạng deliver giá trị chính.

### Step 3 — Hỏi Q&A theo dạng

Đọc `references/skill-taxonomy.md` mục dạng đã classify — mỗi dạng có bộ Q&A riêng. Hỏi toàn bộ trong 1 message, không hỏi tuần tự.

### Step 4 — Đề xuất name + validate

Đọc `references/frontmatter-guide.md` mục "Name". Đề xuất 2-3 tên (gerund form; Reference skill có thể dùng noun-phrase), validate điều kiện cứng. User-supplied name fail validate → nêu rõ điều kiện nào fail rồi đề xuất tên mới.

### Step 5 — Generate frontmatter

Đọc `references/frontmatter-guide.md`. Frontmatter bắt buộc nên có `description`; các field khác opt-in. Viết description third-person, nêu cả *what* + *when*; đặt trigger phrases trong `description` hoặc tách sang field `when_to_use`.

### Step 6 — Generate body

1. Đọc `assets/skill-template.md` — base skeleton.
2. Đọc `references/skill-taxonomy.md` mục "Section structure" của dạng đã classify.
3. Đọc `references/body-guide.md` — quy tắc body chung (imperative, ≤500 dòng, progressive disclosure).
4. Generate.

### Step 7 — Generate bundled resources

Đọc `references/body-guide.md` mục "Bundled resources". Tạo `references/`, `scripts/`, `assets/` theo nhu cầu của dạng skill — auto-suggest theo dạng, không hỏi user nếu đã rõ.

### Step 8 — Generate eval scenarios + self-validate

Đọc `assets/eval-template.md`, tạo 3 eval scenario (Golden / Edge / Anti) lưu vào `EVALS.md` ở root skill folder. Sau đó đọc `references/validation.md`, pass 100% hard gates. Fail gate → fix → re-validate → report.

## Output report

Sau Step 8, output cho user:

```
Skill created — <path tới folder skill>

Files:
- SKILL.md (X từ, Y dòng)
- EVALS.md (3 scenarios)
- references/<files> (nếu có)
- assets/<files> (nếu có)
- scripts/<files> (nếu có)

Type: <Reference | Task: Research/Workflow/Generator/Analyzer/Voice/Discovery>
Scope: <Project | User>

Cách invoke: /<skill-name> [argument]

Test: mở phiên Claude Code mới, chạy 3 scenario trong EVALS.md.

Lưu ý hiệu lực: nếu `.claude/skills/` đã tồn tại từ trước, skill mới có hiệu lực
ngay trong phiên hiện tại. Nếu đây là lần đầu tạo thư mục `.claude/skills/`,
restart Claude Code.
```

KHÔNG emoji, KHÔNG hype, KHÔNG trailing "Hope this helps".

## Voice rules cho skill được generate

- Direct, không hedge ("có thể", "perhaps", "might be")
- KHÔNG emoji, KHÔNG hype words ("đột phá", "revolutionary", "ngoạn mục")
- Imperative form cho body ("Tạo X", "Hỏi user Y") — KHÔNG "Bạn nên...", "You should..."
- KHÔNG trailing recap

Inherit thêm voice rules từ workspace CLAUDE.md nếu có. Override theo yêu cầu user (vd: khoá đào tạo cần tông thân thiện hơn).

## Skill files

| File | Purpose | Load when |
|---|---|---|
| `SKILL.md` | Main pipeline (file này) | Skill triggers |
| `references/skill-taxonomy.md` | Reference vs Task + 6 subtype + Q&A + section structure mỗi dạng | Step 2, 3, 6 |
| `references/frontmatter-guide.md` | Name + description + 15 frontmatter field hợp lệ | Step 4, 5 |
| `references/body-guide.md` | Quy tắc viết body + bundled resources | Step 6, 7 |
| `references/validation.md` | Hard/content gate + sample-check + report format | Step 8 |
| `assets/skill-template.md` | Base SKILL.md skeleton | Step 6 |
| `assets/eval-template.md` | Template 3 eval scenario | Step 8 |
| `EVALS.md` | Test scenarios cho chính skill này | Manual test khi edit SKILL.md |

Mỗi file references chỉ cách `SKILL.md` 1 cấp và không trỏ tới file references khác — `SKILL.md` là router duy nhất.

## Common pitfalls — 4 cái critical nhất

1. **Description vague** — "Helps with X" → Claude undertrigger skill. Phải nêu what + when, trigger phrases cụ thể. Chi tiết: `frontmatter-guide.md`.
2. **Nhồi mọi thứ vào SKILL.md** — body 5000+ từ, context bloat. Cứng: ≤500 dòng, detail dài tách `references/`.
3. **Second person trong body** — "Bạn nên...", "You should...". Body viết imperative cho Claude đọc.
4. **Quên EVALS** — skill không có 3 scenario test → drift. Minimum Golden/Edge/Anti.

## 6 tiêu chí BẮT BUỘC cho SKILL.md (Seongon standard)

Khi tạo skill mới trong claude-workspace này, SKILL.md PHẢI pass đủ 6 tiêu chí — checklist nội bộ Seongon (nguồn: training mẫu seo-backlinks). Memory: feedback-seongon-skill-checklist.md.

1. **Frontmatter đầy đủ**: name + description + user-invokable + argument-hint + license + compatibility + metadata (author/version/category). KHÔNG chỉ có 3 field tối thiểu.
2. **Pipeline từng bước chi tiết**: mỗi step có commands cụ thể (bash/regex/tool template), KHÔNG mô tả vague.
3. **File bổ trợ load-on-demand**: section `## Skill files` có cột "Load when" — Claude chỉ Read file đó khi pipeline trigger.
4. **Error Handling 3 cột**: section `## Error Handling (Vết xe đổ)` table `Error | Cause | Resolution`. KHÔNG dùng 2 cột Failure/Action.
5. **Pre-Delivery Review MANDATORY**: section standalone với checklist categories (content integrity, format, source, output quality...). Rule "Fail ≥1 → KHÔNG return".
6. **Output format rõ ràng**: section `## Output` với mẫu cụ thể (table/JSON/markdown structure).

Template `assets/skill-template.md` đã bake sẵn 6 section. Validation gates ở `references/validation.md`. KHÔNG bỏ section nào trừ khi user explicit yêu cầu.
