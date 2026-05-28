---
name: ai-content-gen-v2
version: 1.0.0
description: |
  Universal AI social media content pipeline — V2 HTML Photo Cards.
  Same workflow as V1 but slides use YOUR photo as background with a dark overlay.
  Gives a personal, brand-authentic feel. Requires a photo library on your computer.
user-invocable: true
last-updated: 2026-05-28
tags: [content, carousel, infographic, social-media, html-cards, photo-background]
---

# AI Content Gen — V2: HTML Cards with Photo Background

**Visual style:** Your real photo as background + dark gradient overlay + text on top.
**Best for:** Personal brands, coaches, speakers — anyone with their own photos.

Trigger: copy this file to `~/.claude/skills/ai-content-gen-v2/SKILL.md` and run `/ai-content-gen-v2`

---

## ⚙️ Phase Zero: First-Run Setup

Check: `~/.claude/skills/ai-content-gen-v2/config.json`

If missing → run onboarding (ask one by one):

```
1.  Brand name → config: brand_name
2.  Brand bio (2–3 sentences) → config: brand_bio
3.  Target audience → config: target_audience
4.  Content pillars (3–6 categories) → config: content_pillars
5.  Brand voice/tone → config: brand_voice
6.  Language(s): single or bilingual? → config: languages
7.  Platforms (IG/FB/LinkedIn/XHS...) → config: platforms
8.  Social media handle (e.g. @yourbrand) → config: handle
9.  Brand primary color (hex, e.g. #ff90d1) → config: brand_color_primary
10. Brand secondary color (hex, e.g. #b3a0ff) → config: brand_color_secondary
11. Preferred font (Google Fonts name, e.g. "Inter", "Playfair Display", "Montserrat")
    → config: font_primary

12. Card overlay style — how should your photo show through?
    (A) Cinematic Dark — heavy dark overlay, text at bottom, moody & dramatic
    (B) Side Panel — left half is a dark text panel, right half shows your photo
    (C) Minimal — light overlay, photo-forward, text centered with soft blur behind
    → config: overlay_style ("cinematic-dark" / "side-panel" / "minimal")

13. Photo library path (folder with your photos, e.g. ~/Photos/brand/) → config: photo_library_path
14. Google Sheets Calendar
    - Spreadsheet ID → config: calendar.spreadsheet_id
    - Sheet name → config: calendar.sheet_name
    - Title column, Status column, Type column → config: calendar.*_col
15. Google Drive parent folder ID → config: drive_parent_folder_id
16. Local output directory → config: local_output_dir
17. rclone remote name → config: rclone_remote
18. Notification preference (WhatsApp / Email / Skip) → config: notification_method + notification_target
```

Save to `~/.claude/skills/ai-content-gen-v2/config.json`:

```json
{
  "brand_name": "...",
  "brand_bio": "...",
  "target_audience": "...",
  "content_pillars": ["...", "..."],
  "brand_voice": "...",
  "languages": ["..."],
  "platforms": ["IG", "FB"],
  "handle": "@yourbrand",
  "brand_color_primary": "#ff90d1",
  "brand_color_secondary": "#b3a0ff",
  "font_primary": "Inter",
  "overlay_style": "cinematic-dark",
  "photo_library_path": "~/Photos/brand/",
  "calendar": {
    "spreadsheet_id": "...",
    "sheet_name": "...",
    "title_col": "E",
    "status_col": "B",
    "type_col": "F"
  },
  "drive_parent_folder_id": "...",
  "local_output_dir": "~/Downloads/my-content/output/",
  "rclone_remote": "gdrive:",
  "notification_method": "whatsapp",
  "notification_target": "+60XXXXXXXXX"
}
```

And proceed.

---

## 📋 Phase 1: Trigger & Idea Approval

Same as V1 — scan Calendar for Approved rows, or generate new ideas if none found.

---

## 📝 Phase 2: Script & Caption

Same as V1 — research → script (adapted to config `brand_voice`) → post captions per platform.

---

## 📁 Phase 3: File Management

Same as V1 — create Drive folder (XX carousel/infographic - title), Script Doc, Caption Doc.

---

## 🎨 Phase 4: Visual Generation (HTML Photo Cards)

**Photo selection first:**

**Step 10 — Choose background photo**
- Scan config `photo_library_path` using `find` to list all images
- Based on the content topic, suggest 2–3 options and ask user to confirm
- Copy chosen photo to output folder as `bg.jpg`

**For each language version:**

**Step 11 — Create output folder**
- Path: `[config.local_output_dir]/XX-[type]-[slug]/`

**Step 12 — Build `slides-[lang].html`**

HTML structure per slide:
```html
<div class="card">
  <div class="photo" style="background-image: url('bg.jpg')"></div>
  <div class="overlay"></div>
  <div class="content">
    <div class="pill">[top label]</div>
    <h1 class="title">[headline — key words wrapped in <span class="accent">]</span></h1>
    <ul class="bullets">
      <li>[bullet 1]</li>
      <li>[bullet 2]</li>
    </ul>
    <div class="footer">
      <span class="takeaway">[takeaway sentence]</span>
      <span class="handle">[config.handle]</span>
    </div>
  </div>
</div>
```

CSS — apply based on config `overlay_style`:

