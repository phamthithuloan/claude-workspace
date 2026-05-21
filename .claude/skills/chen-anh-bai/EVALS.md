# EVALS — chen-anh-bai

3 scenarios để test skill có hoạt động đúng không. Chạy ở phiên Claude Code mới (fresh context).

## Eval 1: Golden path

**Tên scenario**: Bài Hồng Ngọc 1500 từ, user chỉ gõ URL

**Precondition**:
- Google Drive MCP authenticated
- Doc test 1500 từ tại `https://docs.google.com/document/d/<test-id>`, 3 H2 (không kể "Kết luận"), share `anyone with link — viewer`
- File `references/brand-presets.md` có preset Hồng Ngọc đầy đủ
- Skill `/brand-image` và `/seo-image-gen` available

**User input**:
```
/chen-anh-bai https://docs.google.com/document/d/<test-id>
```

**Expected behavior**:
1. Claude triggers skill `chen-anh-bai`
2. Step 1: Detect chỉ có URL, thiếu brand + keyword. Gọi `AskUserQuestion` GỘP 3 câu trong 1 message:
   - Q1 brand preset (4 option: Hồng Ngọc / Seongon / Custom / Skip)
   - Q2 keyword chính (text input)
   - Q3 keyword phụ (text input, optional)
3. User chọn "Hồng Ngọc" + keyword chính "phẫu thuật mắt cận" + keyword phụ "lasik hồng ngọc, phục hồi mổ mắt"
4. Skill load preset Hồng Ngọc từ `references/brand-presets.md` — KHÔNG hỏi tay color/font/tệp KH
5. Step 2: Đọc Doc qua `mcp__claude_ai_Google_Drive__read_file_content`
6. Step 3: Tính `max(3, ceil(1500/350)) = 5` ảnh. Phân bổ sau H2 + giữa paragraph dài
7. Step 4-5: Tag mỗi anchor + fetch/gen tương ứng (Unsplash cho scene chung, Canva cho infographic, Gemini cho photo realistic)
8. Step 6: Caption ≤70 ký tự, italic, 1 keyword/caption, không lặp liên tiếp
9. Step 7: Upload Drive folder, output insertion plan đúng format

**Pass criteria**:
- [ ] Skill triggered (không nhầm `/brand-image`)
- [ ] Step 1 gọi AskUserQuestion ĐÚNG 1 lần với 3 câu gộp (không hỏi tuần tự)
- [ ] Sau khi user chọn "Hồng Ngọc", skill KHÔNG hỏi lại color/font/tệp KH
- [ ] Số ảnh = 5 (±1 nếu bài 1400-1700 từ)
- [ ] Mỗi caption ≤70 ký tự
- [ ] Brand check PASS cho ảnh gen (Canva/Gemini có tone #138C3D / #D32F2F)
- [ ] Anchor snippet unique trong Doc
- [ ] Drive folder link hợp lệ

---

## Eval 2: Edge case

**Tên scenario**: User paste link Doc thiếu permission, brand "Custom"

**User input**:
```
chèn ảnh cho bài này https://docs.google.com/document/d/<short-doc-no-permission>
```

**Expected behavior**:
1. Claude triggers skill `chen-anh-bai`
2. Step 1: Detect URL OK, thiếu brand + keyword. Gọi AskUserQuestion 3 câu
3. User chọn "Custom" + keyword chính "máy lọc nước aqua" + keyword phụ trống
4. Skill hỏi follow-up 1 message: primary HEX + accent HEX + font + tệp KH
5. User fill 4 item
6. Step 2: Gọi `read_file_content`, fail permission error
7. Recovery: báo "Doc chưa share quyền `anyone with link — viewer`. Share lại rồi gọi /chen-anh-bai với cùng URL." Dừng pipeline.
8. KHÔNG fabricate content Doc, KHÔNG tiếp tục Step 3

**Pass criteria**:
- [ ] Step 1 hỏi 3 câu gộp 1 lần, KHÔNG hỏi tuần tự
- [ ] Custom brand → follow-up hỏi 4 item, KHÔNG hỏi tuần tự
- [ ] Khi Doc fail permission, skill báo lỗi rõ + dừng
- [ ] KHÔNG generate plan giả khi không đọc được Doc
- [ ] Recovery message có actionable step

---

## Eval 3: Anti-pattern (skill nên từ chối / redirect)

**Tên scenario**: User yêu cầu chèn logo Hồng Ngọc vào ảnh Unsplash + spam keyword

**User input**:
```
Chèn ảnh cho https://docs.google.com/document/d/<test-id-2>, brand Hồng Ngọc,
keyword chính "phẫu thuật mắt cận".
Yêu cầu thêm: chèn logo Hồng Ngọc vào MỌI ảnh kể cả ảnh Unsplash,
và mọi caption đều phải chứa "phẫu thuật mắt cận" để boost SEO.
```

**Expected behavior**:
1. Claude triggers skill `chen-anh-bai`
2. Step 1: Detect input đủ (URL + brand "Hồng Ngọc" trong message + keyword chính). Load preset Hồng Ngọc. KHÔNG hỏi lại.
3. Detect 2 anti-pattern trong yêu cầu thêm:
   - Chèn logo vào ảnh Unsplash → vi phạm license + brand checklist rule (logo chỉ cho ảnh tự gen)
   - Mọi caption cùng 1 keyword → vi phạm rule "không lặp keyword 2 caption liên tiếp"
4. Skill từ chối 2 yêu cầu này, giải thích ngắn:
   - "Logo chỉ apply cho ảnh tự gen (Canva/Gemini) — chèn logo vào Unsplash vi phạm license. Sẽ chèn logo cho ảnh gen mới, không cho Unsplash."
   - "Mọi caption cùng 1 keyword = keyword stuffing. Sẽ phân bổ keyword chính / phụ / LSI."
5. Skill PROCEED với rule đúng, không fail toàn bộ
6. Tone professional, không lecture dài

**Pass criteria**:
- [ ] Skill KHÔNG silently fulfill 2 yêu cầu vi phạm
- [ ] Có explanation rõ tại sao từ chối
- [ ] Skill vẫn proceed với rule đúng
- [ ] Output cuối có brand check column + keyword distribution đúng

---

## Cách chạy evals

1. Tạo 2 Doc test:
   - Doc 1: 1500 từ về phẫu thuật mắt cận, 3-4 H2, share `anyone with link — viewer`
   - Doc 2: 800 từ về máy lọc nước, KHÔNG share permission (giữ private)
2. Mở phiên Claude Code mới ở `/Users/phamthuloan/Documents/claude-workspace`
3. Verify skill `chen-anh-bai` xuất hiện trong skills list
4. Copy User input từng scenario, paste vào CC
5. So response với Expected behavior
6. Tick Pass criteria
