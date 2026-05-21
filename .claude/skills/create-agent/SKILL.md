---
name: create-agent
description: Use this skill when user asks to "create a new agent", "tạo agent mới", "build a Claude Code subagent", "đóng gói thành agent", "/create-agent [name]", "agent mới cho [task]", or wants to package a delegate-able workflow into a reusable Claude Code agent at .claude/agents/. Generates production-grade agent .md aligned with Anthropic's official subagent docs — classifies agents into 3 types (Reviewer/Specialist/Restricted-Doer), warns when built-in agents (Explore/Plan/general-purpose) cover the task, enforces subagent-cannot-spawn-subagent constraint, validates against common mistakes (auto-set field, file clutter, body bloat), and reports inline test scenarios.
---

# create-agent

Tạo Claude Code agent mới đạt chuẩn production, tuân thủ Anthropic official docs về subagents.

Docs reference: https://code.claude.com/docs/en/sub-agents

## Khi nào dùng

User nói:

- `/create-agent [name]` hoặc `/create-agent`
- "tạo agent mới cho [task]"
- "build a Claude Code agent that..."
- "đóng gói workflow này thành agent"
- "tôi muốn agent có persistent memory" / "agent chạy background"

KHÔNG dùng skill này khi:

- Built-in agent cover được task → redirect (xem `references/agent-taxonomy.md`)
- User chỉ muốn 1 prompt 1 lần (prompt thẳng nhanh hơn)
- User muốn sửa agent có sẵn (edit, không generate mới)
- Workflow inline với conversation, không cần isolated context → dùng `/create-skill`

## Agent vs Skill

Tạo agent CHỈ KHI cần ≥1 trong:

- Tool restriction enforce theo từng task
- Persistent memory qua nhiều session (`memory: project`)
- Background mode mặc định (`background: true`)
- Permission mode khác main conversation
- Run as main thread qua `claude --agent <name>`
- Model khác (vd: `haiku` cho task đơn giản, tiết kiệm cost)

Nếu chỉ cần inline expansion trong main conversation → **skill**. Nếu cần nested delegation phức tạp → skills + chain subagents từ main (subagents KHÔNG spawn được subagents).

## Default settings

| Setting | Default | Override |
|---|---|---|
| Scope | Project (`.claude/agents/<name>.md`) | "Agent toàn cục" → `~/.claude/agents/<name>.md` |
| Language body | English (matches docs examples) | User dùng VN → VN body |
| Description | "Use proactively..." pattern | User chỉ định khác |
| Name format | `<domain>-<role>` (`code-reviewer`, `seo-specialist`) | Domain-specific |
| Model | `inherit` | Cost-sensitive → `haiku`; strategic → `opus` |
| File structure | Single `.md` file | Folder + references/ chỉ nếu body >500 lines |

## Pipeline — 7 bước

Theo thứ tự. Sau Step 1, KHÔNG hỏi user xác nhận từng bước — tự quyết theo default, report cuối.

### Step 1 — Check built-in coverage + hỏi role (HARD GATE)

HARD GATE — kể cả có no-clarify directive, fail-closed nếu input không pass.

1. Đọc `references/agent-taxonomy.md` mục "check built-in trước". Nếu một built-in agent cover được task → nói user, đề xuất dùng built-in. User vẫn insist với lý do cụ thể (model/memory/restricted tools) → proceed.
2. Nếu không match, hỏi user trong đúng 1 message:

   ```
   1. Agent này LÀM GÌ? (1 câu — output là critique / deliverable / restricted action)
   2. 2 sample invocation phrases user sẽ nói
   3. Cần memory cross-session không? Có phải dạng unattended long-running thật sự
      (nightly batch, deploy) không?
   ```

Skip phần hỏi chỉ khi input ban đầu đã đủ cả ba: role cụ thể, có ≥1 invocation example, và run mode rõ.

KHÔNG tự suy "user muốn làm việc khác" thành `background: true` — đó là quyết định dispatch của parent, không phải thuộc tính của agent.

### Step 2 — Classify type

Đọc `references/agent-taxonomy.md`. Classify theo deliverable thành 1 trong 3:

- **Reviewer** — input là artifact, output là critique/verdict.
- **Specialist** — input là task domain, output là deliverable.
- **Restricted-Doer** — action phạm vi hẹp, tool restriction chặt.

Hybrid → chọn type theo deliverable chính.

### Step 3 — Hỏi Q&A theo type

Đọc `references/agent-taxonomy.md` mục type đã classify — mỗi type có bộ Q&A riêng. Hỏi toàn bộ trong 1 message, không hỏi tuần tự.

### Step 4 — Propose name + validate

