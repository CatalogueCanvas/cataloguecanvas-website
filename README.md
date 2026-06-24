# CatalogueCanvas — Documentation Website

[![Codacy Badge](https://app.codacy.com/project/badge/Grade/d6aaf28841f44ebc8c822f85f42e772a)](https://app.codacy.com/gh/ToroRojo-code/cataloguecanvas-website/dashboard?utm_source=gh&utm_medium=referral&utm_content=&utm_campaign=Badge_grade)


The documentation and showcase website for [CatalogueCanvas](#about-cataloguecanvas), built with
**MkDocs** and the **Material** theme. The visual language (oklch tokens, IBM Plex Mono / Spectral /
Archivo type, vermilion accent, light/dark + accent switching) is ported from a Claude Design source.

> This repo holds the **website**, not the CatalogueCanvas application source.

## Prerequisites

- Python 3.11+
- [uv](https://docs.astral.sh/uv/)

## Quick start

```bash
uv sync                 # create the environment and install MkDocs + Material
uv run mkdocs serve     # live-reload dev server at http://127.0.0.1:8000
```

## Build

```bash
uv run mkdocs build            # output to ./site
uv run mkdocs build --strict   # fail on warnings (use this in CI)
```

The `site/` directory is the generated static site; it is git-ignored.

## Project layout

```text
docs/
  index.md                  # home
  showcase/                 # ethos, intro, uses & for whom
  catalog/                  # uploading items
  documentation/            # install, roadmap, user, admin, glossary
  stylesheets/extra.css     # design tokens + component styles (oklch, accents)
  javascripts/accent.js     # header accent-swatch picker (persisted)
overrides/
  main.html                 # loads Archivo / Spectral / IBM Plex Mono webfonts
mkdocs.yml                  # nav, theme, palettes, markdown extensions
pyproject.toml              # uv-managed dependencies
inputdoc/                   # source content drafts (not the live pages)
media/                      # logo assets
```

## Theming

- Custom Material colour schemes `catalog-light` / `catalog-dark` (toggle in the header).
- Five accent presets — vermilion (default), cobalt, terracotta, forest, ink — selectable from the
  header swatch bar and persisted in `localStorage`.
- Tokens and component styles live in `docs/stylesheets/extra.css`; fonts in `overrides/main.html`.

## Contributing content

- Add a page under `docs/` and register it in the `nav:` of `mkdocs.yml`.
- **UK spelling** throughout (organise, favourites, colour, licence, …).
- Run user-facing prose through the `/deslop` and `/humanizer` skills before publishing.
- Reusable design helpers available in Markdown: `<p class="kicker">`, `<p class="lead">`,
  `<ol class="steps">`, admonitions (`!!! note` / `tip` / `warning`), and
  `<div class="plate-ph" data-label="…">` for placeholder image plates.

## About CatalogueCanvas

CatalogueCanvas is a self-hosted, domain-agnostic catalogue server: ingest items as ZIP files,
organise them into collections, enrich them with metadata and optional AI-generated descriptions,
and publish curated portfolios as shareable slide-deck pages — all from a single Docker container.
See the [documentation](docs/) for details.

## Licence

CatalogueCanvas is open source under the **GNU Affero General Public License v3.0**.
