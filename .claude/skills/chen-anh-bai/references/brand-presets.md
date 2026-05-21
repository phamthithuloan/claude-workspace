# Brand Presets

Preset brand guideline để Step 1 load khi user chọn. KHÔNG hỏi tay lại 4 item brand mỗi lần.

## Contents

- Preset: Hồng Ngọc
- Preset: Seongon
- Template: Custom

---

## Preset — Hồng Ngọc (Trung tâm Mắt Hồng Ngọc)

Trigger: user chọn "Hồng Ngọc" ở Q1 Step 1, hoặc message có chứa "Hồng Ngọc" / "mắt Hồng Ngọc" / "Hong Ngoc".

```yaml
brand_name: Hồng Ngọc
primary_color: "#138C3D"
secondary_color: "#D32F2F"
neutral_dark: "#1F2937"
neutral_light: "#F9FAFB"
font_headline: Be Vietnam Pro
font_fallback: [Inter, Manrope, Plus Jakarta Sans]
logo_path: /Users/phamthuloan/.claude/image-cache/a9cb0fb2-8c05-4927-b1e9-84889fa947a9/10.png
logo_versions_available:
  - primary  # vương miện xanh + chữ thập đỏ + "HONG NGOC"
logo_versions_missing:
  - reversed  # trắng cho dark bg
  - mark-only  # chỉ icon
  - wordmark-only
tone:
  - corporate-leaning
  - minimal-modern
  - serious + warm
  - reference: Mayo Clinic, Cleveland Clinic, Vinmec.vn
target_customer:
  - giới tính: nữ chiếm 60%, nam 40%
  - độ tuổi: 25-55 (chính), 55+ (phụ huynh phụ huynh cao tuổi)
  - ngành: bệnh nhân + người chăm sóc
banned:
  - stock photo cliché (handshake, lightbulb)
  - gradient sặc sỡ
  - emoji trang trí
  - religious symbols
  - ảnh phẫu thuật quá graphic
canva_brand_kit_id: null  # CHƯA tạo brand kit dedicated trong Canva — user phải upload logo manually mỗi design
```

**Gen prompt template cho Canva/Gemini**:

```
[image_purpose], primary color #138C3D, accent #D32F2F, Be Vietnam Pro typography, 
professional medical clinic aesthetic, minimal-modern, serious + warm tone, 
reference style Mayo Clinic, KHÔNG stock photo cliché, KHÔNG gradient sặc sỡ
```

## Preset — Seongon (Agency Content)

Trigger: user chọn "Seongon" ở Q1, hoặc message có chứa "Seongon" / "agency content".

```yaml
brand_name: Seongon
primary_color: "[FILL IN]"
secondary_color: "[FILL IN]"
neutral_dark: "[FILL IN]"
neutral_light: "[FILL IN]"
font_headline: "[FILL IN]"
logo_path: "[FILL IN]"
tone:
  - "[FILL IN — modern/playful/corporate?]"
target_customer:
  - "[FILL IN — agency client = B2B marketing manager / SMB owner?]"
canva_brand_kit_id: "[FILL IN nếu có]"
```

**Status**: brand guideline Seongon CHƯA điền. Khi user chọn preset này lần đầu → skill hỏi user fill 4 item:

1. Primary + secondary color HEX
2. Font headline
3. Tệp khách hàng (giới tính, độ tuổi, ngành)
4. Logo path/URL nếu có

Sau khi fill, skill UPDATE file này cho lần sau dùng.

**Gen prompt template** (chỉ generate sau khi fill):

```
[image_purpose], primary color [primary_color], accent [secondary_color], 
[font_headline] typography, [tone] aesthetic
```

## Preset — Elite Dental Group

Trigger: user chọn "Elite Dental" ở Q1, hoặc message có chứa "Elite Dental" / "Elite Dental Group".

```yaml
brand_name: Elite Dental Group
tagline: "Global quality, local hospitality"
primary_color: "#C2185B"  # magenta/pink — chữ ELITE DENTAL + icon + tagline
secondary_color: "#6B6B6B"  # grey — chữ GROUP + icon nhỏ
neutral_dark: "#1A1A1A"
neutral_light: "#FFFFFF"
font_headline: Montserrat Bold  # sans-serif bold uppercase
font_tagline: Allura  # serif italic script
logo_description: |
  Icon hai hình người (1 magenta, 1 grey) tạo dáng cánh bướm + chữ "ELITE DENTAL" magenta
  + "GROUP" grey + tagline "Global quality, local hospitality" magenta italic
tone:
  - premium international
  - professional medical (chuẩn Mỹ + AACI cert)
  - clean modern + warm hospitality
  - reference: practice positioning của chuỗi nha khoa premium quốc tế
target_customer:
  - default: nam + nữ 25-45, thu nhập trung bình (bao gồm cả khách implant young-adult)
  - alternate: nam + nữ 35-60 thu nhập cao (premium implant + full-arch)
key_credentials:
  - "AACI 95,33/100"
  - "7 SOP vô khuẩn theo chuẩn AACI"
  - "PGS.TS.BS Trần Hùng Lâm - Chủ tịch ITI Vietnam"
  - "Cone Beam CT, Navigation DCarer, PIC System, Trios 3D"
banned:
  - stock photo cliché (handshake, lightbulb, generic teeth close-up)
  - gradient sặc sỡ
  - emoji trang trí
  - ảnh phẫu thuật quá graphic (máu, dụng cụ rõ)
canva_brand_kit_id: null  # CHƯA tạo brand kit Canva — upload logo manually
locations:
  - 51A Tú Xương, Phường Võ Thị Sáu, Quận 3, TP.HCM
  - 57 Huỳnh Tịnh Của, Phường Võ Thị Sáu, Quận 3, TP.HCM
  - Galleria An Khánh, Thủ Thiêm, TP.HCM
hotline: "(+84) 28 7306 3838"
```

**Gen prompt template cho Canva/Gemini**:

```
[image_purpose], primary color #C2185B (magenta/pink) for headlines + accents, 
secondary #6B6B6B (grey) for body, Montserrat Bold typography, 
premium international medical clinic aesthetic, clean modern + warm hospitality, 
target audience Vietnamese adults 25-45, 
NO stock photo cliché, NO graphic medical imagery
```

## Template — Custom (brand mới)

Trigger: user chọn "Custom" ở Q1, hoặc brand mention không khớp Hồng Ngọc/Seongon.

Skill hỏi 1 message gộp 4 câu qua AskUserQuestion:

1. Brand name
2. Primary color HEX (vd #138C3D)
3. Accent/secondary color HEX
4. Tệp khách hàng (1-2 câu mô tả)

Optional (skip OK):
- Font headline
- Logo URL/path

Skill dùng ngay cho session hiện tại. KHÔNG save thành preset persistent — nếu user muốn save → hỏi cuối session.

**Gen prompt template**:

```
[image_purpose], primary color [primary_hex], accent [secondary_hex], 
target customer: [target_desc], clean modern aesthetic
```

## Quy tắc dùng preset

- Skill load preset ở Step 1, KHÔNG hardcode brand value vào pipeline.
- Hồng Ngọc preset đầy đủ → auto-proceed Step 2 ngay sau khi user chọn.
- Seongon preset thiếu → hỏi 4 item + update file này.
- Custom preset → session-scoped, không persist.
- Khi update preset Seongon: rewrite section "Preset — Seongon" trong file này, giữ format YAML giống Hồng Ngọc.
