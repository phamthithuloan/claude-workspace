<!-- Skeleton SKILL.md theo CHUẨN SEONGON 6 TIÊU CHÍ. Điền [BRACKETED] placeholder, xóa section không áp dụng (vd Reference skill bỏ Pipeline), rồi xóa hết comment trong file này. Cấu trúc body riêng theo dạng skill: xem skill-taxonomy.md. -->

<!-- TIÊU CHÍ 1 — FRONTMATTER ĐẦY ĐỦ -->
---
name: [SKILL_NAME]
description: "This skill should be used when the user asks to [TRIGGER_PHRASE_1], [TRIGGER_PHRASE_2_VN], [TRIGGER_PHRASE_3], [TRIGGER_PHRASE_4_VN], or [CONTEXT_TRIGGER]. [WHAT_SKILL_DOES_1_SENTENCE]. [WHEN_TO_USE_1_SENTENCE]."
user-invokable: true
argument-hint: "[INPUT_PATTERN]"
license: MIT
compatibility: "[DEPENDENCIES — vd: Free, hoặc Requires Canva MCP + Drive MCP]"
metadata:
  author: Seongon Content
  version: "1.0.0"
  category: [content | seo | design | ops]
---

# [SKILL_NAME]

[1-2 câu mô tả skill làm gì và đối tượng dùng]

## Khi nào dùng

User nói một trong các pattern:

- `/[SKILL_NAME] [argument-hint]`
- "[TRIGGER_PHRASE_1]"
- "[TRIGGER_PHRASE_2]"
- "[TRIGGER_PHRASE_3]"

KHÔNG dùng skill này khi:

- [NEGATIVE_TRIGGER_1]
- [NEGATIVE_TRIGGER_2]

## Tiền điều kiện

- [ ] [CONDITION_1]
- [ ] [CONDITION_2]

## Default settings

| Setting | Default | Override khi |
|---|---|---|
| [SETTING_1] | [DEFAULT_1] | [OVERRIDE_CONDITION_1] |
| [SETTING_2] | [DEFAULT_2] | [OVERRIDE_CONDITION_2] |

<!-- TIÊU CHÍ 2 — QUY TRÌNH TỪNG BƯỚC CHI TIẾT -->
## Pipeline — [N] bước

[Reference skill: thay section này bằng "Core principles" / "Domain knowledge" — không có numbered step.]

Theo thứ tự, không skip.

### Step 1 — [STEP_NAME]

[Imperative instruction. Commands cụ thể (bash/regex/tool call template). Exit condition.]

### Step 2 — [STEP_NAME]

[Imperative instruction.]

**Decision point**: [khi nào hỏi user vs auto-proceed]

### Step N — [FINAL_STEP]

[Output format. Self-validation. Report to user.]

<!-- TIÊU CHÍ 6 — OUTPUT FORMAT RÕ RÀNG -->
## Output format

```
[OUTPUT_TEMPLATE_CỤ_THỂ — table / JSON / markdown structure]
```

<!-- TIÊU CHÍ 4 — ERROR HANDLING 3 CỘT (Error | Cause | Resolution) -->
## Error Handling (Vết xe đổ)

| Error | Cause | Resolution |
|---|---|---|
| [ERROR_MESSAGE_1] | [ROOT_CAUSE_1] | [ACTION_STEPS_1] |
| [ERROR_MESSAGE_2] | [ROOT_CAUSE_2] | [ACTION_STEPS_2] |
| [ERROR_MESSAGE_3] | [ROOT_CAUSE_3] | [ACTION_STEPS_3] |

<!-- TIÊU CHÍ 5 — PRE-DELIVERY REVIEW CHECKLIST -->
## Pre-Delivery Review (MANDATORY)

Trước khi return final output, skill PHẢI tự verify checklist sau. KHÔNG ship nếu fail item nào — báo gap thay vì giả pretend complete.

### [CATEGORY_1 — vd: Content integrity]
- [ ] [CHECK_1]
- [ ] [CHECK_2]

### [CATEGORY_2 — vd: Format compliance]
- [ ] [CHECK_1]
- [ ] [CHECK_2]

### [CATEGORY_3 — vd: Output quality]
- [ ] [CHECK_1]
- [ ] [CHECK_2]

Fail ≥1 item → KHÔNG return final, fix gap hoặc note "INSUFFICIENT DATA".

## Anti-patterns

- KHÔNG [ANTI_PATTERN_1]
  - BAD: [BAD_EXAMPLE]
  - GOOD: [GOOD_EXAMPLE]
- KHÔNG [ANTI_PATTERN_2]
- KHÔNG [ANTI_PATTERN_3]

<!-- TIÊU CHÍ 3 — FILE BỔ TRỢ LOAD-ON-DEMAND -->
## Skill files

| File | Purpose | Load when |
|---|---|---|
| `references/[FILE_1].md` | [PURPOSE_1] | [LOAD_CONDITION_1 — step nào trigger Read] |
| `assets/[FILE_2]` | [PURPOSE_2] | [USED_IN_OUTPUT] |
| `scripts/[FILE_3]` | [PURPOSE_3] | [EXECUTION] |
| `EVALS.md` | 3 test scenario (Golden/Edge/Anti) | Manual test sau khi edit SKILL.md |
