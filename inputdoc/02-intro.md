---
title: Introduction
section: showcase
order: 2
status: template
---

# CatalogueCanvas

> A domain-agnostic catalogue server: ingest ZIP items into a self-hosted app, organize them into collections, and share public portfolios.

## What it is

<!-- 1 short paragraph, plain language, for a first-time visitor. -->

CatalogueCanvas is a self-hosted catalogue server. You ingest items as ZIP files, organize
them into collections, enrich them with metadata and optional AI-generated descriptions, and
publish curated portfolios as shareable slide-deck pages — all from a single Docker container.

## What it does

- **Ingest** — one ZIP becomes one catalogue item; the main image is auto-converted to a WebP preview.
- **Organize** — group items into collections and a built-in Favorites set.
- **Enrich** — edit titles, tags, and Markdown notes; optionally describe items with a vision LLM.
- **Publish** — assemble portfolios and share them publicly at a `/p/<slug>` link.
- **Store & back up** — keep assets across multiple libraries; export the database and all files.

## See it in action

<!-- Drop screenshots / GIFs / a demo portfolio link here. -->

- Screenshot: dashboard — TODO
- Screenshot: item page — TODO
- Live demo portfolio: TODO
- Logo assets: `media/logo.png`, `media/logo_dark.png`

## Key concepts (quick)

| Concept | In one line |
|---|---|
| **Item** | A single catalogued work, created from one uploaded ZIP. |
| **Collection** | A named grouping of items. |
| **Portfolio** | A curated, shareable slide-deck of items. |
| **Library** | A storage location for item assets. |

→ Full definitions in the [Glossary](08-glossary.md).

## Next steps

- New here? Read **[Uses & for whom](03-uses-for-whom.md)**.
- Want to run it? See **[Install](04-install.md)**.
- Using it day to day? See the **[User documentation](06-documentation-user.md)**.
