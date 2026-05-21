# Skill Taxonomy

Phân loại skill và hướng dẫn riêng cho từng dạng. Dùng ở Step 2 (classify), Step 3 (Q&A), Step 6 (section structure).

## Contents

- Trục 1 — Reference vs Task
- Decision tree
- Reference skill
- Task: Research
- Task: Workflow
- Task: Generator
- Task: Analyzer
- Task: Voice/Style
- Task: Discovery
- Hybrid skills

---

## Trục 1 — Reference vs Task

Phân loại theo cách Claude SỬ DỤNG skill, dựa trên deliverable.

**Reference skill** — Claude áp dụng kiến thức/style trong khi làm một task KHÁC. Skill cung cấp context (schema, brand voice, policy, pattern). Skill kết thúc, user KHÔNG nhận artifact mới riêng từ skill — output cuối là deliverable của task chính.

**Task skill** — Claude chạy chuỗi bước CỤ THỂ tạo ra DELIVERABLE MỚI ĐỘC LẬP (file, report, audit, code, draft revised). User nhận artifact riêng từ skill. Có pipeline numbered, decision point, output format spec.

Cả hai trigger giống nhau — qua `/skill-name`, mention rõ, hoặc Claude match `description`. Khác duy nhất ở deliverable.

Câu hỏi quyết định:

> "Sau khi skill chạy xong, user có nhận một artifact MỚI ĐỘC LẬP từ skill không?"
> Có → Task skill. Không (skill chỉ inject knowledge vào task khác) → Reference skill.

## Decision tree

```
Skill tạo ra artifact MỚI ĐỘC LẬP cho user không?
│
├── KHÔNG (chỉ inject knowledge vào task khác) → Reference skill
│
└── CÓ → Task skill — phân tiếp theo input/output:
    │
    ├── Input = topic/keyword → Research
    ├── Input = spec/params → Generator
    ├── Input = artifact có sẵn (URL/file/code) →
    │   ├── Output rating/insight → Analyzer
    │   ├── Output revised version → Voice/Style
    │   └── Output multi-step deliverable → Workflow
    └── Input = câu hỏi mở, không deliverable cố định → Discovery
```

---

## Reference skill

Claude áp dụng song song với task khác. Không có pipeline numbered, không deliverable riêng.

**Ví dụ**: `brand-guidelines`, `bigquery-schema-reference`, `company-policies`, `api-reference`.

### Q&A (Step 3)

Hỏi trong 1 message:

```
1. Domain skill phụ trách là gì? (vd: brand voice, BigQuery schemas, NDA template)
2. Khi nào Claude nên consult skill này? (context domain nào trong conversation)
3. Có file/asset gì Claude cần tham khảo không? (vd: brand-voice.md, schema files)
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả domain]

## Khi nào áp dụng
[Trigger conditions — context nào của conversation cần consult skill]

## Core principles / Domain knowledge
[Kiến thức chính — ngắn, ≤300 từ. Detail dài → references/]

## Reference files
[Pointer tới references/*.md, mỗi file 1 dòng mô tả purpose]

## Anti-patterns
[Cách KHÔNG áp dụng skill — vd "Không dùng tone friendly cho CEO communications"]
```

Reference skill KHÔNG có pipeline numbered — đó là dấu hiệu phân biệt với Task.

### Eval focus

1. **Golden**: User task khớp domain → Claude consult skill, áp dụng đúng.
2. **Edge**: User task chạm nhẹ domain → Claude vẫn nhận ra cần consult.
3. **Anti**: User task NGOÀI domain → Claude KHÔNG over-apply skill.

Reference skill không có output riêng — verify "skill triggered" bằng cách hỏi Claude post-task hoặc kiểm output style có match rule trong skill không.

---

## Task: Research

**Pattern**: input topic → output báo cáo có nguồn citation.

**Đặc trưng**: tổng hợp nhiều nguồn, có citation explicit, pipeline tương đối dài. Thường spawn research agent (background) hoặc dùng Web tool.

**Ví dụ**: macro-research (40+ trang), vietnamese-seo-researcher.

### Q&A (Step 3)

```
1. Output format: markdown / PDF / slides / khác?
2. Target length: bao nhiêu từ/trang?
3. Nguồn ưu tiên: loại nguồn nào? (vd: McKinsey, government data, academic)
4. Vietnam context có bắt buộc một chương riêng không?
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả output type + audience]

## Khi nào dùng
[Trigger phrases]

## Default settings
[Table: output format, length, language, citation style]

## Pipeline N bước
### Step 1 — Clarify scope (optional)
### Step 2 — Spawn research agent (background)
### Step 3 — Skeleton output trong khi agent chạy
### Step 4 — Receive findings, patch placeholders
### Step 5 — Complete sections + citations
### Step 6 — Render output
### Step 7 — Verify + report

## Source priority
[Thứ tự ưu tiên loại nguồn]

## Citation format
[Format chuẩn]

## Anti-patterns
- KHÔNG bịa số liệu — mọi stat phải có citation
- KHÔNG suy đoán không có chứng cứ
- KHÔNG dùng nguồn quá cũ cho data thay đổi nhanh
```