**A) cinematic-dark** — heavy overlay, text anchored to bottom:
```css
.card { width:1080px; height:1350px; position:relative; overflow:hidden; font-family:'[font_primary]',sans-serif; }
.photo { position:absolute; inset:0; background-size:cover; background-position:center; }
.overlay { position:absolute; inset:0; background:linear-gradient(to bottom, rgba(0,0,0,0.3) 0%, rgba(0,0,0,0.85) 60%, rgba(0,0,0,0.95) 100%); }
.content { position:absolute; inset:0; padding:80px 90px; display:flex; flex-direction:column; justify-content:flex-end; }
```

**B) side-panel** — left dark panel, right photo visible:
```css
.card { width:1080px; height:1350px; position:relative; overflow:hidden; font-family:'[font_primary]',sans-serif; }
.photo { position:absolute; inset:0; background-size:cover; background-position:right center; }
.overlay { position:absolute; inset:0; background:linear-gradient(to right, rgba(0,0,0,0.92) 0%, rgba(0,0,0,0.85) 50%, rgba(0,0,0,0.1) 100%); }
.content { position:absolute; inset:0; padding:80px 90px; display:flex; flex-direction:column; justify-content:center; max-width:620px; }
```

**C) minimal** — light overlay, text centered, soft backdrop:
```css
.card { width:1080px; height:1350px; position:relative; overflow:hidden; font-family:'[font_primary]',sans-serif; }
.photo { position:absolute; inset:0; background-size:cover; background-position:center; filter:brightness(0.6); }
.overlay { position:absolute; inset:0; background:rgba(0,0,0,0.35); }
.content { position:absolute; inset:0; padding:80px 90px; display:flex; flex-direction:column; justify-content:center; align-items:center; text-align:center; }
.text-bg { background:rgba(0,0,0,0.45); backdrop-filter:blur(6px); border-radius:16px; padding:48px; }
```

Common styles across all three (add to the style block):
```css
.pill { background:color-mix(in srgb,[brand_color_secondary] 15%,transparent); border:1px solid color-mix(in srgb,[brand_color_secondary] 40%,transparent); color:[brand_color_secondary]; border-radius:6px; padding:8px 20px; display:inline-block; margin-bottom:32px; font-size:30px; font-weight:600; }
.title { font-size:88px; font-weight:900; color:#fff; line-height:1.3; margin-bottom:32px; }
.title .accent { color:[brand_color_primary]; }
.bullets { list-style:none; display:flex; flex-direction:column; gap:24px; }
.bullets li { font-size:40px; color:rgba(255,255,255,0.88); padding-left:52px; position:relative; line-height:1.5; }
.bullets li::before { content:"→"; color:[brand_color_primary]; position:absolute; left:0; font-weight:700; }
.handle { position:absolute; bottom:72px; right:90px; font-size:30px; font-weight:600; color:rgba(255,255,255,0.3); }
```

Fill slides from script, save as `output/XX-slug/slides-[lang].html`

**Step 13 — Split into individual slide files** (prevents lazy-load issues with bg.jpg)
```python
import re
with open('slides-[lang].html', 'r') as f: content = f.read()
head = content[:content.index('<body>') + len('<body>')]
cards = re.findall(r'<div class="card">.*?</div>', content, re.DOTALL)
for i, card in enumerate(cards):
    with open(f'single-[lang]-{str(i+1).zfill(2)}.html', 'w') as f:
        f.write(head + '\n' + card + '\n</body>\n</html>')
```

**Step 14 — Playwright screenshot + upscale**

> ⚠️ Must wait for bg.jpg to load — use `waitForTimeout(1200)` per slide.

```javascript
// Check your machine's DPR first: DPR 0.5 = viewport 540×675, DPR 1 = 1080×1350
await page.setViewportSize({ width: 540, height: 675 }); // adjust for your DPR
for (let i = 1; i <= N; i++) {
  const num = String(i).padStart(2, '0');
  await page.goto(`http://localhost:8787/output/XX-slug/single-[lang]-${num}.html`);
  await page.waitForTimeout(1200); // wait for photo to load
  await page.screenshot({ path: `slide-[lang]-${num}-small.png` });
}
```

```python
from PIL import Image
for i in range(1, N+1):
    num = str(i).zfill(2)
    img = Image.open(f'slide-[lang]-{num}-small.png')
    final = img.resize((1080, 1350), Image.LANCZOS)
    final.save(f'slide-[lang]-{num}.png', 'PNG')
```

Validate each slide: file size > 300KB, not pure black.

---

## ☁️ Phase 5: Upload & Wrap Up

Same as V1 — rclone upload per language → verify → update Calendar → optional local cleanup.

---

## ✅ Completion Checklist

- [ ] Script Doc created (all language versions)
- [ ] Caption Doc created (all platforms + hashtags)
- [ ] Drive main folder has correct slide count (primary language)
- [ ] Drive secondary folder has correct slide count (if bilingual)
- [ ] Calendar row updated: Status = Done, Drive link filled

---

## Key Rules

- Photo must load before screenshot — always `waitForTimeout(1200)` minimum
- Validate each slide visually (check it's not black/blank) before moving to next
- Brand colors come from config — never hardcode
- CTA on last slide must match caption CTA word-for-word
