# SoundPilot — landing page

Single-file bilingual (ES/EN) landing page for **SoundPilot**, the AI-assisted platform for
private guitar teachers. Built to explain the project and collect early user feedback.

Content is derived from the technical spec in
`AgentFlow — Plataforma Integral Asistida por IA para Profesores Particulares.pdf`
(the spec calls the product *GuitarFlow*; the page uses **SoundPilot**).

## Run locally

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

`index.html` is fully self-contained: all CSS and JS are inline. The only external
request is the Google Fonts stylesheet (Fraunces / Karla / IBM Plex Mono), and there
are system-font fallbacks if it fails to load.

## Deploy

Drop the repo on any static host — no build step:

- **Netlify / Vercel / Cloudflare Pages** — connect the repo, no build command, publish root `/`
- **GitHub Pages** — Settings → Pages → deploy from `main`, root

## Contact

Direct contact is **Julio Moreyra · +34 606 15 63 20**, shown in two places:

- the "prefieres hablar directamente?" block under the feedback questions (WhatsApp
  button + `tel:` link)
- the footer

## Wire up the feedback form

The form is at the bottom of `index.html`, in the third `<script>` block:

```js
var ENDPOINT='';              // ← paste a form endpoint here
var WHATSAPP='34606156320';   // fallback target when ENDPOINT is empty
var PHONE='+34 606 15 63 20'; // shown in error copy
```

- **Leave `ENDPOINT` empty** (current state): submitting opens WhatsApp to Julio with
  every field pre-filled as a message — the visitor just hits send. Zero backend, and it
  works on phones, which is where most teachers will open this.
- **Set `ENDPOINT`** to a [Formspree](https://formspree.io), [Basin](https://usebasin.com)
  or [Getform](https://getform.io) URL: the form POSTs JSON there instead and shows an
  inline confirmation. Better if you want submissions collected somewhere queryable.

The POST payload includes `name`, `email`, `role`, `students`, `message`, `beta` and `lang`.

To change the number, update `WHATSAPP` (digits only, country code, no `+`), `PHONE`, the
`wa.me/` and `tel:` links in the contact block, and the `tel:` link in the footer.

## Editing content

Every translatable string is a pair of sibling elements:

```html
<span data-lang="es">Texto en español</span><span data-lang="en">English text</span>
```

CSS hides whichever doesn't match `<html data-lang>`. The switcher persists the choice in
`localStorage` and falls back to the browser language on first visit. When adding copy,
**always add both variants** — a missing one just renders as empty space.

`<select>` options use the same `data-lang` attribute and are toggled by the script
(browsers don't reliably hide `<option>` via CSS alone).

## Structure

| Section | id | Purpose |
|---|---|---|
| Hero | `#top` | 40 students = 40 projects; primary CTA |
| Problem | `#problema` | Five concrete pains |
| Pillars | — | Organise / catalogue / record |
| Multi-cam | `#magia` | The differentiator: student watches their lesson being produced |
| AI | `#ia` | Tag 15 files, AI does the rest; the 8 dimensions |
| Modules | `#modulos` | All 12 modules, tagged v1 / v2 |
| Who + status | `#estado` | Honest fit, real project state, roadmap |
| Feedback | `#feedback` | 4 research questions + form |

## Notes

- Module and roadmap claims are labelled **v1** (working in the prototype) vs **v2**
  (roadmap) to match the spec — keep that honest as scope changes.
- The page states "buscamos 20 profesores" and "gratis durante la beta". Adjust if the
  offer changes.
