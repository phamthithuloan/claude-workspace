<!-- Skeleton agent .md. Điền các [BRACKETED] placeholder, xóa optional field/section không dùng, rồi xóa hết comment trong file này. -->
---
name: [AGENT_NAME]
description: [One sentence what + key triggers]. Use proactively when [conditions].
tools: [comma-separated minimum-necessary tools]
color: [blue Reviewer | green Specialist | yellow Restricted-Doer]
# Optional fields — mặc định BỎ HẾT. Cần field nào thì uncomment; điều kiện set xem frontmatter-guide.md.
# model: [sonnet | opus | haiku]
# maxTurns: [integer]
# effort: [low | high | xhigh | max]
# background: true
# memory: [user | project | local]
# skills:
#   - [skill-name]
# isolation: worktree
# permissionMode: [acceptEdits | dontAsk]
# mcpServers: [...]
# hooks: {...}
# initialPrompt: [text]
# disallowedTools: [list]
---

You are [AGENT_NAME] — [ONE_SENTENCE_ROLE].

## When invoked

1. [First step — parse input or read target]
2. [Second step — apply logic]
3. [Final step — return result to parent]

## Output format

```
[Concrete output structure — markdown sections, JSON, table]
```

## Tools usage

- `[Tool 1]`: [when]
- `[Tool 2]`: [when]

## When to stop and report blocker

Return early thay vì đoán nếu:

- [Condition 1 — input ambiguous]
- [Condition 2 — destructive action chưa authorize]
- [Condition 3 — task out-of-scope]

## Constraints

- [Hard rule 1]
- [Hard rule 2]

<!--
Optional section — thêm vào body nếu agent cần, xóa phần không dùng:

## Memory usage          (nếu có field memory)
Consult memory before work. Update memory after work with new patterns/decisions.

## Skills + parallel constraint   (nếu có field skills và agent dùng Agent tool)
| Mode         | Parallel                 | Cách invoke        |
|--------------|--------------------------|--------------------|
| Main session | YES                      | /agents chọn <name> |
| Subagent     | NO (fallback sequential) | parent dispatch    |

## Progress reporting    (nếu có field background: true)
Print `[PROGRESS] <PHASE> | <STATUS> | <DETAIL>` tại mỗi checkpoint.

## When run as main session   (nếu agent designed cho --agent / /agents switch)
Recommended invoke: /agents chọn <name>. Khi là main thread: spawn parallel sub-agents bình thường.
-->
