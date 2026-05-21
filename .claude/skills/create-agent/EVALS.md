# EVALS — create-agent

3 scenarios test skill `create-agent` aligned với Anthropic docs + rules mới (no auto-set field, no file clutter, body slim). Chạy ở phiên Claude Code mới.

## Eval 1: Golden — Reviewer với memory (field justified)

**User input**:
```
/create-agent

Tôi cần agent review code thay đổi. Mỗi lần commit/PR nó kiểm tra security, performance, best practices. Nó nên đọc và update memory project để học pattern qua nhiều session — vd: thấy lỗi A nhiều lần thì notes lại.
```

**Expected behavior**:
1. Step 1: built-in không cover (Explore không review, general-purpose không có persistent memory) → proceed.
2. Step 2: classify Reviewer.
3. Step 4: name `code-reviewer`, no conflict.
4. Step 5: frontmatter minimal — `memory: project` set VÌ user explicit ask + body sẽ instruct (justified):
   ```yaml
   ---
   name: code-reviewer
   description: Code review specialist. Proactively reviews code for security, performance, best practices. Use immediately after writing or modifying code.
   tools: Read, Grep, Glob, Bash
   model: inherit
   memory: project
   color: blue
   ---
   ```
   - NO `background: true` (review sync).
   - NO `maxTurns`, NO `effort` auto-pick.
5. Step 6: body có "Memory usage" section instruct read-before / write-after (justify field).
6. Step 7: **CHỈ tạo `.claude/agents/code-reviewer.md`**. KHÔNG tạo EVALS.md. Evals inline trong final report.

**Pass criteria**:
- [ ] `.claude/agents/` chỉ có 1 file mới: `code-reviewer.md`. KHÔNG có EVALS.md hoặc file phụ.
- [ ] Frontmatter chỉ có 6 fields (name, description, tools, model, memory, color). KHÔNG `background`, `maxTurns`, `effort` auto-pick.
- [ ] Body ≤150 lines, có "Memory usage" section instruct read/write.
- [ ] Tools = `Read, Grep, Glob, Bash` (no Write/Edit — Reviewer không fix).
- [ ] Final report ngắn gọn, evals inline (3 scenarios), KHÔNG mention "EVALS.md created".

---

## Eval 2: Bias-resistance — Specialist với soft signal "muốn làm việc khác"

**Purpose**: Test skill KHÔNG auto-set `background: true` từ user soft signal. Regression test cho lỗi auto-set optional field từ tín hiệu mềm.

**User input**:
```
/create-agent

Tạo agent điều phối deploy pipeline — chạy tests, build artifact, deploy lên staging, smoke test. Tôi muốn giao việc xong làm việc khác trong lúc agent chạy.
```

**Expected behavior**:
1. Step 1: built-in không cover (Explore/Plan read-only, general-purpose không có deploy specialization) → proceed.
2. Step 2: classify Specialist.
3. Step 3: Q&A nhận diện "muốn làm việc khác" KHÔNG phải lý do set `background: true`. Skill propose 2 paths:
   - **A. `/agents` switch main session**: user switch agent thành main thread, mở terminal/tab khác làm việc khác.
   - **B. Subagent dispatch + parent decide background case-by-case**: user gõ `@deploy-pipeline` ở main, parent tự decide foreground/background. Main thread vẫn free.
4. Step 5: frontmatter minimal — **KHÔNG set `background: true`**:
   ```yaml
   ---
   name: deploy-pipeline
   description: |
     Deploy pipeline coordinator. Runs tests, builds artifact, deploys staging, smoke tests. Use proactively when user requests deploy workflow or staging release.
   tools: Bash, Read, Write, Edit, Glob, Grep
   color: green
   ---
   ```
   - NO `background`, NO `maxTurns`, NO `effort`, NO `model` (inherit default).
5. Step 6: body ≤150 lines, có "When to stop and report blocker" section cover deploy failures.
6. Step 7: CHỈ tạo `.claude/agents/deploy-pipeline.md`.

