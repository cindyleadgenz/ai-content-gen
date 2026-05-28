---
name: ai-content-gen
version: 1.0.0
description: |
  Universal AI social media content pipeline — idea → script → visual cards → Drive upload → Calendar update.
  First run: onboarding wizard to collect your brand config. Subsequent runs: fully automated from your Content Calendar.
  Supports carousel & infographic, single or bilingual output, HTML card visuals.
user-invocable: true
last-updated: 2026-05-28
author: Cindy (based on V4A/V4B workflows)
tags:
  - content
  - carousel
  - infographic
  - social-media
  - copywriting
  - automation
---

# AI Content Generation — Universal Pipeline

A plug-and-play content automation system. Works for any brand, any niche, any language.

Trigger: `/ai-content-gen` or say "make content", "create carousel", "make infographic"

---

## ⚙️ Phase Zero: First-Run Setup (onboarding)

**Check if config exists:**
```
~/.claude/skills/ai-content-gen/config.json
```

**If config does NOT exist → run onboarding wizard:**

Ask the user these questions one by one (wait for answer before next):

```
1. What's your brand name?
   → config: brand_name

2. Describe your brand in 2–3 sentences.
   (Who are you? Who do you serve? What value do you provide?)
   → config: brand_bio

3. Who is your target audience?
   (e.g. "Female entrepreneurs 25–35, side hustle, Southeast Asia")
   → config: target_audience

4. What are your content pillars/categories?
   (List 3–6, e.g. "Marketing Tips, Mindset, Behind the Scenes, Product Education")
   → config: content_pillars (array)

5. What tone/voice does your brand use?
   (e.g. "Friendly and professional", "Bold and motivational", "Casual Gen Z")
   → config: brand_voice

6. What language(s) do you post in?
   Options: (A) Single language  (B) Bilingual (two versions)
   If B → what are the two languages?
   → config: languages (array, e.g. ["Chinese", "English"] or ["English"])

7. Which platforms do you post on?
   (Check all that apply: IG / FB / LinkedIn / XHS / TikTok / Twitter)
   → config: platforms (array)

8. Google Sheets Content Calendar
   - Spreadsheet ID (from the URL: docs.google.com/spreadsheets/d/[THIS_PART]/edit)
   - Sheet tab name (e.g. "Content Plan", "01 Content Idea")
   - Which column has the content title/topic?
   - Which column has the approval status?
   - Which column has the content type (carousel/infographic)?
   → config: calendar { spreadsheet_id, sheet_name, title_col, status_col, type_col }

9. Google Drive parent folder ID
   (The folder where all your content subfolders will be created)
   (From the URL: drive.google.com/drive/folders/[THIS_PART])
   → config: drive_parent_folder_id

10. Local output directory for generated files
    (Where to save HTML cards and screenshots)
    (e.g. ~/Downloads/my-content/output/)
    → config: local_output_dir

11. rclone remote name
    (Run `rclone listremotes` to see yours, e.g. "gdrive:", "mycloud:")
    → config: rclone_remote

12. HTML card template path
    (Leave blank to use the default cindy-skills template, or provide your own path)
    → config: card_template_path (optional)

13. Notification preference for new ideas
    Options: (A) WhatsApp  (B) Email  (C) Skip / I'll check manually
    → config: notification_method
    If A → WhatsApp number (with country code)
    If B → email address
    → config: notification_target
```

**Save all answers to `~/.claude/skills/ai-content-gen/config.json`:**

```json
{
  "brand_name": "...",
  "brand_bio": "...",
  "target_audience": "...",
  "content_pillars": ["...", "..."],
  "brand_voice": "...",
  "languages": ["..."],
  "platforms": ["IG", "FB"],
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
  "card_template_path": "",
  "notification_method": "whatsapp",
  "notification_target": "+60XXXXXXXXX"
}
```

Confirm with the user: "✅ Config saved! You won't need to set this up again. Starting your first content run now..."

