---
title: Glossary
---

<p class="kicker">Documentation</p>

# Glossary

<p class="lead">Terminology used across CatalogueCanvas and this documentation.</p>

### Admin
A privileged user, authenticated by password + session cookie, with full access: upload, edit,
organise, configure, and publish.

### Bulk actions
Operations applied to multiple selected items at once: add tags, clear notes,
favourite/unfavourite, download as ZIP, add/remove collections.

### Collection
A named grouping of items (e.g. "Sketches 2024"). An item can belong to many collections.
A collection can have a **cover item**, a title, and a description.

### Content hash
A SHA-256 hash of an uploaded ZIP used for **deduplication** — re-uploading identical content
is skipped instead of duplicated.

### Default library
The library that receives new uploads unless another is chosen. Can't be deleted while it's
the default.

### FAIR
Findable, Accessible, Interoperable, Reusable — principles for [scientific data](https://www.go-fair.org/fair-principles/).
CatalogueCanvas addresses the **Findable** principle through full-text search over all metadata
and a JSON-LD metadata record per item.

### Favourites
A built-in **system collection**, always present, toggled per item via the heart icon. Can be
turned off in Settings. Unlike normal collections, it can't be edited or deleted.

### Full backup
A downloadable ZIP containing the SQLite database **plus all stored assets** across every library.

### Full-text search
The catalogue search, backed by a server-side index covering each item's title, description/note,
tags, and the full contents of its uploaded `metadata.json` / `metadata.toml`. Results are ranked
by relevance, with prefix matching.

### Ingestion
The process of turning an uploaded ZIP into an item: dedup check, preview selection, WebP
conversion, SVG compression, file storage, metadata read, and ID assignment.

### Item
A single catalogued work, created from **one uploaded ZIP**. Has a WebP preview, supporting
files, a title, tags, and Markdown notes.

### Item ID
A unique, human-friendly identifier auto-assigned at ingestion, of the form `word-NNN`
(e.g. `lantern-042`). Checked against the database to avoid collisions.

### Library
A **storage location** (a folder/path) where item assets are kept. Multiple libraries allow
storing items across different disks or paths. One library is the **default** for new uploads.

### LLM description
An AI-generated summary of an item's preview image, produced by an **OpenAI-compatible vision
LLM** (local or hosted). API keys supplied per request are never stored.

### LZ4
A fast compression format. **SVG source files are stored LZ4-compressed** on disk and
transparently decompressed when viewed or downloaded.

### Metadata (`metadata.json` / `metadata.toml`)
An optional file inside a ZIP. If present, its contents are read and stored with the item, and
included in the full-text search index.

### Metadata record (JSON-LD)
A machine-readable description of an item in **JSON-LD** (schema.org `VisualArtwork` / Dublin
Core), served at `/api/items/{id}/metadata` and linked from the item page. It embeds the item's
persistent ID as `@id` / `identifier`, standard terms for title/description/tags, and the
uploaded metadata — making items findable and harvestable by external tools.

### Multi-user mode
An optional mode in which users sign in with a **username and password** and hold either the
**Admin** or **Reader** role. Every user's password must be unique. With it off, the instance
uses a single-password admin login.

### Notes
Per-item free text written in **Markdown**, rendered as formatted text with a raw-edit mode.

### Portfolio
A curated, ordered set of items published as a **slide-deck**. Has a title, description, a
**slug**, and one of four presentation **themes** (Ledger, Kinetic, Brutalist, Riso). Can be
marked **Public** to be viewable without logging in, or exported as a static site.

### Static site export
A self-contained `.zip` of a public portfolio — a single `index.html` with the chosen theme
baked in, the WebP previews, and a `README.txt`. Uses only relative paths, so it can be hosted
on any static host with no server.

### Preview
The WebP image generated from an item's main image. The source image is chosen by priority:
PNG → JPEG → TIFF → SVG.

### Public portfolio
A portfolio marked **Public**, reachable by anyone at `/p/<slug>` with no login. Private
portfolios return "not found" to non-admins.

### Reader
A **view-only** role available in multi-user mode. Can view the whole catalogue and download
files (individual files, item ZIPs, bulk ZIPs) but cannot modify anything; admin-only menus and
controls are hidden.

### Slug
The URL-friendly identifier of a portfolio, used in its public link `/p/<slug>`. Auto-generated
as three words (e.g. `quiet-amber-loom`) or set manually. Must be unique.

### Tags
Free-form labels on items, addable individually or in **bulk**.

### WebP
The image format CatalogueCanvas converts previews to — small and high quality.
