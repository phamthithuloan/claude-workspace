# Frontmatter Guide

Tất cả về frontmatter của agent: name, description, tools, optional fields. Dùng ở Step 4 (name) và Step 5 (description, tools, fields).

Chỉ `name` và `description` là required. Mọi field khác opt-in.

## Contents

- Name
- Description
- Tools
- Optional fields — nguyên tắc chung
- Optional fields — từng field

---

## Name

Convention: `<domain>-<role>` — vd `code-reviewer`, `seo-specialist`, `db-reader`.

Validate cứng:

- Chỉ gồm chữ thường, số và dấu gạch ngang
- ≤64 ký tự
- KHÔNG chứa "claude" hoặc "anthropic" (Anthropic constraint — agent sẽ bị reject)
- KHÔNG chứa XML tag
- KHÔNG trùng tên ở scope ưu tiên cao hơn

Check trùng tên bằng Glob tool (cross-platform, không cần shell): pattern `.claude/agents/<name>.md` (project scope) và `~/.claude/agents/<name>.md` (user scope). File đã tồn tại → trùng, phải đổi tên.

Thứ tự ưu tiên scope (cao → thấp): managed settings → CLI `--agents` flag → `.claude/agents/` (project) → `~/.claude/agents/` (user) → plugin agents. Khi hai agent trùng tên, scope cao thắng; bản còn lại bị bỏ đi mà không cảnh báo.

**Lỗi thường gặp — trùng tên giữa các scope.** Project có `code-reviewer.md` (behavior A), user cũng có `code-reviewer.md` (behavior B) → project thắng, bản user bị shadow âm thầm. Tránh: agent project đặt tên domain-specific (`seo-specialist`, `wp-publisher`), agent user đặt tên generic (`code-reviewer`, `data-scientist`).

Fail bất kỳ điều kiện nào → đề xuất 3 tên thay thế kèm lý do mỗi tên.

---

## Description

Quy tắc cứng:

- ≤1024 ký tự
- Một câu nêu agent làm gì
- Có cụm "Use proactively" hoặc "Proactively"
- Nêu trigger condition cụ thể — user task nào thì parent dispatch agent này
- KHÔNG chứa XML tag

Vì sao bắt buộc "Use proactively": docs ghi Claude dùng `description` để quyết định khi nào delegate. Description mơ hồ → parent UNDERTRIGGER agent, user phải gõ `@<name>` thủ công mỗi lần.

Bad:

```yaml
description: Helps with code stuff.
```

```yaml
description: A reviewer agent.
```

Good:

```yaml
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability. Use immediately after writing or modifying code.
```

**Lỗi thường gặp — dấu `: ` làm vỡ YAML.** `description` là một YAML scalar không đặt trong dấu nháy. Nếu giá trị chứa `: ` (hai chấm + dấu cách) — vd viết "3 types: Reviewer/Specialist/..." — YAML parser hiểu nhầm đó là mapping lồng nhau và báo lỗi "mapping values are not allowed". Fix: tránh `: ` trong description — đổi sang dấu gạch ngang ("3 types — Reviewer/...") hoặc bỏ dấu cách sau hai chấm.

---

## Tools

Cấp tool theo nguyên tắc minimum-necessary. Mỗi tool nạp schema vào context của subagent — over-grant gây bloat và mở rộng risk surface. Strip tool nào body không dùng tới.

### Default theo type

- **Reviewer** — `Read, Grep, Glob, Bash`. Bash chỉ cho test/linter/git diff, read-only. KHÔNG Write/Edit (Reviewer critique, không fix). Ngoại lệ: reviewer review URL ngoài → thêm `WebFetch`.
- **Specialist** — `Bash, Read, Write, Edit, Glob, Grep`. Thêm khi cần: `Skill` (invoke skills), `WebFetch`/`WebSearch` (external data), `Agent` (chỉ có tác dụng khi run as main session).
- **Restricted-Doer** — `Bash`, hoặc 1-3 tool cụ thể. Thường kèm `hooks` để validate runtime.

### Tools matrix

| Tool | Reviewer | Specialist | Restricted-Doer |
|---|---|---|---|
| `Read` | YES | YES | maybe |
| `Glob` | YES | YES | NO |
| `Grep` | YES | YES | NO |
| `Bash` | YES (read-only) | YES | maybe (kèm hook) |
| `Write` | NO | YES | maybe |
| `Edit` | NO | YES | maybe |
| `WebFetch` | maybe | maybe | NO |
| `WebSearch` | NO | maybe | NO |
| `Agent` | NO | chỉ khi run as main | NO |
| `Skill` | maybe | YES | NO |
| `AskUserQuestion` | NO | maybe (foreground only) | NO |

YES = default include. NO = default exclude. maybe = chỉ include nếu có use case cụ thể.

`Agent` tool chỉ có tác dụng khi agent chạy as main session (`claude --agent`). Subagent không spawn được subagent, nên `Agent` trong định nghĩa subagent là vô hiệu.

### `disallowedTools` — denylist