**If config already exists → skip onboarding, go straight to Phase 1.**

> To reset config: delete `~/.claude/skills/ai-content-gen/config.json` and run `/ai-content-gen` again.

---

## 📋 Phase 1: Trigger & Idea Approval

**Step 1 — Read Content Calendar**

Use Google Sheets MCP/API to read the calendar (using config: `calendar.spreadsheet_id`, `calendar.sheet_name`).

Look for rows where `status_col = "Approved"` AND content is pending production.
- If multiple rows match → ask user which one to do first
- If no rows match → ask: "No approved content found. Want me to generate some ideas now?"

**If generating ideas:**
- Generate 3–5 content ideas based on config: `brand_bio`, `target_audience`, `content_pillars`
- Each idea: title + type (carousel/infographic) + which content pillar it belongs to
- Present the ideas, ask user to pick one or suggest their own
- Write the chosen idea into the Calendar

**Record for this session:** `title`, `type` (carousel or infographic), `content_pillar`

---

## 📝 Phase 2: Script & Caption

**Step 2 — Research reference content**

Use Firecrawl to search for similar content:
- Search: `[title/topic] [year] social media [platform] content ideas`
- Method: **Copy → Adapt → Elevate** (never copy directly)
- Extract 3–5 key insight points
- **Relevance check:** Confirm the topic is current (tools, examples, data are recent)

**Step 3 — Read brand context**

Load config: `brand_bio`, `target_audience`, `brand_voice`, `content_pillars`

**Step 4 — Generate Slide script**

For each language in config `languages`:

