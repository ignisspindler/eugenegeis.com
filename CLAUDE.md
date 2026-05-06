# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A personal link-tree / landing page for **Eugene J. Geis, PhD** at `eugenegeis.com`. Single-file static site (`index.html` + `hero.jpg`). No build step, no framework, no dependencies beyond Google Fonts loaded via CDN.

## Commands

```bash
# Preview in browser
open index.html

# Regenerate the hero image (uses GEMINI_API_KEY env var)
# Use the nano-banana-pro-preview model for image generation (generateContent endpoint with responseModalities: ["IMAGE", "TEXT"])
# Save the base64-decoded bytesBase64Encoded field from the response as hero.jpg
```

## Site Architecture

- **`index.html`** — Everything. Styles, markup, and JS are all inline in one file.
- **`hero.jpg`** — Lone figure holding frame against a cascading digital storm. Used on the Medium card.
- **`physics.jpg`** — Nuclear physics particle trajectories / quantum structure. Used on both Research cards.
- **`diplomacy.jpg`** — Diplomatic council chamber with warm amber light. Used on PhD2Pro and MUN-E cards.
- **`meditation.jpg`** — Meditating figure with luminous energy rings. Used on Educational Shaman and Holding Frame Podcast cards.

All images generated with Gemini `nano-banana-pro-preview`, 16:9, saved to disk (do not regenerate unless explicitly asked).

### Key Design Decisions

- **Typography**: Cormorant Garamond (headings, serif, academic feel) + Inter (body, sans-serif)
- **Palette**: Near-black bg (`#07090e`), amber accent (`#c8933c`), warm white text (`#ddd8cf`)
- **Hover popovers**: A single global `#popover` div is repositioned via JS on `mouseenter`. Popover content is stored in `data-popover-title`, `data-popover-body`, and optionally `data-popover-list` (JSON array) attributes on each `.card`.
- **Popovers only activate** on `(hover: hover) and (pointer: fine)` — touch devices get none.

### Adding a New Link Card

Copy an existing `.card` element, update the `href`, `data-popover-*` attributes, and the `.card__cat`, `.card__title`, `.card__sub` text. Cards go inside `.links-grid` divs. Use `.full` class on the grid for a single full-width card.

## Voice & Constraints

Eugene's brand centers on **cognitive sovereignty** and the **anthropomorphism cascade** — the measurable pattern by which AI anthropomorphism leads users through attachment → cognitive outsourcing → identity erosion. His projects:

| URL | Identity | Purpose | Image |
|-----|----------|---------|-------|
| bitscopic.com | Eugene Geis | Current employer — healthcare AI | none |
| medium.com/@eugene.geis | Eugene Geis | Long-form writing | hero.jpg |
| holdingframepodcast.com | Eugene Geis | Podcast | meditation.jpg |
| educationalshaman.com | Eugene Geis | Self-education, energy work, meditation | meditation.jpg |
| phd2pro.com | **Ignis Spindler** | PhD → industry transition | diplomacy.jpg |
| amazon.com/…/0985648619 | Eugene Geis | MUN-E book (Model UN, JP Stevens HS) | diplomacy.jpg |
| arxiv.org/abs/1912.12755 | Eugene Geis | Dissertation (statistics, 2nd PhD) | physics.jpg |
| journals.aps.org/prl/…/101.042501 | Eugene Geis | PRL paper (nuclear physics, 1st PhD) | physics.jpg |
| linkedin.com/in/eugenegeis | Eugene Geis | Professional network | none |

**Never produce anything that resembles "slop"** — generic AI-adjacent aesthetics, filler copy, or performative credentialism. Every word on this site should be load-bearing.

## Image Regeneration

```python
# Gemini image generation pattern (nano-banana-pro-preview)
import requests, base64, json, os

r = requests.post(
    "https://generativelanguage.googleapis.com/v1beta/models/nano-banana-pro-preview:generateContent",
    headers={"x-goog-api-key": os.environ["GEMINI_API_KEY"], "Content-Type": "application/json"},
    json={
        "contents": [{"parts": [{"text": "<prompt>"}]}],
        "generationConfig": {"responseModalities": ["IMAGE", "TEXT"]}
    }
)
data = r.json()
for part in data["candidates"][0]["content"]["parts"]:
    if "inlineData" in part:
        with open("hero.jpg", "wb") as f:
            f.write(base64.b64decode(part["inlineData"]["data"]))
```
