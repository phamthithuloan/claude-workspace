# Body Guide

Quy tắc viết body `SKILL.md` và tạo bundled resources. Dùng ở Step 6 (body) và Step 7 (resources).

File này là quy tắc body CHUNG, áp cho mọi dạng skill.

## Contents

- Progressive disclosure
- Quy tắc body
- Bundled resources
- Anti-patterns

---

## Progressive disclosure

Skill có 3 tầng, nội dung vào context theo nhu cầu — đây là lý do tách file:

| Tầng | Cái gì | Vào context khi nào |
|---|---|---|
| 1 | `name` + `description` (frontmatter) | Luôn luôn (~100 token/skill) |
| 2 | Body `SKILL.md` | Khi skill được trigger (nên giữ dưới ~5000 token) |
| 3 | `references/`, `scripts/`, `assets/` | Khi Claude chủ động đọc/chạy — gần như không giới hạn |

→ Viết body gọn; kiến thức dài đẩy xuống tầng 3. Body phình = chiếm context mỗi lần skill chạy.

## Quy tắc body

- **Imperative form** — body viết cho Claude (một instance khác) đọc, không phải cho user. "Tạo X", "Hỏi user Y", "Validate Z". KHÔNG "Bạn nên...", "You should...".
- **≤500 dòng** — guidance chính thức của Anthropic: *"Keep SKILL.md under 500 lines. Move detailed reference material to separate files."* Ideal 1500-2000 từ.
- **Forward slash** cho mọi path, kể cả khi skill target Windows.
- **Thuật ngữ nhất quán** — chọn 1 từ và dùng xuyên suốt.
- Cấu trúc tối thiểu: section "Khi nào dùng" (gồm cả negative trigger "KHÔNG dùng khi..."), và — với Task skill — một pipeline numbered. Reference skill KHÔNG có pipeline.
- Scope rõ ràng: nếu skill ở `.claude/skills/` (project), đừng viết trong body rằng nó "available everywhere" — gây hiểu nhầm. Nói rõ project-scoped vs user-scoped.

## Bundled resources

Step 7 tạo các thư mục con khi skill cần. **Tên thư mục `references/` `scripts/` `assets/` là convention cộng đồng, không bắt buộc** — chỉ `SKILL.md` là bắt buộc. Nhưng dùng đúng convention giúp nhất quán.

Ba thư mục, ba mục đích khác nhau — KHÔNG gộp:

- **`references/`** — tài liệu Claude `Read` khi cần. KHÔNG vào context tới khi được đọc. Mỗi file `references/*.md` >100 dòng phải có Table of Contents ở đầu.
- **`scripts/`** — code chạy được. Claude execute, chỉ output vào context (không phải source code). Dùng cho việc deterministic (parse, fetch, validate).
- **`assets/`** — file template / hình ảnh được copy hoặc chỉnh ra output. KHÔNG phải để đọc-hiểu mà để dùng làm vật liệu.

Auto-suggest theo dạng skill: Reference skill hầu như luôn cần `references/`; Generator/Research cần `assets/` chứa template; Analyzer thường cần `scripts/`.

Mọi file con tạo ra phải được SKILL.md (hoặc file điều phối) trỏ tới — không để file mồ côi.

## Anti-patterns

### Second person trong body

Body là system prompt cho Claude, không phải hướng dẫn cho user. "Bạn nên đọc config" → sửa thành "Đọc config". Imperative ngắn hơn, ít mơ hồ.

### Nhồi mọi thứ vào SKILL.md

Body 5000+ từ chứa cả pipeline + advanced patterns + API ref + ví dụ → context bloat mỗi lần trigger. Cứng: ≤500 dòng. Detail dài → `references/<topic>.md`, body chỉ để pointer 1 dòng.

### Nested references

`SKILL.md` link tới `references/a.md`, `a.md` lại link `references/b.md`, `b.md` link tiếp... Claude hay đọc partial (`head`) khi follow chain → sót thông tin. **Quy tắc: mọi reference link 1 cấp từ SKILL.md. File references KHÔNG link tới file references khác.**

### Time-sensitive info

"Trong 2026...", "Sau Q2 2026...", "API mới ra tháng trước..." → 6 tháng sau sai. Wrap thông tin lịch sử trong section `## Old patterns` (dùng `<details>` cho gọn), hoặc bỏ hẳn.

### Windows-style paths

`scripts\helper.py`, `references\guide.md` — backslash lỗi trên Unix. Luôn forward slash `/` trong body, kể cả skill target Windows. Ngoại lệ: lệnh PowerShell native trong `scripts/*.ps1`.

### Offering too many options

"Để xử lý PDF, có thể dùng pypdf, hoặc pdfplumber, hoặc PyMuPDF, hoặc..." → user rối, Claude lưỡng lự. Mỗi decision point đưa **1 default + 1 alternative cho edge case**, không liệt kê 5 lựa chọn.

### Voodoo constants trong scripts

`TIMEOUT = 47` không giải thích → magic number, Claude không biết khi nào chỉnh. Mỗi config value trong `scripts/` kèm 1 dòng comment giải thích WHY giá trị đó.
