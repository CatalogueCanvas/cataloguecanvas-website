# Handoff — CatalogCanvas Landing → mkdocs-landing

Port the `CatalogCanvas Landing.html` design onto the **[mkdocs-landing](https://teparsons.github.io/mkdocs-landing/)** theme by Todd Parsons. Most of the look is reproducible through the theme's `mkdocs.yaml` config; the rest is a short `extra_css` override. No template hacking required.

The design language (shared with the rest of the CatalogCanvas doc set):
- **Display** Spectral (serif), **Body** Archivo (sans), **Mono** IBM Plex Mono — all on Google Fonts.
- Warm near-neutral shades in **oklch**, vermilion **primary** accent, green **secondary**, amber **tertiary**.
- Light + dark, content box floating over a soft diagonal gradient background.

> Color values below are given as `oklch(...)` strings. mkdocs-landing injects config color values **verbatim** into CSS, and `oklch()` is valid CSS, so you can paste them straight into `mkdocs.yaml`. Hex fallbacks are in comments for older browsers — drop them in if you need to support pre-2023 engines.

---

## 1. Project layout

```
my-site/
├─ mkdocs.yaml
├─ docs/
│  ├─ index.md                 # homepage content (intro + links)
│  ├─ assets/
│  │  ├─ avatar.png            # 320×320, square — theme crops to a circle
│  │  └─ favicon.png           # 16×16
│  └─ stylesheets/
│     └─ landing.css           # the override in §3
```

Install:

```bash
pip install mkdocs-landing
mkdocs serve        # local preview at http://127.0.0.1:8000
mkdocs build        # static output to ./site
```

---

## 2. `mkdocs.yaml`

This reproduces ~80% of the design natively. Each block is annotated with the CSS variable it replaces in the HTML mockup.

```yaml
site_name: Mara Vance
site_url: https://maravance.co

# Extra CSS for the bits config can't express (see §3)
extra_css:
  - stylesheets/landing.css

theme:
  name: landing

  # ── FONTS ────────────────────────────────────────────────
  heading_font: Spectral          # --serif
  body_font: Archivo              # --sans
  mono_font: IBM Plex Mono        # --mono

  # ── HEADER ───────────────────────────────────────────────
  # header_layout == order of children in .hgrid
  header_layout:
    - avatar                      # 1 col
    - title                       # 2 cols  → completes row 1
    - tagline                     # 2 cols  → row 2
    - nav                         # 2 cols  → row 3
    - socials                     # 2 cols  → row 4
  avatar: assets/avatar.png
  avatar_size: 8rem               # --avatar-size
  round_avatar: true              # [data-avatar="round"]
  # Pronunciation line lives in the tagline (markdown):
  tagline: |
    /ˈmɑːrə vɑːns/ · she/her

    Designer & archivist cataloguing **type, marks & printed ephemera**.
    Building open reference libraries — *one specimen at a time*.
  socials:
    - label: Email
      link: mailto:hi@maravance.co
      icon: [solid, envelope]
    - label: GitHub
      link: https://github.com/maravance
      icon: [brands, github]
    - label: Bluesky
      link: https://bsky.app/profile/maravance.co
      icon: [brands, bluesky]
    - label: Website
      link: https://maravance.co
      icon: [solid, globe]

  # ── CONTENT BOX ──────────────────────────────────────────
  content_box_opacity: 0.86       # --box-opacity (use 0.78 feel in dark)
  content_box_padding: 56px       # --box-pad
  border_left: 1px solid          # --box-border-l (color from overlay below)
  border_right: 1px solid         # --box-border-r

  # ── BACKGROUND + GRADIENT ────────────────────────────────
  # Defaults to base_color; we keep the soft diagonal wash from the mockup.
  background_color: ""            # leave empty → uses mantle/base
  background_gradient_angle: 45deg                 # --grad-angle
  background_gradient_colors:                       # --grad-stops
    - color-mix(in oklab, oklch(0.97 0.004 95) 80%, transparent)
    - transparent
    - color-mix(in oklab, oklch(0.945 0.004 95) 22%, transparent)

  # ── SCHEME COLORS (primary / secondary / tertiary) ───────
  primary_color:   oklch(0.6 0.21 30)     # #e0492b vermilion  (--primary)
  secondary_color: oklch(0.55 0.11 158)   # #2f8a64 green      (--secondary)
  tertiary_color:  oklch(0.62 0.14 65)    # #bd8334 amber      (--tertiary)
  primary_color_dark:   oklch(0.68 0.2 32)    # #f0623f
  secondary_color_dark: oklch(0.68 0.12 162)  # #4cc491
  tertiary_color_dark:  oklch(0.74 0.13 72)   # #d9a24a

  # ── SHADES (base → overlay) ──────────────────────────────
  base_color:    oklch(1 0 0)          # #ffffff  content box   (--base)
  mantle_color:  oklch(0.97 0.004 95)  # #f4f3f1  page bg        (--mantle)
  crust_color:   oklch(0.945 0.004 95) # #ecebe8  insets / fills (--crust)
  overlay_color: oklch(0.88 0.005 95)  # #d9d6d1  hairlines      (--overlay)
  base_color_dark:    oklch(0.205 0.003 95)  # #2a2826
  mantle_color_dark:  oklch(0.17 0.003 95)   # #211f1e
  crust_color_dark:   oklch(0.235 0.003 95)  # #302e2b
  overlay_color_dark: oklch(0.31 0.004 95)   # #423f3b

  # ── TEXT ─────────────────────────────────────────────────
  text_color:   oklch(0.2 0.004 95)    # #2b2926   (--text)
  hltext_color: oklch(0.99 0 0)        # #fdfdfd   text on accent (--hltext)
  text_color_dark:   oklch(0.96 0.003 95)  # #f2f0ee
  hltext_color_dark: oklch(0.99 0 0)       # #fdfdfd

  # ── OTHER ────────────────────────────────────────────────
  favicon: assets/favicon.png
  toc_level: 1                    # the "Jump to" rail
  share_btn: true                 # share icon, top-right
  page_source:
    link: https://github.com/maravance/site

# Top-level nav → renders as the header `nav` element
nav:
  - Work: index.md
  - Collections: collections.md
  - Writing: writing.md
  - About: about.md
```

### Accent presets (optional)
The mockup's accent swatches map to swapping `primary_color` (+ `_dark`). Keep these handy if you want alternates:

| Preset | `primary_color` | `primary_color_dark` |
| --- | --- | --- |
| Vermilion (default) | `oklch(0.6 0.21 30)` | `oklch(0.68 0.2 32)` |
| Cobalt | `oklch(0.55 0.21 258)` | `oklch(0.7 0.17 258)` |
| Terracotta | `oklch(0.58 0.13 45)` | `oklch(0.66 0.12 48)` |
| Forest | `oklch(0.52 0.11 155)` | `oklch(0.62 0.12 158)` |
| Ink | `oklch(0.32 0.01 260)` | `oklch(0.82 0.01 260)` |

---

## 3. `docs/stylesheets/landing.css`

Config can't reach a few details from the mockup: the monospace kicker/pronunciation tracking, the LinkTree-style **link rows** (icon chip + sublabel + hover lift, cycling primary→secondary→tertiary), and selection color. This override is **additive** — it layers on top of the theme.

> ⚠️ mkdocs-landing's links page generates its own markup; class names may differ from the mockup. The selectors below target the most likely structure — have Claude Code open the rendered page (`mkdocs serve` → inspect) and adjust `.md-link` / wrapper selectors to match the real DOM. The visual *recipe* is what matters.

```css
/* ============================================================
   CatalogCanvas Landing — extra_css override for mkdocs-landing
   Layers the bits not reachable through mkdocs.yaml config.
   Tokens (--primary/--base/--text/…) are defined by the theme
   from your color config, so we just consume them here.
   ============================================================ */

:root {
  --serif: 'Spectral', Georgia, serif;
  --sans:  'Archivo', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  --mono:  'IBM Plex Mono', ui-monospace, monospace;
}

body { -webkit-font-smoothing: antialiased; }
::selection { background: color-mix(in oklab, var(--primary) 12%, transparent); }

/* Display headings in Spectral, tight tracking (matches mockup h1.name) */
h1 {
  font-family: var(--serif);
  font-weight: 600;
  letter-spacing: -0.02em;
  line-height: 1.0;
  text-wrap: balance;
}

/* Pronunciation / kicker line — first line of the tagline, monospaced */
.tagline { line-height: 1.55; }
.tagline > :first-child {
  font-family: var(--mono);
  font-size: 0.75em;
  letter-spacing: 0.06em;
  color: var(--primary);
  margin-bottom: 0.5em;
}

/* ── LinkTree-style rows ───────────────────────────────────
   Restyle the theme's link items into icon-chip + label + arrow.
   Adjust the wrapper/link selectors to the rendered markup. */
.md-links a,                /* ← verify against real DOM */
.landing-links a {
  --c: var(--primary);
  display: grid;
  grid-template-columns: 46px minmax(0, 1fr) auto;
  align-items: center;
  gap: 16px;
  padding: 16px 18px;
  border: 1px solid var(--overlay);
  border-radius: 12px;
  background: var(--base);
  text-decoration: none;
  color: var(--text);
  transition: border-color .16s, transform .16s, box-shadow .16s;
}
.md-links a:nth-child(3n+2) { --c: var(--secondary); }
.md-links a:nth-child(3n+3) { --c: var(--tertiary); }
.md-links a:hover {
  border-color: color-mix(in oklab, var(--c) 55%, var(--overlay));
  transform: translateY(-2px);
  box-shadow: 0 8px 24px color-mix(in oklab, var(--text) 8%, transparent);
}
/* icon chip (the theme renders a fontawesome <i>/<svg> first) */
.md-links a > :first-child {
  width: 46px; height: 46px;
  display: grid; place-items: center;
  border-radius: 10px;
  color: var(--c);
  background: color-mix(in oklab, var(--c) 12%, transparent);
  border: 1px solid color-mix(in oklab, var(--c) 26%, transparent);
}
/* label + monospace sublabel */
.md-links a b  { font-weight: 600; font-size: 15.5px; letter-spacing: -0.01em; }
.md-links a small,
.md-links a .sub {
  display: block;
  font-family: var(--mono);
  font-size: 11.5px;
  letter-spacing: 0.02em;
  color: var(--text-ter, var(--overlay));
  margin-top: 3px;
}

/* Header social pills — flat bordered chips (matches mockup .socials a) */
.socials a {
  border: 1px solid var(--overlay);
  border-radius: 8px;
  padding: 7px 12px 7px 10px;
  transition: border-color .15s, background .15s, color .15s;
}
.socials a:hover { background: var(--crust); border-color: var(--overlay); }
```

---

## 4. `docs/index.md` (homepage content)

The header (avatar/title/tagline/nav/socials) comes from config — `index.md` only holds the body below it. Use the theme's links syntax for the LinkTree rows; a plain markdown list works as a fallback:

```markdown
## Now

I keep a working catalogue of the specimens I collect and the systems I
build to organise them. Everything below is live — pulled from my
CatalogCanvas portfolios and updated as new work is filed.

## Links

- [Specimen Archive](https://catalogcanvas.app/mara/specimens) — 1,240 items
- [Collections](https://catalogcanvas.app/mara/collections) — 28 sets
- [Field Notes](https://maravance.co/notes) — essays on archiving
- [Print Shop](https://maravance.co/shop) — risograph editions
- [Booking & Talks](https://maravance.co/booking) — workshops + lectures
```

> For the icon + sublabel treatment in the mockup, use mkdocs-landing's **links page** format (see the theme's `links` docs) so each item emits an icon + label + description, then let §3 style it. A bare list still renders cleanly with the row styling minus the icon chip.

---

## 5. Verification checklist

- [ ] Fonts load (Spectral/Archivo/IBM Plex Mono) — check network tab, no FOUT fallback to Times.
- [ ] Light **and** dark both legible — toggle and confirm `*_dark` colors apply.
- [ ] Header grid order matches `header_layout`; avatar is circular.
- [ ] Link rows show icon chip + monospace sublabel, lift on hover, and cycle vermilion→green→amber.
- [ ] Gradient background sits behind the content box, not over it.
- [ ] Share button + favicon + `page_source` link present.
- [ ] If link selectors didn't catch, inspect the rendered DOM and update the `.md-links` selectors in §3.

The full visual reference is `CatalogCanvas Landing.html` in this project — open it side-by-side while tuning.
