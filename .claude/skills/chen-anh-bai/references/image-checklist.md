# Image Checklist — Full Rules

Nguồn rule cho `inserting-blog-images`. Load khi cần chi tiết về UX, vị trí, caption, brand định vị.

## Contents

- Trải nghiệm người dùng
- Vị trí ảnh
- Chú thích ảnh (caption)
- Định vị thương hiệu
- Quy tắc license + watermark

---

## Trải nghiệm người dùng

- Ảnh nét, KHÔNG bị vỡ/mờ. Resolution tối thiểu 1200px chiều dài cạnh dài. Aspect ratio ưu tiên 16:9 hoặc 4:3.
- Bài viết ≥1000 chữ tối thiểu 3 hình. Quy tắc cứng: 1 hình / 300-400 chữ. Skill dùng `ceil(wordcount / 350)`.
- Hình ảnh liên quan đến nội dung bài viết và đoạn văn xung quanh. Cách kiểm: đọc paragraph trên + dưới anchor, hình phải minh họa concept chính của 2 paragraph đó.
- KHÔNG dùng ảnh chỉ vì "đẹp" mà không liên quan nội dung.

## Vị trí ảnh

- Căn giữa (cứng).
- Ưu tiên đặt sau heading H2 hoặc H3.
- Slot thứ 2 trở đi đặt giữa paragraph dài (>200 từ).
- KHÔNG đặt 2 ảnh cách nhau <200 từ.
- KHÔNG đặt ảnh trong H2 "Kết luận", "FAQ", "Tham khảo", "Câu hỏi thường gặp".
- KHÔNG đặt ảnh ngay sau title H1 nếu đã có hero image (skill không tạo hero — dùng `/brand-image` hoặc `/seo-image-gen` riêng).

## Chú thích ảnh (caption)

Rule cứng:

- ≤70 ký tự (bao gồm dấu câu, space).
- In nghiêng. Markdown: `*caption text*`. Trong Doc: italic + căn giữa, font cỡ nhỏ hơn body 1-2pt.
- Chứa 1 keyword: keyword chính HOẶC keyword phụ HOẶC LSI keyword. KHÔNG nhồi nhiều keyword 1 caption.
- Mô tả rõ và dễ hiểu nội dung hình ảnh. KHÔNG lặp lại paragraph xung quanh.

Phân bổ keyword:

- Ảnh đầu tiên: keyword chính.
- Ảnh giữa: keyword phụ.
- Ảnh cuối: LSI hoặc lặp lại keyword phụ khác.
- KHÔNG dùng cùng 1 keyword trong 2 caption LIÊN TIẾP (anti over-optimization).

Caption example (giả sử keyword chính = "phẫu thuật mắt cận"):

- BAD: "Hình ảnh phẫu thuật mắt cận tại phòng khám." (45 ký tự — OK length nhưng quá generic, không bổ sung context)
- BAD: "Phẫu thuật mắt cận an toàn." (28 ký tự — quá ngắn, không mô tả ảnh)
- GOOD: "*Quy trình phẫu thuật mắt cận Lasik tại Hồng Ngọc — phòng vô khuẩn.*" (65 ký tự, có keyword, mô tả rõ context)

## Định vị thương hiệu

### Logo

- CHỈ chèn logo cho ảnh **tự gen mới** (tag `gen-canva` hoặc `gen-gemini`).
- KHÔNG chèn logo cho ảnh từ Unsplash/Pexels (vi phạm license — ảnh CC0 vẫn không cho phép re-brand kiểu này), hoặc ảnh nguyên bản do user cung cấp (user tự quản logo).
- Vị trí logo: góc dưới phải hoặc góc dưới trái. Opacity 80-100%.
- Logo phải đủ nhỏ để không che nội dung chính (max 12% chiều ngang ảnh).

### Tone màu

- Ảnh gen mới: sử dụng brand primary color làm dominant tone (background, accent).
- Ảnh web-search: ưu tiên chọn ảnh có tone GẦN brand color (vd brand xanh → chọn ảnh có cây cối, nền xanh).
- KHÔNG dùng ảnh có tone đối lập hoàn toàn (vd brand đỏ tươi mà ảnh tone xanh dương lạnh).

### Nhân vật

- Đúng tệp khách hàng mục tiêu của thương hiệu. User phải cung cấp tệp KH (giới tính, độ tuổi, ngành).
- Ví dụ: brand mỹ phẩm cho phụ nữ 25-40 → tránh ảnh đàn ông hoặc cụ già.
- Ví dụ: brand B2B SaaS → ảnh người trong môi trường văn phòng, không ảnh gia đình.
- Skill verify bằng prompt khi gen: bao gồm độ tuổi + giới tính + bối cảnh trong prompt.

### Ưu tiên ảnh nguyên bản

Thứ tự ưu tiên cho ảnh minh họa quy trình, sản phẩm, kết quả, case study:

1. **Original** — do user/tổ chức tự chụp/quay. Nhất luôn.
2. **Gen brand-aligned** — Canva với brand template + logo.
3. **Stock photo** — Unsplash/Pexels.
4. **AI-generated photorealistic** — Gemini.

Skill mặc định hỏi user "có ảnh nguyên bản không?" khi anchor có purpose chứa "quy trình/sản phẩm/kết quả/case study/khách hàng".

## Quy tắc license + watermark

- KHÔNG dùng ảnh có watermark (Shutterstock thumbnail, Getty preview, etc.).
- KHÔNG dùng ảnh từ trang không công bố license (Pinterest, Tumblr, blog cá nhân không nói rõ).
- License chấp nhận: Unsplash, Pexels (free, no attribution required nhưng vẫn ghi credit nếu space cho phép), Pixabay, Wikimedia Commons (CC0/CC-BY).
- Ảnh AI gen: an toàn về license nhưng cần verify không trùng style với brand khác (vd không tạo ảnh nhái Studio Ghibli khi không có quyền).
- Ảnh có người: ưu tiên ảnh có model release rõ hoặc ảnh stylized không nhận diện được mặt.
