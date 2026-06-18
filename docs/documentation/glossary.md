---
title: Glossary
---

<p class="kicker">Documentation</p>

# Glossary

<p class="lead">Terminology used across CatalogueCanvas and this documentation.</p>

### Item
A single catalogued work, created from **one uploaded ZIP**. Has a WebP preview, supporting
files, a title, tags, and Markdown notes.

### Item ID
A unique, human-friendly identifier auto-assigned at ingestion, of the form `word-NNN`
(e.g. `lantern-042`). Checked against the database to avoid collisions.

### Collection
A named grouping of items (e.g. "Sketches 2024"). An item can belong to many collections.
A collection can have a **cover item**, a title, and a description.

### Favourites
A built-in **system collection**, always present, toggled per item via the heart icon. Can be
turned off in Settings. Unlike normal collections, it can't be edited or deleted.

### Portfolio
A curated, ordered set of items published as a **slide-deck**. Has a title, description, and a
**slug**. Can be marked **Public** to be viewable without logging in.

### Slug
The URL-friendly identifier of a portfolio, used in its public link `/p/<slug>`. Auto-generated
as three words (e.g. `quiet-amber-loom`) or set manually. Must be unique.

### Public portfolio
A portfolio marked **Public**, reachable by anyone at `/p/<slug>` with no login. Private
portfolios return "not found" to non-admins.

### Library
A **storage location** (a folder/path) where item assets are kept. Multiple libraries allow
storing items across different disks or paths. One library is the **default** for new uploads.

### Default library
The library that receives new uploads unless another is chosen. Can't be deleted while it's
the default.

### Ingestion
The process of turning an uploaded ZIP into an item: dedup check, preview selection, WebP
conversion, SVG compression, file storage, metadata read, and ID assignment.

### Preview
The WebP image generated from an item's main image. The source image is chosen by priority:
PNG → JPEG → TIFF → SVG.

### Content hash
A SHA-256 hash of an uploaded ZIP used for **deduplication** — re-uploading identical content
is skipped instead of duplicated.

### Metadata (`metadata.json` / `metadata.toml`)
An optional file inside a ZIP. If present, its contents are read and stored with the item.

### LZ4
A fast compression format. **SVG source files are stored LZ4-compressed** on disk and
transparently decompressed when viewed or downloaded.

### WebP
The image format CatalogueCanvas converts previews to — small and high quality.

### Notes
Per-item free text written in **Markdown**, rendered as formatted text with a raw-edit mode.

### Tags
Free-form labels on items, addable individually or in **bulk**.

### Bulk actions
Operations applied to multiple selected items at once: add tags, clear notes,
favourite/unfavourite, download as ZIP, add/remove collections.

### LLM description
An AI-generated summary of an item's preview image, produced by an **OpenAI-compatible vision
LLM** (local or hosted). API keys supplied per request are never stored.

### Admin
A privileged user, authenticated by password + session cookie, with full access: upload, edit,
organise, configure, and publish.

### Reader
A **view-only** role available in multi-user mode. Can view the whole catalogue and download
files (individual files, item ZIPs, bulk ZIPs) but cannot modify anything; admin-only menus and
controls are hidden.

### Multi-user mode
An optional mode in which users sign in with a **username and password** and hold either the
**Admin** or **Reader** role. The admin and reader passwords must differ. With it off, the
instance uses a single-password admin login.

### Full backup
A downloadable ZIP containing the SQLite database **plus all stored assets** across every library.