Script rules:
- Adapt tone to config `brand_voice` (not Cindy's specific voice)
- Each Slide = separate paragraph: headline + body copy + design notes
- Cover slide: hook headline + CTA preview
- Last slide: clear CTA (comment / DM / follow)
- carousel: 5–9 slides | infographic: 1 slide

**Step 5 — Generate post caption**

For each platform in config `platforms`:

```
[Hook] — stop-scroll first line (question / number / counter-intuitive / pain point)

[Body] — 3–5 short paragraphs
- Mirror carousel content but don't repeat slide-by-slide
- Logical flow: problem → cause → solution

[CTA] — one clear action
- "Comment X to get Y" / "DM me" / "Follow for more"

[Hashtags]
- IG: 20–30 (mix popular + niche + brand)
- LinkedIn: 5–10 (professional, niche)
- XHS: 5–10 (Chinese preferred)
- FB: 3–5
```

> ⚠️ CTA consistency: caption CTA keyword must match the last slide CTA exactly. Confirm before writing caption.

---

## 📁 Phase 3: File Management

**Step 6 — Create Drive folder**

Using config `drive_parent_folder_id`:
- Scan existing subfolders for the highest sequence number (XX)
- Create folder: `XX carousel - [title]` or `XX infographic - [title]`
- Record the returned folder ID

**Step 7 — If bilingual output:**
- Create subfolder `[Second Language] Version` inside the main folder
- Record that folder ID

**Step 8 — Create Script Doc**
- Name: `XX Script - [title]`
- Place inside main folder (Step 6)
- Write all language versions, separated by headings

**Step 9 — Create Caption Doc**
- Name: `XX Caption - [title]`
- Place inside main folder
- Write all platform captions + hashtags

---

## 🎨 Phase 4: Visual Card Generation (HTML + Playwright)

**Determine card template:**
- If config `card_template_path` is set → use that template
- Otherwise → use the default template from cindy-skills: `skills/cards/assets/`

**For each language version:**

**Step 10 — Create output folder**
- Path: `[config.local_output_dir]/XX-[type]-[slug]/`

**Step 11 — Build `slides-[lang].html`**
- Copy the card template
- Fill in slide content from script (one `.card` div per slide)
- Save as `slides-[lang].html` in output folder
- Preview via local HTTP server to confirm layout

**Step 12 — Split into individual slide files** (avoids lazy rendering)
```python
import re
with open('slides-[lang].html', 'r') as f: content = f.read()
head = content[:content.index('<body>') + len('<body>')]
cards = re.findall(r'<div class="card">.*?</div>', content, re.DOTALL)
for i, card in enumerate(cards):
    with open(f'single-[lang]-{str(i+1).zfill(2)}.html', 'w') as f:
        f.write(head + '\n' + card + '\n</body>\n</html>')
```

**Step 13 — Playwright screenshot + upscale**

```javascript
// DPR note: check your machine's DPR first
// CSS target: 1080×1350 → physical pixels depend on DPR
// DPR 0.5 (common on Mac): CSS 1080×1350 → physical 540×675
// DPR 1.0: CSS 1080×1350 → physical 1080×1350
// DPR 2.0 (Retina): CSS 1080×1350 → physical 2160×2700

await page.setViewportSize({ width: 540, height: 675 }); // adjust for your DPR
for (let i = 1; i <= N; i++) {
  const num = String(i).padStart(2,'0');
  await page.goto(`http://localhost:8787/output/XX-slug/single-[lang]-${num}.html`);
  await page.waitForTimeout(1000);
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

---

## ☁️ Phase 5: Upload & Wrap Up

**Step 14 — Upload to Drive**

For each language:
```bash
rclone copy [config.local_output_dir]/XX-slug/ [config.rclone_remote] \
  --drive-root-folder-id <folder_id_for_this_language> \
  --include "slide-[lang]-*.png" \
  --progress
```

> ⚠️ Known pitfalls (do NOT try these):
> - Drive REST API without proper scope → 403 error
> - Playwright `file_upload` → fake upload, files don't actually go to Drive
> - Chrome MCP for Drive domain → blocked

**Step 15 — Verify upload**

Use Drive MCP `search_files` to confirm correct number of files in each folder.

**Step 16 — Update Content Calendar**

Write back to the row using Google Sheets API:
- Status column → `Done`
- Drive link column → main folder URL
- Any other columns your calendar uses

**Step 17 — Clean up local files** (optional)

Only after confirming Drive upload is complete:
```bash
# Move to Trash (recoverable) instead of permanent delete
trash "[config.local_output_dir]/XX-slug/"
# or on Mac:
osascript -e 'tell application "Finder" to delete POSIX file "/path/to/output/XX-slug"'
```

---

## ✅ Completion Checklist

Before calling this done, confirm:
- [ ] Script Doc created with all language versions
- [ ] Caption Doc created with all platform captions + hashtags
- [ ] Drive main folder has `slide-[lang1]-01` to `slide-[lang1]-XX` (correct count)
- [ ] Drive secondary folder (if bilingual) has `slide-[lang2]-01` to `slide-[lang2]-XX`
- [ ] Calendar row updated: Status = Done, Drive link filled

---

## Drive Folder Structure (per content piece)

```
📁 XX carousel - [title]              ← Step 6 (main folder)
├── 📄 XX Script - [title]            ← Step 8 (all language scripts)
├── 📄 XX Caption - [title]           ← Step 9 (all platform captions)
├── 🖼 slide-[lang1]-01.png           ← Primary language images
├── 🖼 slide-[lang1]-02.png
├── ...
└── 📁 [Second Language] Version      ← Step 7 (if bilingual)
    ├── 🖼 slide-[lang2]-01.png
    └── ...
```

---

## Key Rules

- `infographic` = 1 slide, `carousel` = 5–9 slides
- Sequence number XX = largest existing number in Drive parent folder + 1
- Script ≠ Caption: script = text ON the slides, caption = text posted with the image
- CTA keyword must be identical between last slide and caption
- Always verify Drive upload before cleaning local files
- Config lives at `~/.claude/skills/ai-content-gen/config.json` — edit it anytime to update your setup