Đọc `references/frontmatter-guide.md` mục "Name". Đề xuất tên theo convention `<domain>-<role>`, validate các điều kiện cứng. Fail bất kỳ điều kiện nào → đề xuất 3 tên thay thế kèm lý do.

### Step 5 — Generate frontmatter

Required: `name` + `description`. Optional fields: **mặc định SKIP tất cả**.

Đọc `references/frontmatter-guide.md` cho ba việc: viết description đúng chuẩn, chọn tools minimum-necessary theo type, và xét điều kiện set từng optional field. Mỗi field set phải justify được bằng 1 câu rõ trong report.

### Step 6 — Generate body

1. Đọc `assets/agent-template.md` — base skeleton.
2. Đọc `references/agent-taxonomy.md` mục "Body structure" của type đã classify — cấu trúc body riêng cho từng type.
3. Đọc `references/body-guide.md` — quy tắc body chung: độ dài, ngôi xưng, section bắt buộc, cái gì KHÔNG cho vào, progress reporting.
4. Generate.

### Step 7 — Self-validate + report

Đọc `references/validation.md`, pass 100% hard gates.

**File output: chỉ `<name>.md` ở `.claude/agents/` — KHÔNG tạo EVALS.md, KHÔNG folder, KHÔNG file phụ.** Fail gate → fix → re-validate → report.

## Output report

Format: ngắn gọn, evals inline ở cuối, KHÔNG bloat.

```
Agent: <name> tại <path>

Type: <Reviewer | Specialist | Restricted-Doer>
Scope: <Project | User>
Tools: <list>
Optional fields: <list active fields với justification 1 dòng mỗi field, HOẶC "none">

Invoke:
- Auto-dispatch (default): parent decide foreground/background case-by-case
- Explicit: @<name>
- Main session: /agents chọn <name>, HOẶC `claude --agent <name>` (nếu agent designed cho mode này)

Test (3 scenarios, copy-paste vào session sạch):

### Eval 1 — Golden
Trigger: <user prompt>
Expect: <1-2 dòng behavior + output format>
Pass: <1-2 criteria ngắn>

### Eval 2 — Edge
Trigger: <ambiguous/incomplete prompt>
Expect: <graceful behavior, không fabricate>
Pass: <1-2 criteria>

### Eval 3 — Anti
Trigger: <out-of-scope/destructive prompt>
Expect: <từ chối + redirect>
Pass: <1-2 criteria>

Notes:
- Restart Claude Code để dùng agent mới
```

Evals KHÔNG ghi ra file. Nếu user explicit muốn save, hỏi path (vd `tests/agents/<name>.md`) — KHÔNG default save vào `.claude/agents/`.

KHÔNG emoji, KHÔNG hype, KHÔNG trailing "Hope this helps".

## Voice rules cho agent generate

- Direct, no hedge phrases ("might be", "perhaps", "could potentially")
- No emoji, no hype words ("revolutionary", "powerful", "amazing")
- No trailing recap ("Hope this helps", "Let me know if...")
- Second-person addressed cho agent body; imperative voice cho instructions

Inherit thêm voice rules từ workspace CLAUDE.md nếu có. Override theo yêu cầu user.

## Skill files

| File | Purpose | Load when |
|---|---|---|
| `SKILL.md` | Main pipeline (this file) | Skill triggers |
| `references/agent-taxonomy.md` | Check built-in + 3 types + Q&A + body structure mỗi type | Step 1, 2, 3, 6 |
| `references/frontmatter-guide.md` | Name + description + tools + optional fields | Step 4, 5 |
| `references/body-guide.md` | Quy tắc viết body + progress reporting | Step 6 |
| `references/validation.md` | Hard/content gates + sample-check + report format | Step 7 |
| `assets/agent-template.md` | Base agent skeleton | Step 6 |
| `EVALS.md` | Test scenarios cho chính skill này | Manual test khi edit SKILL.md |

Mỗi file references chỉ cách `SKILL.md` 1 cấp và không trỏ tới file references khác — `SKILL.md` là router duy nhất.

## Relationship với built-in `/agents` command

Claude Code có built-in `/agents` command với "Generate with Claude" wizard. Khác biệt:

| Feature | `/agents` built-in | `create-agent` skill |
|---|---|---|
| Interactive UI | Yes (tabs, prompts) | No (markdown output) |
| Voice rules custom | No | Yes (project CLAUDE.md inherit) |
| Inline test scenarios | No | Yes (3 Golden/Edge/Anti, không tạo file) |
| Validation checklist | Light | Strict (hard gates + sample-check) |
| Built-in overlap warning | No | Yes |
| Type taxonomy | Implicit | Explicit (3 types) |
| Reproducible from prompt | No | Yes |

Use `/agents` cho quick interactive creation. Use `create-agent` skill cho team-shared agents với strict validation + inline eval scenarios.
