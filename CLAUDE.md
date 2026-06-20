# Chess Checkmates Site — CLAUDE.md

## What this is
A static site deployed on Netlify at `sreekar-checkmates.netlify.app`. Sreekar plays chess on Chess.com on Android and shares screenshots here to be cropped, captioned, and added to the site.

## Repo
`git@github.com:sreekar-code/chess.git`  
Netlify auto-deploys on every push to `main`.

## File structure
```
index.html       — single page, all 13 checkmate cards
c-styles.css     — all styles
c-images/        — cropped + AI-upscaled chess board images, named by caption slug
new images/      — gitignored, raw screenshots from Sreekar
.gitignore       — ignores new images/ and .DS_Store
```

## Adding new checkmate images

Sreekar shares screenshots directly in chat (not via WhatsApp — WhatsApp compresses and loses quality). Steps:

1. **Save** the screenshot to `new images/` folder in the repo
2. **Crop** using fixed coordinates — all screenshots from his Android phone are 717×1600px and the board is always at:
   - `y_start = 446`, `y_end = 1163`, `x = 0 to 717` → gives a 717×717 square
   - **Do not add padding** — extra pixels at the top pull in the dark app background and show as a black line
   ```python
   from PIL import Image
   img = Image.open("new images/screenshot.jpeg")
   cropped = img.crop((0, 446, 717, 1163))
   cropped.save("/tmp/cropped.jpeg", "JPEG", quality=92)
   ```
3. **Upscale** to 1080×1080 using Upscayl's Real-ESRGAN (installed at `/Applications/Upscayl.app`):
   ```bash
   /Applications/Upscayl.app/Contents/Resources/bin/upscayl-bin \
     -i /tmp/cropped.jpeg \
     -o /tmp/upscaled.png \
     -m /Applications/Upscayl.app/Contents/Resources/models \
     -n digital-art-4x -r 1080x1080 -f png
   ```
   Then convert PNG → JPEG:
   ```python
   from PIL import Image
   Image.open("/tmp/upscaled.png").convert("RGB").save("c-images/slug-name.jpeg", "JPEG", quality=92)
   ```
4. **Name** using the caption slug: lowercase, hyphens, no special chars (e.g. `queen-hunts-king-to-f5.jpeg`)
5. **Caption** based on what's visible on the board — describe the key piece and finish move (e.g. "Queen Hunts the King to f5", "Knight Strikes on f3")
6. **Add to HTML** — append a new `.gallery` div inside `.gallery-container` in `index.html`:
   ```html
   <div class="gallery">
       <img src="c-images/slug-name.jpeg" alt="Caption Here" loading="lazy">
       <div class="description">Caption Here</div>
   </div>
   ```
7. **Commit and push** to `main` — Netlify deploys automatically

All images in `c-images/` are 1080×1080px JPEG.

## Design decisions
- **2-column grid** on desktop (Sreekar prefers this over 3-column)
- **1-column** on mobile (≤500px breakpoint)
- **max-width: 900px** on the grid — keeps cards from getting too large on wide screens
- Card hover lifts the whole card (`translateY(-6px)`), not just the image
- Images fill the full card width (no `max-width` on `img`)
- Description has a `border-top` separator above the caption text
- Intro line break ("Up for a game?") is `display: block` on desktop, `inline` on mobile

## Chess.com link
`https://chess.com/member/sreekar98` — opens in new tab (`target="_blank"`)

## Analytics
Umami script is already wired up in `<head>` — do not remove it.

## Tools
- **Pillow** — image cropping: `pip3 install Pillow --break-system-packages`
- **Upscayl** — AI upscaling, already installed at `/Applications/Upscayl.app`. Uses `digital-art-4x` model (best for chess piece illustrations).