Khi muốn agent kế thừa toàn bộ tool trừ 1-2 cái, dùng denylist gọn hơn allowlist:

```yaml
disallowedTools: Write, Edit
```

Khi set cả `tools` và `disallowedTools`: `disallowedTools` áp dụng trước, rồi `tools` mới resolve.

### Verify sau khi chọn tool

- Mỗi tool trong frontmatter có ≥1 use case nhắc trong body
- KHÔNG tool nào body nhắc nhưng frontmatter thiếu
- Write/Edit chỉ có nếu agent thật sự modify file
- Bash giới hạn read-only trong body nếu agent thuộc nhóm read-only

**Lỗi thường gặp — over-grant tools.** Cho Reviewer cả `Write, Edit, Agent, WebFetch`. Fix: theo matrix, strip mọi tool body không nhắc tới.

---

## Optional fields — nguyên tắc chung

**Mặc định SKIP tất cả.** Default frontmatter chỉ gồm `name`, `description`, `tools`, `color`. Mọi field khác opt-in.

Với mỗi field định set, trả lời: *"Có user explicit ask field này, hoặc có requirement objective rõ ràng không?"* Nếu câu trả lời là "tôi nghĩ nên có" / "thường thì..." / "có thể useful" → SKIP. Default behavior tốt hơn một field thừa.

**Lỗi thường gặp nhất — auto-set field từ tín hiệu mềm của user.** Mỗi field set ép behavior cho MỌI lần dispatch, lấy mất quyền chọn của parent và user.

| User nói | Hành động đúng | Hành động SAI |
|---|---|---|
| "Muốn làm việc khác trong lúc agent chạy" | Hướng dẫn `/agents` switch main session | Set `background: true` |
| "Agent nên thông minh hơn theo thời gian" | Cải thiện body, hoặc không làm gì | Set `memory: project` |
| "Task này phức tạp" | Để parent quyết effort case-by-case | Set `effort: high` |
| "Agent edit nhiều file" | Default OK | Set `isolation: worktree` |
| "Hạn chế cost" | Default OK, hoặc `model: haiku` nếu rõ | Set `maxTurns` đoán mò |

---

## Optional fields — từng field

### `model`

```yaml
model: sonnet | opus | haiku | inherit | claude-opus-4-7
```

- `inherit` (default khi bỏ field) — dùng model của session hiện tại
- `haiku` — task nhanh, cost-sensitive (kiểu Explore)
- `sonnet` — Specialist điều phối workflow phức tạp
- `opus` — reasoning phức tạp, chiến lược

Set khi agent cần model khác hẳn session mặc định. Bỏ khi agent nên match model của user.

### `maxTurns`

Giới hạn cứng số turn của agent, chống agent chạy runaway.

**Default: OMIT** (unlimited, dựa vào auto-compaction). Chỉ set khi cần cost guard cụ thể — vd batch lớn có kích thước đoán trước được. KHÔNG auto-pick một con số.

### `effort`

```yaml
effort: low | medium | high | xhigh | max
```

Override mức effort của session. **Default: OMIT** (kế thừa `medium`). Set `high` chỉ khi task chứng minh được cần reasoning sâu hơn (vd debug nhiều bước). "Task có vẻ phức tạp" KHÔNG đủ lý do.

### `background`

```yaml
background: true
```

Khi set, Claude Code TỰ chạy agent này dưới dạng background task mỗi lần spawn. Không cần prose hint, không cần parent pass `run_in_background`.

**Default: OMIT.** Không set field → parent tự quyết foreground/background theo ngữ cảnh từng task; user cũng có thể yêu cầu "run in background" hoặc Ctrl+B.

Foreground vs background (docs):

> Foreground subagents block the main conversation until complete. Permission prompts are passed through to you.
> Background subagents run concurrently while you keep working. They use the permissions already granted in the session and auto-deny any tool call that would otherwise prompt.

Hệ quả: background agent KHÔNG hỏi được user giữa chừng (mọi prompt bị auto-deny), KHÔNG dùng được `AskUserQuestion` giữa chừng.

Khi nào set `background: true`:

| Tình huống | Set? |
|---|---|
| Agent thiết kế cho unattended-only batch (nightly cron, deploy pipeline, research chạy hàng giờ) | YES |
| Task >5 phút nhưng user thường ngồi theo dõi | NO — để parent quyết |
| User nói "muốn làm việc khác" | NO — đó là dispatch decision, hoặc giải bằng `/agents` switch main session |
| Agent cần user input giữa chừng | NO — auto-deny làm hỏng tương tác |
| Quick task <2 phút | NO — switching cost lớn hơn lợi ích |

Tắt background toàn cục: env var `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1`.

**Lỗi thường gặp — `background: true` cho agent cần tương tác.** Agent có `AskUserQuestion` hoặc cần confirm thao tác destructive mà set background → mọi prompt bị auto-deny, agent fail âm thầm. Background mode đòi agent hoàn toàn tự chủ.

