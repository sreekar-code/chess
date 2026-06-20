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
c-images/        — cropped chess board images (img1.jpeg … img13.jpeg)
new images/      — gitignored, raw WhatsApp screenshots from Sreekar
.gitignore       — ignores new images/ and .DS_Store
```

## Adding new checkmate images

Sreekar shares screenshots from the Chess.com Android app via chat. Steps:

1. **Save** the screenshots to `new images/` folder in the repo
2. **Crop** using fixed coordinates — all screenshots from his phone are 717×1600px and the board is always at:
   - `y_start = 446`, `y_end = 1163`, `x = 0 to 717` → gives a 717×717 square
   - Use this Python snippet:
     ```python
     from PIL import Image
     img = Image.open("path/to/screenshot.jpeg")
     cropped = img.crop((0, 446, 717, 1163))
     cropped.save("c-images/imgN.jpeg", "JPEG", quality=92)
     ```
   - **Do not add padding** — any extra pixels at the top pull in the dark app background and show as a black line
3. **Name** sequentially: next after the highest existing `imgN.jpeg` in `c-images/`
4. **Caption** based on what's visible on the board — describe the key piece and finish move (e.g. "Queen Hunts the King to f5", "Knight Strikes on f3")
5. **Add to HTML** — append a new `.gallery` div inside the single `.gallery-container` in `index.html`:
   ```html
   <div class="gallery">
       <img src="c-images/imgN.jpeg" alt="Caption Here" loading="lazy">
       <div class="description">Caption Here</div>
   </div>
   ```
6. **Commit and push** to `main` — Netlify deploys automatically

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

## Pillow (image processing)
If not installed: `pip3 install Pillow --break-system-packages`
