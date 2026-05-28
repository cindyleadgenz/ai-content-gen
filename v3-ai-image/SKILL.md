---
name: ai-content-gen-v3
version: 1.0.0
description: |
  Universal AI social media content pipeline — V3 AI Image Generation.
  Uses ChatGPT (via Playwright) to generate each slide as an AI image with your photo
  as background and your brand colors/typography. AI-generated visuals, not HTML templates.
user-invocable: true
last-updated: 2026-05-28
tags: [content, carousel, infographic, social-media, chatgpt, ai-image]
---

# AI Content Gen — V3: AI Image Generation (ChatGPT + Playwright)

**Visual style:** ChatGPT generates each slide as a real AI image — your photo as background, brand colors, styled typography.
**Best for:** Users who want AI-generated visuals rather than HTML card templates.

> ⚠️ **Known limitation:** ChatGPT image generation can struggle with non-Latin scripts (Chinese, Arabic, etc.). If you post in a non-English language, use V1 or V2 instead for reliable text rendering.

Trigger: copy this file to `~/.claude/skills/ai-content-gen-v3/SKILL.md` and run `/ai-content-gen-v3`

---

## ⚙️ Phase Zero: First-Run Setup

Check: `~/.claude/skills/ai-content-gen-v3/config.json`

If missing → run onboarding (ask one by one):

```
1.  Brand name → config: brand_name
2.  Brand bio (2–3 sentences) → config: brand_bio
3.  Target audience → config: target_audience
4.  Content pillars (3–6 categories) → config: content_pillars
5.  Brand voice/tone → config: brand_voice
6.  Language(s): single or bilingual? → config: languages
    ⚠️ If non-English (Chinese, Arabic, etc.) → warn: ChatGPT may render text incorrectly. Suggest V1 or V2.
7.  Platforms (IG/FB/LinkedIn/XHS...) → config: platforms
8.  Social media handle → config: handle
9.  Brand primary color (hex) → config: brand_color_primary
10. Brand secondary color (hex) → config: brand_color_secondary
11. Primary font preference (e.g. "Playfair Display", "Inter", "Montserrat") → config: font_primary
12. Photo library path (folder with your brand photos) → config: photo_library_path
13. ChatGPT account email → config: chatgpt_email
    (Playwright will open chat.openai.com — you need to be logged in or provide cookies)
14. Google Sheets Calendar
    - Spreadsheet ID, Sheet name, column mapping → config: calendar.*
15. Google Drive parent folder ID → config: drive_parent_folder_id
16. Local output directory → config: local_output_dir
17. rclone remote name → config: rclone_remote
18. Notification preference → config: notification_method + notification_target
```

Save to `~/.claude/skills/ai-content-gen-v3/config.json` and proceed.

---

## 📋 Phase 1: Trigger & Idea Approval

Same as V1 — scan Calendar, or generate ideas if none found.

---

## 📝 Phase 2: Script & Caption

Same as V1 — research → script → captions. Script should include per-slide:
- Headline (note which words should be highlighted in brand color)
- Bullet points
- Top label/pill text
- Bottom takeaway sentence

This detail feeds directly into the ChatGPT image prompt.

---

## 📁 Phase 3: File Management

Same as V1 — Drive folders, Script Doc, Caption Doc.

---

## 🎨 Phase 4: AI Image Generation (ChatGPT + Playwright)

**Step 10 — Choose background photo**
- Scan `config.photo_library_path` with `find`
- Pick one relevant to the content topic (confirm with user if unsure)
- This same photo is uploaded to ChatGPT for every slide in the set

**Step 11 — Create output folder**
- Path: `[config.local_output_dir]/XX-[type]-[slug]/`

**Step 12 — Open ChatGPT via Playwright**
```javascript
await page.goto('https://chat.openai.com');
// If not logged in, wait for user to log in manually, then continue
await page.waitForURL('**/c/**', { timeout: 60000 });
```

**Step 13 — Generate each slide (repeat for every slide)**

For each slide, upload the background photo + send this prompt:

```
Generate a vertical social media slide, 1080×1350px (4:5 ratio).

BACKGROUND:
- Use the uploaded photo as background (keep it, don't replace with a generated scene)
- Apply a dark overlay: from rgba(26,26,26,0.85) to rgba(45,45,45,0.92)
- Photo should feel cinematic/moody, text must be fully readable

BRAND COLORS (strictly follow):
- Primary accent: [config.brand_color_primary] — headlines, → bullet markers, divider lines
- Secondary accent: [config.brand_color_secondary] — top label pill
- Main text: #ffffff
- Body text: rgba(255,255,255,0.88)
- Muted text (handle): rgba(255,255,255,0.35)

TYPOGRAPHY:
- Headline: [config.font_primary], weight 900, ~88–108px
- Body: [config.font_primary], weight 400, ~44px
- Label/handle: Inter or DM Sans, weight 600

LAYOUT (90px horizontal padding, 80px vertical padding, left-aligned):
① Top pill label — secondary color text, low-opacity fill, rounded border
② Main headline — large bold white, key words in primary color
③ Bullet list — "→" marker in primary color, body text white 88%
④ Thin divider line — rgba(255,255,255,0.15)
⑤ Bottom row — left: takeaway (key words bold white); right: handle "[config.handle]" muted

CONTENT FOR THIS SLIDE:
Label: [top pill text from script]
Headline: [main headline — mark color-highlighted words with *asterisks*]
Bullets:
→ [bullet 1]
→ [bullet 2]
→ [bullet 3]
Takeaway: [bottom sentence — mark bold words with *asterisks*]
```

**Download the generated image:**
```python
# Use Playwright evaluate to fetch image with session cookies (direct requests = 403)
result = await page.evaluate("""
  async () => {
    const img = document.querySelector('img[src*="oaiusercontent"]');
    if (!img) return null;
    const resp = await fetch(img.src, { credentials: 'include' });
    const buf = await resp.arrayBuffer();
    return Array.from(new Uint8Array(buf));
  }
""")
if result:
    with open(f'slide-[lang]-{num}-raw.png', 'wb') as f:
        f.write(bytes(result))
```

**Step 14 — Resize to 1080×1350**
```python
from PIL import Image
img = Image.open(f'slide-[lang]-{num}-raw.png')
final = img.resize((1080, 1350), Image.LANCZOS)
final.save(f'slide-[lang]-{num}.png', 'PNG')
```

**⚠️ Text rendering check — after EVERY slide:**
- Read the saved image with the Read tool
- Check all text is legible and correct (no garbled characters)
- If 1 slide has issues: note it, continue
- If 2 consecutive slides have issues: STOP, notify user, wait for instruction
  - Option A: Retry with rephrased prompt
  - Option B: Switch to V1 or V2 for this content piece

**Step 15 — Repeat for all slides, then repeat for second language if bilingual**

---

## ☁️ Phase 5: Upload & Wrap Up

Same as V1 — rclone upload → verify → update Calendar → optional local cleanup.

---

## ✅ Completion Checklist

- [ ] Every slide visually verified (no garbled text)
- [ ] Script Doc + Caption Doc created
- [ ] Drive folders have correct slide count per language
- [ ] Calendar row updated

---

## Key Rules

- Never skip the per-slide visual check
- Stop after 2 consecutive rendering failures — don't push through
- For non-Latin scripts: seriously consider using V1 or V2 instead
- Always upload photo fresh per ChatGPT session (don't reuse old conversation)
- If ChatGPT session expires mid-run: re-login, re-upload photo, continue from last successful slide
