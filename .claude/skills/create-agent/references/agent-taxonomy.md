# Agent Taxonomy — 3 dạng custom agent + khi nào dùng built-in

## Trước khi tạo custom agent — check built-in trước

Claude Code đã có 5 built-in subagent. Đừng tạo custom nếu built-in cover được:

| Built-in | Model | Tool access | Use case |
|---|---|---|---|
| `Explore` | haiku | Read-only | File discovery, code search, codebase exploration |
| `Plan` | inherit | Read-only | Codebase research cho plan mode |
| `general-purpose` | inherit | All tools | Complex multi-step, exploration + action |
| `claude-code-guide` | haiku | Read | Q&A về Claude Code features |
| `statusline-setup` | sonnet | Read+Edit | Configure status line via `/statusline` |

**Tạo custom agent CHỈ KHI**:
- Cần model khác (vd: `haiku` cho cost) hoặc tool restriction cụ thể
- Cần domain prompt riêng (code-reviewer với checklist project-specific)
- Cần persistent `memory` qua nhiều session
- Cần `background: true` mặc định
- Cần hooks validation (vd: db-reader chỉ read-only SQL)
- Muốn dùng `claude --agent <name>` làm main session

Nếu user chỉ cần "tìm file", "research codebase", "tạo plan" → redirect built-in, đừng tạo custom.

## 3 dạng custom agent

```
Agent này LÀM GÌ trong codebase?
│
├── Critique artifact (file/PR/draft) → output verdict/score
│   → Reviewer
│
├── Execute domain task end-to-end → output deliverable
│   → Specialist
│
└── Action narrow scope với tool validation
    → Restricted-Doer
```

---

## 1. Reviewer

**Pattern**: input artifact (file/PR/draft) → output critique with verdict.

**Tools mặc định**:
```yaml
tools: Read, Grep, Glob, Bash    # Bash chỉ cho tests/linters
```

**Frontmatter typical** (minimal — không auto-set field):
```yaml
---
name: code-reviewer
description: |
  Expert code review specialist. Reviews code for quality, security, maintainability.
  Use proactively after writing or modifying code.
tools: Read, Grep, Glob, Bash
color: blue
---
```

`model: inherit` là default — skip field. `memory` chỉ set khi user explicit ask + body có Memory usage section instruct read/write.

### Khi nào dùng `memory`
Reviewer phù hợp nhất với memory trong 3 dạng — NHƯNG vẫn OPT-IN, không default:
- `memory: project` — checked into git, team share
- `memory: user` — personal learning across all projects
- `memory: local` — không vào git

Memory tự enable Read/Write/Edit cho memory directory only (`<scope>/agent-memory/<name>/`).

**KHÔNG auto-set `memory: project` chỉ vì "Reviewer thường benefit"**. Set chỉ khi:
1. User explicit ask agent học pattern qua sessions, VÀ
2. Body có "Memory usage" section instruct read-before / write-after cụ thể.

Nếu thiếu 1 trong 2 → field waste. Default skip.

### Q&A (Step 3)

Hỏi trong 1 message:

- Artifact type (code file / PR / Google Doc / markdown)?
- Output: pass/fail | severity tiers | 0-100 score?
- Memory: project (default) | user | local | none?
- Needs Bash (run tests)?

### Body structure

```markdown
You are a <role> reviewer. <Single sentence objective>.

## When invoked
1. <First step — usually git diff or read target>
2. <Second step — apply checklist>
3. <Final step — output verdict>

## Review checklist
- <criterion 1>
- <criterion 2>
- ...

## Output format
### Critical (must fix)
- <issue> — <location> — <recommendation>

### Warnings (should fix)
- ...

### Suggestions (consider)
- ...

[If memory enabled]
## Memory usage
- Consult memory before review for known patterns
- Update memory after review with new patterns learned
```

### Eval focus

1. **Golden**: Artifact có issues rõ → detect tất cả + categorize đúng severity
2. **Edge**: Artifact clean → confirm OK, không bịa issues
3. **Anti**: Out-of-scope review request → từ chối với rationale

### Examples thực tế