### Eval focus

1. **Golden**: Topic phổ biến → output đúng format, đủ độ dài, có citation.
2. **Edge**: Topic niche / ít public data → skill note "data sparse" thay vì bịa.
3. **Anti**: Topic vi phạm policy (vd medical advice) → skill từ chối hoặc redirect.

---

## Task: Workflow

**Pattern**: chuỗi bước có deliverable end-to-end, thường multi-stage.

**Đặc trưng**: 5-10 bước numbered, có decision point giữa chừng, phụ thuộc tool/file trong repo, output là một deliverable tổng hợp (audit report, deployment, migration).

**Ví dụ**: audit-webapp, security-review, deployment workflow.

### Q&A (Step 3)

```
1. Bước cuối deliver gì? (file, report, deployed app, PR...)
2. Skill phụ thuộc tool/file/script gì trong repo? (liệt kê absolute path)
3. Decision points: bước nào skill tự dừng hỏi user vs auto-proceed?
4. Recovery: nếu step giữa fail, skill xử lý thế nào?
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả end-to-end outcome]

## Khi nào dùng
[Trigger phrases + tiền điều kiện]

## Tiền điều kiện
- [ ] [repo state cần có]
- [ ] [tool installed]

## Pipeline N bước
### Step 1 — <name>
**Decision point**: [khi nào skip / proceed]
**Exit condition**: [khi nào step coi là done]
### Step 2 — <name>
...

## Decision points
[Table: Step | Hỏi user khi | Auto-proceed khi]

## Recovery
[Mỗi failure mode → recovery action]

## Output
[Format report cuối pipeline]

## Anti-patterns
- KHÔNG skip validation step để fast-track
- KHÔNG auto-deploy khi chưa có user confirm
```

### Eval focus

1. **Golden**: Repo đúng tiền điều kiện → pipeline chạy hết, output đúng format.
2. **Edge**: Tiền điều kiện thiếu 1 item → skill detect + báo user + dừng.
3. **Anti**: Input ambiguous ở decision point → skill HỎI thay vì đoán.

---

## Task: Generator

**Pattern**: tạo artifact mới (file, code, config) từ spec.

**Đặc trưng**: input là spec/params, output là 1+ file mới, có template + placeholder fill logic, validation cuối.

**Ví dụ**: create-skill (chính nó), scaffold scripts, mcp-builder.

### Q&A (Step 3)

```
1. Output là gì? (file type + naming convention)
2. Input params: bắt buộc gì, optional gì?
3. Có template/skeleton có sẵn không? (path + format)
4. Validation cuối: skill check gì để confirm output đúng?
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả output artifact]

## Khi nào dùng
[Trigger phrases]

## Input schema
[Table: Param | Type | Required | Default | Note]

## Pipeline
### Step 1 — Validate input
### Step 2 — Choose template
### Step 3 — Fill placeholders
### Step 4 — Generate file(s)
### Step 5 — Self-validate

## Output naming convention
[Pattern]

## Templates
[Liệt kê assets/templates/* — mỗi cái 1 dòng mô tả khi nào dùng]

## Anti-patterns
- KHÔNG overwrite file có sẵn khi chưa user confirm
- KHÔNG generate output khi input invalid
```

### Eval focus

1. **Golden**: Input đầy đủ, hợp lệ → output đúng schema, file parse OK.
2. **Edge**: Input thiếu optional param → skill dùng default đúng.
3. **Anti**: Input invalid → skill từ chối với error rõ, không generate junk.

---

## Task: Analyzer

**Pattern**: input artifact (URL, code, file) → output insight có score/rating.

**Đặc trưng**: gather data từ nhiều nguồn, scoring rubric explicit, pre-delivery review, fallback cascade khi data thiếu.

**Ví dụ**: seo-backlinks, security-review, performance-profiling.

### Q&A (Step 3)

```
1. Input: artifact gì? (URL, file, code, repo)
2. Output: report format gì + có score không (0-100 / pass-fail / severity)?
3. Data sources: skill lấy data từ đâu?
4. Fallback: nếu primary source unavailable, degrade thế nào?
5. Pre-delivery review: skill verify findings thế nào trước khi report?
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả: input → score/insight]

## Khi nào dùng
[Trigger phrases]

## Source detection
[Bước đầu: detect data source nào available]

## Analysis framework
[Mỗi section: sources + scoring threshold]

## Scoring rubric
[Table: Factor | Weight | Source preference | Confidence]
**Data sufficiency gate**: <N factor có data → output INSUFFICIENT DATA thay vì score giả

## Fallback cascade
[Primary → secondary → baseline, kèm confidence weighting]

## Pre-delivery review (bắt buộc)
- [ ] Mỗi claim có source label
- [ ] Phân biệt "not found" vs "not crawled" vs "below threshold"
- [ ] Score chỉ output khi đủ data

## Output format
[Summary score → sections → critical issues → recommendations]

## Anti-patterns
- KHÔNG output numeric score khi data insufficient
- KHÔNG present inferred data as fact
- KHÔNG skip pre-delivery review
```

