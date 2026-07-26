# Demo clips

The four chapters served by the `#demos` section:

| # | File | Length | Size | Source | Chapter |
|---|---|---|---|---|---|
| 01 | `01-perfil.mp4` | 2:26 | 17 MB | `clip1.mp4` | Defining the student profile → better AI suggestions |
| 02 | `02-clase.mp4` | 10:26 | 86 MB | `clip2.mp4` | Teaching with the live tools (whiteboard, recording, loop) |
| 03 | `03-sugerencias.mp4` | 3:31 | 30 MB | `clip3.mp4` | AI suggestions for the next lesson + sending material to the student |
| 04 | `04-llamada-cobro.mp4` | 1:03 | 8 MB | `clip4.mp4` | A call interrupts → voice reminder → the payment is recorded |

Verified by sampling frames across each source: `clip4` is the one showing the teacher on
the phone, the voice-reminder panel and the payments screen; `clip3` shows the material
catalogue and song suggestions. Don't reorder these two on file name alone.

Each has a poster (first-frame thumbnail) with the same name and a `.jpg` extension.
All four are H.264 High / yuv420p, 1280×720 @ 30 fps, AAC 96 kbps, `faststart`.

The raw recordings (`clip1.mp4` … `clip4.mp4`) are HEVC/H.265 and are **git-ignored** —
they stay on your machine. Keep them: they're the source for any re-encode.

> **HEVC does not play in Chrome or Firefox on most platforms.** Never publish the raw
> `clipN.mp4` files, only the encoded versions above.

Any chapter whose file is missing shows a "clip on the way" placeholder instead of a
broken player, so the page is safe to deploy with only some clips uploaded.

## Chapter 02 is heavy

86 MB / 10:26 is a lot for a landing page — both for the visitor's data and for the repo.
`preload="metadata"` means it's only downloaded when someone presses play, so it doesn't
slow the initial page load, but it's still the first thing to fix if the demo feels slow.
In order of preference:

1. **Trim it.** Ten minutes is long for a landing page regardless of file size.
2. **Re-encode harder** — `-crf 30` roughly halves it, at some cost in text sharpness.
3. **Host it off-repo** (Cloudflare Stream, Bunny, S3) and point `data-src` at the URL.

## Re-encoding

The recipe used for all four (run from this directory):

```bash
ffmpeg -y -i clip1.mp4 \
  -map 0:v:0 -map "0:a:0?" \
  -vf "fps=30" \
  -c:v libx264 -profile:v high -level 4.0 -crf 26 -preset slow -pix_fmt yuv420p \
  -c:a aac -b:a 96k -ac 2 \
  -movflags +faststart \
  01-perfil.mp4

# poster, one second in
ffmpeg -y -ss 1 -i 01-perfil.mp4 -frames:v 1 -q:v 4 01-perfil.jpg
```

- `-movflags +faststart` is **required** — without it the browser must download the
  whole file before the first frame appears.
- Raise `-crf` (28–30) to shrink further; lower it (22–24) for more quality.
- The source is 60 fps; `fps=30` roughly halves the bitrate with no real loss for
  screen and talking-head footage.

## Renaming / reordering

File name, poster and the duration badge are set per chapter in `index.html`:

```html
<button type="button" class="chapter"
        data-src="assets/demos/01-perfil.mp4"
        data-poster="assets/demos/01-perfil.jpg">
  <span class="chapter-meta"><span class="chapter-n">01</span><span class="chapter-dur">2:26</span></span>
```

The duration badge is hand-written — **update it if you re-cut a clip**, it isn't read
from the file.
