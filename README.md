# ai-content-gen

A universal AI social media content pipeline skill for **Claude Code**.

Works for **any brand, any niche, any language** — not tied to any specific account or business.

---

## What it does

Automates your full content creation workflow:

**Idea → Script → Visual Cards (HTML) → Google Drive → Content Calendar**

Supports:
- Carousel & infographic formats
- Single language or bilingual output
- Any brand voice / tone
- IG, FB, LinkedIn, XHS, TikTok, and more

---

## Install

Copy `SKILL.md` into your Claude Code skills folder:

```bash
mkdir -p ~/.claude/skills/ai-content-gen
curl -o ~/.claude/skills/ai-content-gen/SKILL.md \
  https://raw.githubusercontent.com/cindyleadgenz/ai-content-gen/main/SKILL.md
```

---

## First Run

Open Claude Code and type:

```
/ai-content-gen
```

Claude will walk you through a one-time setup (takes ~3 minutes):
- Your brand name, bio, tone of voice
- Target audience & content pillars
- Language preferences (single or bilingual)
- Platforms (IG, FB, LinkedIn, XHS...)
- Google Sheets Content Calendar details
- Google Drive parent folder
- Local output directory & rclone setup

Config is saved to `~/.claude/skills/ai-content-gen/config.json` — you only set up once.

---

## What you need

| Tool | Purpose |
|---|---|
| [Claude Code](https://claude.ai/code) | Runs the skill |
| Google Sheets MCP | Read/write Content Calendar |
| Google Drive MCP | Create folders & docs |
| [rclone](https://rclone.org/) | Upload images to Google Drive |
| [Playwright MCP](https://github.com/microsoft/playwright-mcp) | Screenshot HTML cards |
| Python + Pillow | Crop & resize images |

---

## After Setup

Every time you run `/ai-content-gen`:

1. Scans your Content Calendar for approved ideas
2. Researches reference content (Firecrawl)
3. Writes slide scripts in your brand voice
4. Generates post captions for each platform
5. Creates Drive folder structure & Docs
6. Builds HTML visual cards
7. Screenshots + resizes to 1080×1350
8. Uploads to Google Drive via rclone
9. Updates Calendar row to "Done"

---

## Reset Config

Delete the config file and run again:

```bash
rm ~/.claude/skills/ai-content-gen/config.json
```

---

## Based on

Extracted from [Cindy's](https://github.com/cindyleadgenz/cindy-skills) personal V4A/V4B content automation workflows — generalized so anyone can use it.

---

*Made by [@cindyleadgenz](https://github.com/cindyleadgenz)*
