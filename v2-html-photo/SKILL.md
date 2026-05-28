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
11. Photo library path (folder with your photos, e.g. ~/Photos/brand/) → config: photo_library_path
12. Google Sheets Calendar
    - Spreadsheet ID → config: calendar.spreadsheet_id
    - Sheet name → config: calendar.sheet_name
    - Title column, Status column, Type column → config: calendar.*_col
13. Google Drive parent folder ID → config: drive_parent_folder_id
14. Local output directory → config: local_output_dir
15. rclone remote name → config: rclone_remote
16. Notification preference (WhatsApp / Email / Skip) → config: notification_method + notification_target
```

Save to `~/.claude/skills/ai-content-gen-v2/config.json` and proceed.

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

CSS (use config brand colors):
```css
.card { width: 1080px; height: 1350px; position: relative; overflow: hidden; }
.photo { position: absolute; inset: 0; background-size: cover; background-position: center; }
.overlay { position: absolute; inset: 0;
  background: linear-gradient(rgba(26,26,26,0.85), rgba(45,45,45,0.92)); }
.content { position: absolute; inset: 0; padding: 80px 90px;
  display: flex; flex-direction: column; justify-content: flex-end; }
.pill { background: rgba([brand_color_secondary RGB], 0.15);
  border: 1px solid rgba([brand_color_secondary RGB], 0.4);
  color: [config.brand_color_secondary]; border-radius: 6px; padding: 8px 20px; display: inline-block; margin-bottom: 32px; }
.title { font-size: 88px; font-weight: 900; color: #fff; line-height: 1.3; margin-bottom: 32px; }
.title .accent { color: [config.brand_color_primary]; }
.bullets li::before { content: "→"; color: [config.brand_color_primary]; margin-right: 16px; }
.handle { color: rgba(255,255,255,0.35); }
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
