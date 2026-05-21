# Body Guide

Quy tắc viết phần body của agent `.md`. Dùng ở Step 6.

Body của agent trở thành system prompt cho subagent đó — mỗi lần dispatch, full body được nạp vào context. Body gọn và rõ trực tiếp ảnh hưởng chất lượng và chi phí.

Cấu trúc body RIÊNG cho từng type (Reviewer / Specialist / Restricted-Doer) nằm trong `agent-taxonomy.md`. File này là quy tắc CHUNG, áp cho mọi type.

## Contents

- Quy tắc body
- Section bắt buộc
- KHÔNG cho vào body
- Subagent không spawn được subagent
- Body bloat
- Progress reporting

---

## Quy tắc body

- Viết ở ngôi thứ hai, hướng tới agent — "You are...", "When invoked you...".
- Câu lệnh dùng imperative — "Read X", "Return Y" — không viết passive.
- Target ≤150 dòng, hard cap ≤500. Viết gọn; mỗi section phải justify được sự tồn tại. "Hơi thiếu" tốt hơn "vẽ vời".
- Forward slash cho mọi path, kể cả khi agent target Windows.
- KHÔNG lặp lại thông tin đã có trong frontmatter (vd viết "Agent này dùng model haiku" trong khi đã có `model: haiku`).
- Dùng thuật ngữ nhất quán — chọn một từ và dùng xuyên suốt.

## Section bắt buộc

Mọi agent body cần tối thiểu 3 section:

- **"When invoked"** (hoặc "Quy trình") — quy trình các bước, ≤7 bước, compact.
- **"Output format"** — template cụ thể cho output agent trả về parent.
- **"When to stop and report blocker"** — điều kiện agent dừng và báo về thay vì đoán (input mơ hồ, thao tác destructive chưa được authorize, task ngoài scope).

Section type-specific (Review checklist, Tools usage, Allowed/Blocked operations...) → xem `agent-taxonomy.md`.

## KHÔNG cho vào body

- Logic spawn parallel sub-agent — TRỪ KHI agent được thiết kế chạy as `--agent` main mode.
- Thiết kế đệ quy (agent tự spawn chính nó).
- Prose hack mô tả hành vi dispatch của parent — dùng frontmatter field thay vào đó.
- Section "Progress reporting" — TRỪ KHI `background: true` (xem mục dưới).
- "Quality checklist" tách riêng nếu đã có "Constraints" — gộp làm một.
- Bảng dài so sánh run modes nếu agent chỉ có một mode chính.

## Subagent không spawn được subagent

Anthropic constraint, docs ghi rõ:

> Subagents cannot spawn other subagents. If your workflow requires nested delegation, use Skills or chain subagents from the main conversation.

> Subagents cannot spawn other subagents, so `Agent(agent_type)` has no effect in subagent definitions.

**Lỗi thường gặp.** Body của một Specialist có section "Spawn N parallel sub-agents để xử lý keywords" — sẽ fail khi chính agent đó đang chạy as subagent. Tương tự, thiết kế đệ quy ("nếu task lớn, spawn một bản con của chính mình với phần nhỏ hơn") fail âm thầm hoặc vỡ logic.

Ba cách làm đúng khi cần parallel work:

1. **Thiết kế agent chạy as main session** qua `claude --agent <name>`. Khi là main thread, agent spawn được sub-agent. Body ghi rõ: "When run as main session, this agent can spawn parallel sub-agents."
2. **Chuyển orchestration vào Skill.** Skill chạy trong main context và spawn được agent. Main → invoke skill → skill spawn parallel agents.
3. **Chain subagent từ main conversation.** Main dispatch reviewer-1 (xong) → dispatch reviewer-2 (xong) → main tổng hợp. Phân rã ở cấp parent, không lồng nhau.

Khi classify một Specialist có nhu cầu orchestration, hỏi user agent chạy as main session hay as subagent — quyết định này đổi cách viết body.

## Body bloat

Body >500 dòng = system prompt khổng lồ nạp vào context mỗi lần dispatch, đẩy chỗ làm việc thật ra khỏi window.

**Lỗi thường gặp.** Một file agent `.md` 800 dòng nhồi inline cả checklist, examples, anti-patterns, edge cases.

Fix: target body 200-400 dòng. Vượt 500 → tách folder structure:

```
.claude/agents/
└── seo-specialist/
    ├── seo-specialist.md       # ~250 dòng — core
    └── references/
        ├── domain-knowledge.md  # load khi cần
        └── examples.md
```

Body chỉ để pointer: "For domain X edge cases, read references/domain-knowledge.md." Hoặc dùng field `skills` để preload knowledge cụ thể.

## Progress reporting

Convention project-local, KHÔNG phải feature chính thức của Anthropic. Chỉ thêm section này vào body khi agent có `background: true` và chạy lâu (>5 phút) — agent foreground user thấy real-time nên không cần.

Vấn đề nó giải: background agent chạy 15 phút không in gì, user hỏi "đến đâu rồi" thì không có gì để fetch.

### Marker `[PROGRESS]`

Agent in dòng theo format:

```
[PROGRESS] <PHASE> | <STATUS> | <DETAIL>
```

- `<PHASE>` — UPPERCASE_SNAKE: `INIT`, `READ_INPUT`, `WORK_PHASE_1`, `DONE`
- `<STATUS>` — chuỗi ngắn: "Đang chạy", "5/10 xong", "Failed"
- `<DETAIL>` — context 1 dòng: keyword, file path, error message

In ngay tại checkpoint, không gom lại in một lần. Tổng 5-30 marker cho một task trung bình.

### Section template cho body

```markdown
## Progress reporting

This agent emits structured progress markers. Print at each checkpoint:

[PROGRESS] <PHASE> | <STATUS> | <DETAIL>

Checkpoints:
- [PROGRESS] INIT | Started | <input summary> — at agent start
- [PROGRESS] <PHASE_N> | <progress> | <detail> — at each phase boundary
- [PROGRESS] DONE | Complete | <summary> — before final return
- [PROGRESS] ERROR | <phase> | <detail> — on any error (do not stay silent)
```

### Giới hạn

Anthropic docs KHÔNG document tool nào fetch marker một cách structured. Marker giúp ích nhưng không phải kênh monitoring đáng tin cậy. Khi user cần monitoring chắc, hướng họ tới `/agents` UI ("Running" tab) hoặc agent teams (`/en/agent-teams`).

**Lỗi thường gặp — claim marker là official.** Đừng viết `[PROGRESS]` như pattern Anthropic hỗ trợ. Ghi rõ trong body rằng đây là project convention.
