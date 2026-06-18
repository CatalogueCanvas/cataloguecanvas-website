# CatalogueCanvas — Functionality & Instructions

CatalogueCanvas is a self-hosted, domain-agnostic catalogue server. You ingest items as ZIP files, organize them into collections, enrich them with metadata and AI-generated descriptions, and publish curated portfolios as public slide-deck pages — all from a single Docker container.

It is built for cataloguing any kind of visual or file-based work: generative art, illustrations, code sketches, photography, design assets, or mixed-media archives.

---

## What it does

- **Ingest** ZIP files as individual catalogue items, one ZIP = one item.
- **Auto-preview** the main image of each item, converted to WebP.
- **Organize** items into collections and a built-in Favorites collection.
- **Edit** titles, tags, and Markdown notes per item, individually or in bulk.
- **Describe** items automatically with a vision-capable LLM (OpenAI-compatible).
- **Publish** portfolios — curated sets of items presented as a public slide deck at a shareable link.
- **Store** assets across multiple libraries on different disks or paths.
- **Back up** the database and all stored assets from the UI.

---

## Core concepts

| Concept | Description |
|---|---|
| **Item** | A single catalogued work, created from one uploaded ZIP. Has a preview image, files, title, tags, and notes. |
| **Collection** | A named grouping of items (e.g. "Sketches 2024"). Items can belong to many collections. |
| **Favorites** | A built-in system collection, always present, that can be toggled per item. Can be turned off in Settings. |
| **Portfolio** | A curated, ordered set of items published as a slide deck. Can be made public and shared via a `/p/<slug>` link. |
| **Library** | A storage location (folder/path) where item assets are kept. Multiple libraries can live on different disks. |
| **Item ID** | A unique, human-friendly identifier auto-assigned at ingestion in the form `word-NNN` (e.g. `lantern-042`). |

---

## How ingestion works

Each item is uploaded as a **single ZIP file**. On ingest, CatalogueCanvas:

1. **Deduplicates** by content hash — re-uploading the same ZIP is skipped, not duplicated.
2. **Selects a preview image** from the archive, preferring (in order) PNG → JPEG → TIFF → SVG. If several candidate images exist, the first is used and a note explains the choice.
3. **Converts** that preview to a high-resolution **WebP** image.
4. **Compresses SVG** source files with **LZ4** to save space (the original SVG remains downloadable).
5. **Stores all other files** (code, JSON, TOML, text, etc.) alongside the item so they can be viewed or downloaded.
6. **Reads metadata** automatically if the ZIP contains `metadata.json` or `metadata.toml`.
7. **Assigns a unique item ID** (`word-NNN`), checked against the database to avoid collisions.

**Recognized file types inside a ZIP:**
- **Images:** png, jpg, jpeg, gif, webp, svg, bmp, tiff
- **Text/code:** txt, md, json, toml, yaml, csv, py, r, js, ts, tsx, jsx, p5, html, css, sh
- **Other:** any other file is stored and offered as a download.

---

## Roles & access

CatalogueCanvas uses a **single admin login** secured by a password and a session cookie.

- **Admin** — full access. Logs in with the admin password to upload, edit, organize, configure, and publish. All management screens require an authenticated admin session.
- **Public visitor** — no login required. Can only view portfolios that an admin has marked **Public**, via their `/p/<slug>` link. No other part of the catalogue is exposed.

> Login is rate-limited: after 5 failed attempts from one address within 5 minutes, further attempts are temporarily blocked.

---

## For Admins

### Logging in
Open the app and enter the admin password (set at deployment via `CC_ADMIN_PASSWORD`). The session is kept in a secure cookie.

### Uploading items
- Click the upload button in the top bar to open the upload screen.
- Select or drag-and-drop one or more **ZIP files**.
- Choose which **library** receives the items (defaults to the default library).
- Ingestion reports progress, including notes such as "SVG compressed (lz4)" or "already ingested".

### Editing items
On an item's page you can:
- Edit the **title** and **tags**.
- Write **notes in Markdown** — notes render as formatted text and have an edit mode for the raw Markdown.
- Assign the item to one or more **collections**.
- Toggle **Favorite** (heart icon), if favorites are enabled.
- **Generate an LLM description** (if configured) and apply it to the notes.
- **Navigate** between items with the **left/right arrow keys**.
- View linked files (images/text open inline; other types download).
- **Download** the item as a ZIP, or download all its files.