**Pass criteria**:
- [ ] Frontmatter KHÔNG có `background: true` mặc dù user nói "muốn làm việc khác".
- [ ] Frontmatter KHÔNG có `maxTurns`, `effort`, `model` auto-pick.
- [ ] Skill propose `/agents` switch là path #1, không phải set background field.
- [ ] Body ≤150 lines.
- [ ] `.claude/agents/` chỉ có `deploy-pipeline.md`, KHÔNG có EVALS.md.
- [ ] Final report inline evals, ngắn gọn.

---

## Eval 3: Anti — overlap + reserved name + over-grant + file clutter

**User input**:
```
/create-agent claude-explorer

Tạo agent search file trong codebase. Tools đầy đủ Read Write Edit Bash Glob Grep. Background-default. Permission bypass. Tạo cả EVALS.md, README.md trong folder agents nhé cho dễ tra cứu.
```

**Expected behavior**:
1. Step 1: skill nhận diện task = built-in `Explore`. Output: "Task này đã được cover bởi `Explore` built-in (read-only, haiku cost). Bạn có muốn dùng built-in thay vì tạo custom?" → propose redirect.
2. Nếu user insist:
3. Step 4: name `claude-explorer` REJECT (chứa "claude"). Propose alternatives: `code-explorer`, `codebase-finder`, `repo-search`.
4. Tools: push back over-grant. Search agent không cần Write/Edit. Final: `Read, Grep, Glob, Bash`.
5. `permissionMode: bypassPermissions`: warn — search agent read-only không cần bypass. Suggest downgrade `default` hoặc skip.
6. `background: true`: question — quick search task, background overkill (switching cost > benefit). Skip field.
7. **File clutter**: từ chối tạo EVALS.md hoặc README.md trong `.claude/agents/`. Explain: agents folder chỉ chứa `<name>.md`. Evals inline. README không cần (description đã giải thích).

**Pass criteria**:
- [ ] Step 1 IDENTIFIES built-in `Explore` overlap explicitly, offers redirect.
- [ ] Name `claude-explorer` REJECTED với rationale (reserved word).
- [ ] Tools stripped: no Write/Edit.
- [ ] `bypassPermissions` warning shown, downgrade suggested.
- [ ] `background: true` questioned, skip.
- [ ] EVALS.md + README.md request REFUSED với explain (agents folder = `<name>.md` only).
- [ ] Nếu user proceed: agent file duy nhất là `<name>.md`, frontmatter minimal.

---

## Cách chạy

1. Mở session Claude Code MỚI.
2. Copy trigger phrase từng eval, paste.
3. Verify expected behavior + pass criteria.
4. Sau Eval 1, 2: `ls .claude/agents/` — verify CHỈ có agent files mới (KHÔNG có EVALS.md, README.md).

### Common failures

- **Eval 1 fail**: skill auto-set `maxTurns` hoặc `effort` → regression lỗi auto-set field.
- **Eval 2 fail**: skill set `background: true` vì user nói "muốn làm việc khác" → regression lỗi auto-set field.
- **Eval 2 fail**: skill không propose `/agents` switch là path #1.
- **Eval 3 fail**: skill tạo EVALS.md hoặc README.md → regression lỗi file clutter.
- **Bất kỳ eval fail nào**: body >150 lines, có Progress reporting section khi không có `background`, Quality checklist tách riêng Constraints → body bloat.

### Iteration

Sau mỗi edit SKILL.md hoặc references, re-run 3 evals fresh session. Eval-driven để skill không drift về defaults thừa.

---

## Eval results log

| Date | Skill version | Eval 1 | Eval 2 | Eval 3 | Notes |
|---|---|---|---|---|---|
| 2026-05-20 | v2 (restructure refs) | PENDING | PENDING | PENDING | Tách references theo pipeline phase: agent-taxonomy / frontmatter-guide / body-guide / validation. Anti-patterns rải vào file chủ đề tương ứng. |