**Lỗi thường gặp — prose hack thay vì field.** Nhồi prose kiểu "BACKGROUND MODE — BẮT BUỘC: khi user nói X, parent phải invoke với run_in_background=true" vào `description` để ép parent dispatch, thay vì set `background: true`. Prose instruction không đáng tin — parent có thể không follow. Dùng field.

### `skills`

```yaml
skills:
  - api-conventions
  - error-handling-patterns
```

Preload full nội dung skill vào context của subagent lúc startup. Agent vẫn discover được skill khác qua `Skill` tool lúc chạy — cần `Skill` trong `tools` (default tool set của 3 type không có sẵn).

Set khi: Specialist cần domain knowledge luôn sẵn có. Bỏ khi: skill chỉ cần cho một số task → để agent tự discover; hoặc skill content lớn (>5KB) mà không phải lúc nào cũng cần → tránh bloat.

### `memory`

```yaml
memory: project | user | local
```

- `project` — checked vào git, team share
- `user` — học cá nhân, áp mọi project
- `local` — không vào git

Bật một thư mục persistent `<scope>/agent-memory/<name>/` cho agent học qua nhiều session, tự động enable Read/Write/Edit cho riêng thư mục đó.

**Default: OMIT.** Chỉ set khi đồng thời 2 điều kiện: (1) user explicit ask agent học pattern qua sessions, và (2) body có section "Memory usage" hướng dẫn đọc-trước / ghi-sau cụ thể.

**Lỗi thường gặp — set `memory` nhưng body không hướng dẫn.** Field bật thư mục + quyền truy cập, nhưng nếu body không nhắc gì tới memory thì agent gần như không dùng → field lãng phí. Thiếu 1 trong 2 điều kiện trên → SKIP field.

### `isolation`

```yaml
isolation: worktree
```

Chạy agent trong một git worktree tạm — bản sao repo cô lập, tự dọn nếu agent không thay đổi gì.

Set khi: agent modify nhiều file và user muốn xem trước khi merge, hoặc nhiều agent chạy song song trên cùng repo. Bỏ khi: agent read-only, hoặc agent cần các thay đổi chưa commit hiện tại (worktree bắt đầu từ HEAD).

### `permissionMode`

```yaml
permissionMode: default | acceptEdits | auto | dontAsk | bypassPermissions | plan
```

| Mode | Use case |
|---|---|
| `default` | Prompt chuẩn — đa số agent |
| `acceptEdits` | Tự nhận edit trong working dir — Specialist tin cậy cao |
| `auto` | Auto theo classifier — tin cậy vừa |
| `dontAsk` | Auto-deny prompt — Restricted-Doer chỉ làm thao tác đã allow |
| `bypassPermissions` | Bỏ mọi prompt — NGUY HIỂM, chỉ cho autonomous bot |
| `plan` | Read-only plan mode — research agent |

**Lỗi thường gặp — `bypassPermissions` tùy tiện.** Docs cảnh báo: nó bỏ mọi permission prompt, cho phép subagent ghi cả vào `.git`, `.claude`, `.vscode`. Chỉ dùng cho autonomous bot có tool đã scope chặt, trong môi trường tin cậy, và phải ghi rõ lý do trong body.

**Lỗi thường gặp — quên parent takes precedence.** Nếu parent đang dùng `bypassPermissions` hoặc `acceptEdits`, mode đó thắng và `permissionMode` trong frontmatter subagent bị bỏ qua. Đừng giả định set `permissionMode: default` là agent luôn prompt user. Cần enforce chắc → dùng `hooks` (PreToolUse).

### `mcpServers`

```yaml
mcpServers:
  - playwright:
      type: stdio
      command: npx
      args: ["-y", "@playwright/mcp@latest"]
```

Cho agent truy cập MCP server không có trong main session. Định nghĩa inline chỉ scope cho agent này. Set khi: agent cần MCP cụ thể (vd browser testing cần Playwright) mà không muốn bloat main session.

### `hooks`

```yaml
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-cmd.sh"
```

Lifecycle hook scope cho agent, dùng validate/automation runtime. Pattern thường gặp: `PreToolUse` validate Bash command (block thao tác destructive), `PostToolUse` chạy linter sau Edit/Write. Chủ yếu cho Restricted-Doer cần validation có điều kiện — tốt hơn allowlist `tools` tĩnh.

### `initialPrompt`

```yaml
initialPrompt: |
  Start by listing recent PRs and identifying which need review.
```

Tự submit như turn đầu tiên khi agent chạy as MAIN session (`claude --agent` hoặc `agent` setting). Set khi: agent thiết kế cho `claude --agent <name>` và luôn muốn cùng một hành vi mở đầu. Bỏ khi: agent chỉ chạy as subagent (field bị bỏ qua).

### `color`

```yaml
color: red | blue | green | yellow | purple | orange | pink | cyan
```

Màu chip UI trong task list và transcript. Convention: `blue` Reviewer, `green` Specialist, `yellow` Restricted-Doer, `red` agent có khả năng destructive, `purple` research/exploration.