### Bulk actions
Select multiple items from the catalogue to:
- Add **tags** to all selected.
- **Clear notes** on all selected.
- **Favorite / unfavorite** in bulk.
- **Download** all selected as a single ZIP archive.
- Add to / remove from collections.

### Collections
- Create, rename, and describe collections.
- Set a **cover item** for a collection.
- Delete collections (the system **Favorites** collection cannot be edited or deleted).

### Portfolios
- Create a portfolio with a title and description.
- Add and order the items it contains.
- A **slug** is auto-generated (e.g. `quiet-amber-loom`) or you can set your own.
- Mark it **Public** to expose it at `/p/<slug>` as a slide-deck presentation.
- Share the link with anyone — no login needed to view a public portfolio.

### Libraries (multi-storage)
- Add additional **libraries** pointing at different writable directories (e.g. a second disk).
- Set any library as the **default** for new uploads.
- A library's path can't be changed once it holds items, and a library can't be deleted while it contains items or while it is the default.

### Settings
- **Appearance:** theme (light/dark), accent color, navigation layout (top bar / sidebar), density (airy/balanced/dense), and enable/disable Favorites.
- **LLM defaults:** API URL, model, item type, summary focus, number of bullet points, max words per bullet, and whether to auto-offer description generation.
- **Prompt template:** edit the raw TOML prompt used for LLM descriptions, with a reset-to-default option.
- **Backup & export:** download a database-only backup, or a full backup (database + all stored assets) as a ZIP.

### LLM descriptions
CatalogueCanvas calls any **OpenAI-compatible vision LLM** to describe an item's preview image.
- Configure the API URL and model in **Settings**.
- An API key may be entered per request — it is used only for that request and **never stored**.
- Works with local servers (LM Studio, Ollama) and hosted APIs.
- If the app runs in Docker and the LLM runs on your host machine, point the API URL at `http://host.docker.internal:<port>/v1/chat/completions` rather than `localhost`.

---

## For Public Viewers

If someone shares a portfolio link (`/p/<slug>`):

- The portfolio opens as a **slide-deck presentation** — title, description, and the curated items.
- No account or login is required.
- Only items the admin placed in that portfolio are shown; the rest of the catalogue stays private.
- Only portfolios explicitly marked **Public** are reachable; private ones return "not found".

---

## Setting up (Admin / Operator)

### Run with Docker (recommended)

Generate a session secret once:

```bash
mkdir -p secrets
openssl rand -hex 32 > secrets/cc_secret_key.txt
```

Start the app with an admin password:

```bash
CC_ADMIN_PASSWORD=mysecretpassword docker compose up --build
```

Open `http://localhost:8000` and log in. All data persists in the `cc-data` Docker volume.

### Key configuration

| Variable | Default | Description |
|---|---|---|
| `CC_ADMIN_PASSWORD` | _(empty)_ | Admin login password — required to log in |
| `CC_SECRET_KEY_FILE` | _(unset)_ | Path to a file holding the session signing key (Docker secret) |
| `CC_SITE_TITLE` | `My Catalogue` | Title shown in the UI and on public portfolios |
| `CC_SITE_AUTHOR` | _(empty)_ | Author/owner name shown on public portfolios |
| `CC_DATA_DIR` | `/data` | Base directory for database and storage |

### Footprint

- Docker image ~436 MB.
- ~256 MB RAM for light use; more when running LLM descriptions or large ingests.
- Single CPU core is sufficient; disk usage scales with uploaded assets.

---

## Backups

From **Settings → Backup & export**:
- **Database backup** — a single SQLite file snapshot.
- **Full backup** — a ZIP containing the database plus every stored asset across all libraries.

The Settings page also shows live stats: total items, total collections, and items missing a preview.

---

## At a glance

| You want to… | Where |
|---|---|
| Add new work | Upload button (top bar) → drop ZIP files |
| Tag, note, describe a work | Item page |
| Group related works | Collections |
| Mark a favorite | Heart icon on an item |
| Publish a shareable showcase | Portfolios → mark Public → share `/p/<slug>` |
| Store on another disk | Settings → Libraries |
| Configure AI descriptions | Settings → LLM defaults |
| Save a backup | Settings → Backup & export |
