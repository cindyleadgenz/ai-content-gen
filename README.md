# ai-content-gen

A universal AI social media content pipeline for **Claude Code**.

Works for **any brand, any niche, any language** — fully configurable on first run.

---

## 3 Versions — Pick What Fits Your Style

| | V1 — HTML Cards | V2 — HTML Photo Cards | V3 — AI Image |
|---|---|---|---|
| **Visual style** | Clean gradient backgrounds | Your photo + dark overlay | ChatGPT-generated images |
| **Needs photos?** | No | Yes (your own photos) | Yes (uploaded to ChatGPT) |
| **Needs ChatGPT?** | No | No | Yes |
| **Non-English text?** | ✅ Reliable | ✅ Reliable | ⚠️ May garble |
| **Best for** | Any brand, clean look | Personal brands, coaches | AI-image aesthetic |
| **Install trigger** | `/ai-content-gen-v1` | `/ai-content-gen-v2` | `/ai-content-gen-v3` |

> Can't decide? Start with **V1** — simplest setup, most reliable.

---

## Install

Pick the version(s) you want:

```bash
# V1 — HTML Cards (recommended starting point)
mkdir -p ~/.claude/skills/ai-content-gen-v1
curl -o ~/.claude/skills/ai-content-gen-v1/SKILL.md \
  https://raw.githubusercontent.com/cindyleadgenz/ai-content-gen/main/v1-html-cards/SKILL.md

# V2 — HTML Photo Cards
mkdir -p ~/.claude/skills/ai-content-gen-v2
curl -o ~/.claude/skills/ai-content-gen-v2/SKILL.md \
  https://raw.githubusercontent.com/cindyleadgenz/ai-content-gen/main/v2-html-photo/SKILL.md

# V3 — AI Image (ChatGPT)
mkdir -p ~/.claude/skills/ai-content-gen-v3
curl -o ~/.claude/skills/ai-content-gen-v3/SKILL.md \
  https://raw.githubusercontent.com/cindyleadgenz/ai-content-gen/main/v3-ai-image/SKILL.md
```

You can install all 3 — they're independent and won't conflict.

---

## First Run

Open Claude Code and type the version trigger:

```
/ai-content-gen-v1
```

Claude runs a one-time setup (~3 min) and saves your config. Every run after that is fully automated.

---

## What the Pipeline Does

```
Calendar scan → Idea approval
      ↓
Research + Script (adapted to your brand voice)
      ↓
Post captions (per platform)
      ↓
Drive folder + Docs
      ↓
Visual cards (V1/V2: HTML  |  V3: ChatGPT)
      ↓
Upload to Google Drive (rclone)
      ↓
Calendar updated ✅
```

---

## What You Need

| Tool | Purpose |
|---|---|
| [Claude Code](https://claude.ai/code) | Runs the skill |
| Google Sheets MCP | Read/write Content Calendar |
| Google Drive MCP | Create folders & Docs |
| [rclone](https://rclone.org/) | Upload images to Google Drive |
| [Playwright MCP](https://github.com/microsoft/playwright-mcp) | Screenshot HTML cards (V1/V2) or control ChatGPT (V3) |
| Python + Pillow | Resize images to 1080×1350 |

---

## Card Templates (V1)

Two built-in themes in `templates/`:

| Theme | Style |
|---|---|
| `blue-dark/` | Dark, teal accents — professional/tech |
| `orange-light/` | Light, warm accents — lifestyle/personal |

Or bring your own template and set `card_template_path` in config.

---

## Reset Config

```bash
rm ~/.claude/skills/ai-content-gen-v1/config.json  # reset V1
rm ~/.claude/skills/ai-content-gen-v2/config.json  # reset V2
rm ~/.claude/skills/ai-content-gen-v3/config.json  # reset V3
```

---

*Made by [@cindyleadgenz](https://github.com/cindyleadgenz)*
*Based on real production workflows powering LeadGenz & Cindy IP content automation.*
