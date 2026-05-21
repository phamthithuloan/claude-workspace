# Agent Validation Checklist — aligned with Anthropic docs

Run before committing agent. Hard gates MUST pass 100%. Content gates SHOULD pass.

## Contents
- Hard gates (auto-verifiable)
- Content gates (manual review)
- Type-specific gates
- Anti-pattern sample-check
- Failure recovery
- Final validation report format

## Hard gates

### File location
- [ ] File at `.claude/agents/<name>.md` (project) hoặc `~/.claude/agents/<name>.md` (user)
- [ ] If folder structure (rare): `.claude/agents/<name>/<name>.md` với references/ subfolder
- [ ] Name not conflicting với higher-priority scope (check managed > CLI > project > user > plugin)

### Frontmatter — Required fields
- [ ] YAML parses cleanly
- [ ] `name`: present, lowercase + hyphens + numbers only
- [ ] `name`: ≤64 chars
- [ ] `name`: NOT containing "anthropic" or "claude" (Anthropic constraint)
- [ ] `name`: NOT containing XML tags
- [ ] `description`: present, non-empty
- [ ] `description`: ≤1024 chars
- [ ] `description`: NOT containing XML tags

### Frontmatter — Description quality
- [ ] Single-sentence what agent does
- [ ] Contains "Use proactively" hoặc "Proactively" phrase
- [ ] Mentions specific trigger conditions
- [ ] If `background: true` set → drop prose "BACKGROUND MODE" hack (use field instead)

### Frontmatter — Tools
- [ ] `tools` field present (allowlist) hoặc inherits all (omitted)
- [ ] Each tool in list is valid name
- [ ] Each tool used in body (≥1 mention)
- [ ] If `disallowedTools` also set, both lists make sense together

### Frontmatter — Optional fields (verify if present)
- [ ] `model` ∈ {sonnet, opus, haiku, full-model-id, inherit}
- [ ] `maxTurns` integer 1-200
- [ ] `effort` ∈ {low, medium, high, xhigh, max}
- [ ] `permissionMode` ∈ {default, acceptEdits, auto, dontAsk, bypassPermissions, plan}
- [ ] `memory` ∈ {user, project, local}
- [ ] `background` is boolean
- [ ] `isolation` is "worktree" if set
- [ ] `color` ∈ {red, blue, green, yellow, purple, orange, pink, cyan}
- [ ] `skills` items reference existing skills (warn if missing)
- [ ] `mcpServers` items reference valid server config
- [ ] `hooks` follow `hooks` event schema

### Body
- [ ] Body ≤500 lines (hard cap)
- [ ] **Body target ≤150 lines** (soft cap — vượt thì re-justify mỗi section)
- [ ] Second-person addressed to agent ("You are...")
- [ ] Has "When invoked" or "Quy trình" section
- [ ] Has "Output format" section với concrete template
- [ ] Has "When to stop and report blocker" hoặc tương đương
- [ ] KHÔNG có Progress reporting section nếu `background` field không set
- [ ] KHÔNG có Quality checklist tách riêng nếu đã có Constraints (gộp 1 chỗ)

### Body — tool/field alignment
- [ ] Every tool in frontmatter mentioned in body usage section
- [ ] If `memory` set → body has "Memory usage" section with read/write instructions
- [ ] If `skills` preloaded → body references them in "Skills usage" section
- [ ] If `background: true` set → body designed for autonomous (no AskUserQuestion mid-task) + có Progress reporting
- [ ] If `permissionMode: bypassPermissions` set → body explicitly justifies bypass

### Field justification (mới — hard gate)
- [ ] **Mọi optional field set có justification rõ**. Sanity-check từng field:
  - `model` non-inherit: có cost/quality reason cụ thể?
  - `maxTurns`: có cost guard reason hoặc bị auto-pick? Auto-pick → REMOVE.
  - `effort` non-medium: task chứng minh cần deeper reasoning?
  - `background: true`: agent thiết kế cho unattended-only batch?
  - `memory`: body thực sự instruct read-before/write-after?
  - `skills`: agent cần preload (vs tự discovery)?
  - `isolation: worktree`: modify nhiều files VÀ user muốn isolate?
  - `permissionMode` non-default: rủi ro cụ thể justify?
  - `hooks`: Restricted-Doer cần runtime validation?
  - `initialPrompt`: agent designed cho `--agent` main mode?
- [ ] Nếu justification = "tôi nghĩ nên có" hoặc "thường thì..." → REMOVE field.