- `code-reviewer` (docs example)
- `voice-checker` — review draft theo brand voice
- `security-review` — focus security issues only
- `content-review-user` — UX checklist cho content

---

## 2. Specialist

**Pattern**: domain expert. Input domain task → output deliverable.

**Đặc trưng quan trọng**: Specialist có 2 run modes, behavior khác nhau:

| Run mode | Cách invoke | Parallel sub-agents | Use case |
|---|---|---|---|
| **As subagent** | Main Claude dispatch via Agent tool, hoặc user `@<name>` | **KHÔNG** — subagent cannot spawn subagent | Single-shot domain task |
| **As main session** | `claude --agent <name>` | **CÓ** — main thread spawn được | Orchestrate workflow đầy đủ |

→ Khi build Specialist orchestrator (nhiều parallel sub-agents), user PHẢI invoke qua `--agent` mode, không phải via Agent tool.

Nếu Specialist muốn chạy parallel work từ context subagent → KHÔNG được. Workaround:
- Chain sub-agents từ main conversation thay vì nesting
- Dùng Skills (skills chạy trong main context, có thể spawn agents từ đó)

**Tools mặc định**:
```yaml
tools: Bash, Read, Write, Edit, Glob, Grep    # full set
# Thêm Agent nếu Specialist run as main (claude --agent)
# Thêm Skill để invoke skills
```

**Frontmatter typical**:
```yaml
---
name: seo-specialist
description: |
  SEO end-to-end specialist. Coordinates content writing and WordPress publishing from Google Sheets.
  Use proactively when user requests SEO content tasks, WP publishing, or full SEO pipeline.
tools: Bash, Read, Write, Edit, Glob, Grep, Skill
model: sonnet
skills:
  - content-write    # preload skill content
  - tech-wp-publish
maxTurns: 80
effort: high
color: green
---
```

KHÔNG set `background: true` mặc định cho Specialist — kể cả khi user nói "tôi muốn làm việc khác". Lý do:
1. User có thể `/agents` switch agent thành main session → mở terminal khác làm việc song song. Đây là pattern recommended cho Specialist có skills spawn parallel sub-agents.
2. Parent Claude tự decide foreground/background case-by-case dựa trên context. Không cần ép field.
3. Set `background: true` = ép EVERY dispatch chạy background → mất quyền chọn của parent + user.

### Khi nào dùng các field

- `background: true` — **CHỈ** khi agent thiết kế cho long unattended runs (nightly batch jobs, deploy pipelines). KHÔNG set chỉ vì user "muốn làm việc khác" — đó là dispatch decision, không phải agent property. Default: skip field.
- `skills: [...]` — preload skill content vào context startup, agent không cần discovery
- `isolation: worktree` — agent modify files, muốn isolate khỏi main checkout
- `memory: project` — agent học domain patterns qua nhiều session
- `permissionMode: acceptEdits` — auto-accept edits (only nếu trust agent)
- `initialPrompt` — auto-submit first turn khi user run `claude --agent <name>`

### Q&A (Step 3)

Hỏi trong 1 message:

- Domain?
- Tools needed (Read/Write/Edit/Bash + others)?
- Run mode: subagent only | `/agents` main session | both?
- Background-default? **Default NO** — chỉ YES nếu agent thiết kế cho unattended-only batch (nightly job, deploy). "User muốn làm việc khác" KHÔNG phải lý do.
- Skills cần preload?
- Memory needed?

### Body structure

```markdown
You are <role> specialist. <Single sentence objective>.

## When invoked
1. <Parse input — sheet URL, task spec, etc>
2. <Classify task mode if multi-mode>
3. <Execute via skills/tools>
4. <Consolidate output>

## Tools usage
- <Tool 1>: <when>
- <Tool 2>: <when>

## Skills usage
| Skill | When to call |
|---|---|
| <skill 1> | <use case> |

## Output format
<Final report structure>

## Constraints
- <hard rule 1>
- <hard rule 2>
```

**KHÔNG** include section "Spawn parallel sub-agents" trong body nếu agent run as subagent. Subagents không spawn được.

Nếu Specialist intended để run via `claude --agent`, body có thể có section orchestration parallel.