### Eval focus

1. **Golden**: Artifact đủ data → score đúng, recommendation cụ thể.
2. **Edge**: Artifact thiếu data → skill output INSUFFICIENT DATA hoặc partial score kèm confidence note.
3. **Anti**: Artifact malformed → skill detect + error rõ, không hallucinate findings.

---

## Task: Voice/Style

**Pattern**: review hoặc rewrite content theo voice/style định trước.

**Đặc trưng**: input là draft text, output là revised text + diff explanation, có before/after examples bắt buộc, anti-patterns explicit (cấm từ X).

**Ví dụ**: voice-checker, git-commit-viet.

### Q&A (Step 3)

```
1. Voice mô tả ngắn: <tone> + <register> + <audience>
2. Cho 2 cặp BEFORE-AFTER: 1 bad → 1 good (CỤ THỂ, không vague)
3. Anti-patterns: liệt kê words/structures CẤM dùng
4. Output khi revise: full rewrite / diff inline / suggestions list?
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả voice + audience]

## Khi nào dùng
[Trigger phrases]

## Voice fingerprint
- Tone / Register / Audience / Non-negotiables

## Examples (Before → After)
### Example 1: <type of issue>
**Before** / **Issue** / **After** / **Why fixed**
### Example 2-5: ...

## Anti-patterns (banned)
### Banned words
### Banned structures
### Banned tones

## Pipeline khi review
### Step 1 — Read draft
### Step 2 — Identify violations
### Step 3 — Rewrite / suggest
### Step 4 — Output theo format đã định

## Output format
[Spec cụ thể]
```

### Eval focus

1. **Golden**: Draft có ≥3 violation → skill detect tất cả + rewrite đúng.
2. **Edge**: Draft đã on-voice → skill confirm OK, không over-edit.
3. **Anti**: Draft có technical content phức tạp → skill chỉ sửa voice, KHÔNG đổi technical accuracy.

---

## Task: Discovery

**Pattern**: Q&A có hướng dẫn để KHÁM PHÁ requirement, không có deliverable cố định.

**Đặc trưng**: conversational multi-turn, mỗi turn skill quyết định câu hỏi tiếp, output cuối là spec/plan/decision (không phải file). Khó eval — eval thường là "chất lượng câu hỏi".

**Ví dụ**: brainstorming, requirements-gathering.

### Q&A (Step 3)

```
1. Goal khám phá: skill giúp user làm rõ gì?
2. Question tree: 3-5 câu opening, mỗi câu 2-3 nhánh follow-up
3. Stop condition: khi nào skill kết thúc Q&A?
4. Output cuối: summary / plan / recommendation / decision matrix?
```

### Section structure

```markdown
# <skill-name>

[1-2 câu mô tả goal exploration]

## Khi nào dùng
[Trigger phrases]

## Discovery approach
- Style (Socratic / directive) / Pace / Tone

## Question tree
### Opening questions (Q1-Q5)
[Mỗi câu kèm nhánh follow-up theo answer]
### Deepening questions

## Stop condition
[Khi nào skill kết thúc Q&A]

## Output cuối
[Format: summary / plan / decision matrix]

## Anti-patterns
- KHÔNG hỏi >3 câu trong 1 turn
- KHÔNG advance sang deepening trước khi opening rõ
- KHÔNG output recommendation generic — phải specific dựa trên Q&A
```

### Eval focus

1. **Golden**: User cooperative, trả lời chi tiết → skill ra recommendation cụ thể.
2. **Edge**: User trả lời ngắn / "không biết" → skill rephrase câu hỏi hoặc đưa option.
3. **Anti**: User cố push qua Q&A nhanh → skill vẫn ensure đủ info trước khi synthesize.

---

## Hybrid skills

Nhiều skill thực tế là hybrid 2-3 dạng. Quy tắc: chọn dạng deliver giá trị CHÍNH làm primary, apply section structure của dạng đó, mention dạng phụ trong body.

| Skill | Hybrid | Primary |
|---|---|---|
| `macro-research` | Research + Workflow | Research (nguồn + citation là core) |
| `audit-webapp` | Workflow + Analyzer | Workflow (multi-step audit là core) |
| `git-commit-viet` | Voice + Generator | Voice (style enforcement là core) |
| `brand-guidelines` | Reference + Voice | Reference (Claude áp dụng nhiều domain) |