### Paths
- [ ] All paths use forward slash `/`, NOT backslash `\`
- [ ] References (if folder structure) are 1-level deep from agent .md

### Output structure (mới — hard gate)
- [ ] `.claude/agents/` chỉ chứa `<name>.md` (KHÔNG có EVALS.md, README.md, notes.md, etc.)
- [ ] Nếu agent dùng folder structure: `.claude/agents/<name>/<name>.md` + `references/` subfolder. Vẫn KHÔNG có EVALS.md ở agents root.
- [ ] Evals report **inline** trong final message cho user. KHÔNG write file mặc định.
- [ ] Nếu user explicit ask save evals → confirm path (default `tests/agents/<name>.md`), KHÔNG vào `.claude/agents/`.

## Content gates (manual review)

### Built-in overlap check
- [ ] Agent does NOT duplicate Explore (code search)
- [ ] Agent does NOT duplicate Plan (codebase research for plans)
- [ ] Agent does NOT duplicate general-purpose (complex multi-step)
- [ ] If overlap detected, justify differentiator (model/tools/memory/background)

### Type-specific (Reviewer)
- [ ] No Write/Edit in tools (Reviewer doesn't fix)
- [ ] Output format has verdict/severity rubric
- [ ] If `memory: project` set, body has memory usage section
- [ ] No sub-agent spawn logic (Reviewer is single-shot)

### Type-specific (Specialist)
- [ ] Tool list matches workflow needs
- [ ] If body has parallel sub-agent spawning logic → document "requires --agent main mode"
- [ ] If `background: true` → no AskUserQuestion in body
- [ ] If `skills` preloaded → body uses preloaded knowledge
- [ ] If `initialPrompt` set → describes main-session opening behavior

### Type-specific (Restricted-Doer)
- [ ] Tools narrow (1-3)
- [ ] If hooks set → hook scripts referenced exist
- [ ] Body explicitly lists blocked operations
- [ ] Permission mode appropriate for restriction (dontAsk often fits)

### Voice
- [ ] No emoji unless user explicitly allowed
- [ ] No hype words ("revolutionary", "đột phá")
- [ ] No hedge phrases — direct statements
- [ ] Consistent terminology

## Anti-pattern sample-check

Sample 5-10 sentences from body, check:

### Built-in overlap
Bad: Agent describes work that Explore/Plan/general-purpose already does
Fix: redirect to built-in, justify custom only if unique differentiator

### Missing proactive trigger
Bad: description lacks "Use proactively" or "Proactively"
Fix: add phrase

### Subagent spawn logic
Bad: body suggests "spawn parallel sub-agents from this agent" without `--agent` note
Fix: either drop parallel logic (subagent constraint) or document main-session-only

### Prose `background` hack
Bad: description contains "BACKGROUND MODE" prose telling parent to use run_in_background
Fix: replace with `background: true` field, drop prose

### Tool over-grant
Bad: frontmatter has Write/Edit but body only does Read/Grep
Fix: strip Write/Edit

### Memory unused
Bad: `memory: project` set, body never mentions memory
Fix: add Memory usage section with read-before / write-after instructions

### Background + interactive
Bad: `background: true` + AskUserQuestion in tools/body
Fix: choose one (drop background OR drop interactivity)

### bypassPermissions no justify
Bad: `permissionMode: bypassPermissions` without explanation in body
Fix: justify or downgrade

### Third-person body
Bad: "The agent should...", "It needs..."
Fix: "You are...", "You will..."

### Paths backslash
Bad: `scripts\helper.py`, `references\guide.md`
Fix: forward slash

### Auto-set field từ soft signal
Bad: User nói "muốn làm việc khác" → set `background: true`. User nói "agent thông minh hơn theo thời gian" → set `memory: project`. User nói "task phức tạp" → set `effort: high`.
Fix: REMOVE field. Field set chỉ khi user explicit ask hoặc requirement objective rõ.

### EVALS.md hoặc file phụ trong .claude/agents/
Bad: `.claude/agents/EVALS.md`, `.claude/agents/README.md`, `.claude/agents/notes.md`
Fix: DELETE file. Evals inline trong final report. Nếu user explicit muốn save → `tests/agents/<name>.md`.

### Body bloat
Bad: Body >150 lines với Progress reporting section + Quality checklist tách riêng + Constraints + bảng dài compare run modes + repeat info từ frontmatter
Fix: gộp Quality + Constraints. Drop Progress reporting nếu không `background`. Trim bảng nếu 1 mode chính. Drop repeated info.

Sample-check fail >1 violation → re-write section, re-check.

## Failure recovery

| Gate fail | Recovery |
|---|---|
| YAML parse error | Fix indentation, quotes, colons |
| Name >64 chars / invalid chars | Propose 3 alternatives, ask user |
| Name conflicts higher-priority scope | Rename or move to higher scope |
| Reserved word in name (claude/anthropic) | Reject, propose without reserved word |
| Description >1024 chars | Trim less-critical content |
| Description missing "proactively" | Add phrase referencing common triggers |
| Body >500 lines | Move long section to references/, replace with pointer |
| Body >150 lines (soft cap) | Trim, gộp Quality+Constraints, drop Progress reporting nếu không background, drop bảng dài |
| Tools mismatch body | Strip unused OR add missing |
| Built-in overlap | Redirect to built-in, justify differentiator if proceeding |
| Memory set, no body mention | Add Memory usage section HOẶC remove memory field |
| Background + interactive conflict | Drop background OR drop interactivity |
| Subagent spawn logic | Drop OR move to --agent main mode docs |
| Field set không justify | REMOVE field |
| EVALS.md trong .claude/agents/ | DELETE file, evals inline trong report |
| File phụ (README, notes) trong .claude/agents/ | DELETE — agents folder chỉ chứa `<name>.md` |

## Final validation report format

```
Validation report — <agent-name>

Hard gates: <N>/<total> passed
- PASS: <gate 1>
- PASS: <gate 2>
- FAIL: <gate 3> — <reason> — <recovery action>

Content gates: <N>/<total> passed (informational)
- PASS: ...
- WARN: <gate X> — <reason> — <action>

Type-specific gates (<type>): <N>/<total> passed

Tool alignment: <N>/<frontmatter tools> mention in body
- PASS hoặc list mismatches

Field alignment:
- background: <true/false/omit> — <consistent with body>
- memory: <scope/omit> — <body has instructions>
- skills: <preloaded count> — <body uses them>
- permissionMode: <mode/omit> — <appropriate for risk>

Anti-pattern sample-check: <N>/10 sentences clean
- <N> violations: <type> at <location> — <fix>

Status: READY TO SHIP | NEEDS FIX
```

Report AGENT READY only when:
- Hard gates 100% pass
- Tool alignment 100%
- Field alignment consistent
- Anti-pattern sample-check 0 violations
- Type-specific gates relevant all pass