### Eval focus

1. **Golden**: Domain task match → deliverable hoàn chỉnh
2. **Edge**: Task partial match → process phần match, note out-of-scope
3. **Anti**: Out-of-domain task → từ chối, redirect

### Examples thực tế

- `data-scientist` (docs example) — SQL + BigQuery
- `debugger` (docs example) — Read+Edit+Bash với debug workflow
- `deployment-specialist` — orchestrate deploy
- `seo-specialist` (project) — content + WP publish

---

## 3. Restricted-Doer

**Pattern**: action narrow scope, thường với hook validation hoặc tool restriction extreme.

**Đặc trưng**:
- Tools hẹp (1-3 tools)
- Thường có `hooks: PreToolUse` validate input
- `permissionMode` cụ thể (vd: `dontAsk` cho safe ops)
- Single-purpose

**Tools mặc định**:
```yaml
tools: Bash    # hoặc Read+Edit cụ thể
```

**Frontmatter typical**:
```yaml
---
name: db-reader
description: Execute read-only database queries. Use proactively for data analysis or reports.
tools: Bash
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-readonly-query.sh"
model: inherit
color: yellow
---
```

### Khi nào dùng `hooks`

Restricted-Doer thường có hooks để enforce constraint runtime:
- `PreToolUse` — validate input trước khi tool chạy (block destructive SQL, block writes ngoài scope)
- `PostToolUse` — run linter/formatter sau edit
- Hooks return exit code 2 để block operation

### Khi nào dùng `permissionMode`

- `dontAsk` — agent auto-deny prompts, chỉ làm operations explicitly allowed
- `acceptEdits` — auto-accept edits (chỉ khi trust)
- `plan` — read-only enforcement

### Q&A (Step 3)

Hỏi trong 1 message:

- Allowed operations (1-5 explicit)?
- Blocked operations (rely on hooks)?
- Permission mode (dontAsk often fits)?

### Body structure

```markdown
You are <role> doer with restricted scope. <Single sentence what allowed/blocked>.

## When invoked
1. <Parse request>
2. <Validate against scope>
3. <Execute>

## Allowed operations
- <op 1>
- <op 2>

## Blocked operations
- <op 1> — handled by PreToolUse hook
- <op 2>

## Output format
<Result + any blocked attempts logged>

If asked to do blocked operation, explain why blocked + suggest alternative.
```

### Eval focus

1. **Golden**: Allowed operation → executes, returns result
2. **Edge**: Borderline operation → hook validates, allows or blocks với rationale
3. **Anti**: Blocked operation → hook blocks (exit 2), agent explains

### Examples thực tế

- `db-reader` (docs example) — read-only SQL với hook validate
- `npm-installer` — chỉ allowed `npm install`, hook block các npm subcommands khác
- `git-commit-only` — Bash với hook chỉ allow `git add/commit`, block push/reset

---

## Decision flowchart

```
User mô tả task agent
│
├── Built-in agents cover được không?
│   ├── YES → Redirect: dùng Explore/Plan/general-purpose, đừng tạo custom
│   └── NO → tiếp tục
│
├── Agent OUTPUT là gì?
│   │
│   ├── Critique/verdict/score → Reviewer
│   ├── Deliverable (file/PR/report/data) → Specialist
│   └── Narrow action với validation → Restricted-Doer
│
└── Run mode chính?
    ├── As subagent (from main) → đảm bảo KHÔNG cần spawn sub-agents
    ├── As main session (claude --agent) → có thể orchestrate đầy đủ
    └── Both → document cả 2 paths trong body
```

## Hybrid agents

Đa số custom agents là pure 1 type. Hybrid rare. Examples:

| Agent | Hybrid | Primary | Lý do |
|---|---|---|---|
| `debugger` (docs example) | Reviewer + Specialist | **Specialist** | Output = code fix, không phải critique |
| `audit-webapp` | Reviewer + Specialist | **Reviewer** | Output = audit verdict, fix là job khác |
| `data-scientist` | Specialist + Restricted-Doer | **Specialist** | Output = analysis + recommendations |

Quy tắc: chọn type có deliverable primary làm classification.
