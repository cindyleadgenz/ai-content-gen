# Card Templates

Two ready-to-use HTML card themes for your social media visuals.

| Theme | Style | Best for |
|---|---|---|
| `blue-dark/` | Dark background, teal/green accents | Tech, business, professional |
| `orange-light/` | Light background, warm orange accents | Lifestyle, personal brand, warm tone |

## Slide types (per theme)

| File | Use for |
|---|---|
| `cover.html` | First slide — title + hook |
| `content-text.html` | Text-only content slides |
| `content-image.html` | Content slides with image/graphic |
| `cta.html` | Last slide — call to action |

## Customize

Before using, replace these placeholders in each HTML file:

| Placeholder | Replace with |
|---|---|
| `@yourbrand` | Your social media handle |
| Title/subtitle text | Your actual content |

Or just let Claude fill them in during `/ai-content-gen` — it handles this automatically.

## Bring your own template

Set your own template path in config:

```json
{
  "card_template_path": "/path/to/your/templates/"
}
```
