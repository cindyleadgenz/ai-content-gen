# Card Templates

One base template with color/font variables — Claude fills in your brand details automatically.

## How it works

During `/ai-content-gen-v1` setup, you provide:
- Primary color (hex)
- Secondary color (hex)  
- Font name (Google Fonts)
- Handle

Claude loads the template and replaces the placeholders before building your slides:

| Placeholder | Replaced with |
|---|---|
| `{{PRIMARY_COLOR}}` | Your brand primary color |
| `{{SECONDARY_COLOR}}` | Your brand secondary color |
| `{{FONT}}` | Your chosen font |
| `{{HANDLE}}` | Your social media handle |

## Slide types

| File | Use for |
|---|---|
| `base/cover.html` | First slide — title + hook |
| `base/content-text.html` | Text content slides with bullet points |
| `base/cta.html` | Last slide — call to action |

## Bring your own template

Set `card_template_path` in your config to use a custom template.
Just use the same `{{PLACEHOLDER}}` syntax and Claude will apply your brand config.
