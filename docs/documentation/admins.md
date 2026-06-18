---
title: Admin documentation
---

<p class="kicker">Documentation</p>

# Admin documentation

<p class="lead">Configuration, storage, AI setup, backups, and operations.</p>

For everyday use (uploading, editing, portfolios), see [User documentation](user.md). For
deployment, see [Install](install.md).

## Access model

CatalogueCanvas can run in single-admin or **multi-user** mode, secured by passwords and a
session cookie.

- **Admin** — full access: upload, edit, organise, configure, and publish. All management
  screens require an authenticated admin session.
- **Reader** _(multi-user mode)_ — can view the whole catalogue and download files, but cannot
  modify anything; all admin-only menus and controls are hidden.
- **Public visitor** — no login; can only reach portfolios marked **Public**.

When multi-user mode is **on**, users sign in with a **username and password**, and the admin
and reader passwords must differ. When it is **off**, the original single-password admin login
is used. The signed-in username is shown next to the **Log out** button. Admins manage accounts
from the **Users** panel in Settings.

Security notes:

- Login is **rate-limited**: 5 failed attempts from one address in 5 minutes blocks further tries.
- Passwords are hashed (argon2); sessions use a signed cookie.
- The session signing key is generated automatically on first start and persisted at
  `<CC_DATA_DIR>/cc_secret_key.txt` — no manual setup required.
- Set a strong `CC_ADMIN_PASSWORD` in production (and a distinct reader password if multi-user
  mode is enabled).

## Settings overview

The **Settings** page groups: Appearance, Users, LLM defaults, Prompt template, Libraries, and Backup & export.

### Appearance
Theme (light/dark), accent colour, navigation layout (top/side), density (airy/balanced/dense),
and enable/disable **Favourites**.

### Users
Enable **multi-user mode** and manage accounts. Each user has a username, a password, and a
role (Admin or Reader). The admin and reader passwords must differ. With multi-user mode off,
the instance uses the single-password admin login.

### LLM defaults

| Field | Meaning |
|---|---|
| API URL | OpenAI-compatible chat-completions endpoint |
| Model | Model name to call |
| Item type | What the items are (e.g. "image", "artwork") |
| Summary focus | What the description should emphasise |
| Bullet points | How many bullets to generate |
| Max words per bullet | Length cap per bullet |
| Generate description (LLM) | Whether the item editor offers a generate button |

### Prompt template
Edit the raw **TOML** prompt used to build the LLM request. Placeholders:
`{item_type}`, `{summary_focus}`, `{bullet_count}`, `{bullet_max_words}`. Includes a
**reset-to-default** option.

### Libraries (multi-storage)

- Add libraries pointing at different **writable directories** (e.g. a second disk).
    - Paths must already be **mounted into the container** and writable.
- Set any library as the **default** for new uploads.
- Constraints: a library's **path can't change** once it holds items; a library **can't be deleted**
  while it contains items or while it is the default.

### Backup & export

- **Database backup** — a single SQLite snapshot.
- **Full backup** — a ZIP of the database **plus every stored asset** across all libraries.
- Live stats shown: total items, total collections, items missing a preview.

## LLM / AI descriptions in depth

- Works with any **OpenAI-compatible vision LLM** — local (LM Studio, Ollama) or hosted.
- An **API key may be supplied per request** and is used **only for that request — never stored**.
  This also applies to **batch generation**: an optional API-key field is available when
  generating descriptions for many selected items at once.
- The API URL is **auto-completed** — enter just the host and port
  (e.g. `http://host.docker.internal:1234`) and `/v1/chat/completions` is appended; a full URL
  also works.
- **Reasoning is stripped** from results: `<think>` blocks and reasoning preambles from
  "thinking" models are removed, and the prompt requests a clean JSON-only response, so
  descriptions contain only the summary and bullet points.
- The request **timeout is 90 seconds**, to accommodate slower local models.
- Failures report the **actual cause** (connection, HTTP error, non-JSON, or no choices
  returned) rather than an opaque error.

## How items are stored (operator view)

- Each item lives under its library at `items/<item-id>/` with a `preview.webp` and an `other/` folder.
- **SVGs are LZ4-compressed** on disk. Compressed files are served **exactly as stored, as a
  download** — never decompressed or rendered in the browser. The WebP preview generated at
  ingestion remains the display image.
- Item IDs are unique `word-NNN` strings, checked against the database to avoid collisions.
- Items are **deduplicated by content hash** at ingest.

## Diagnostics

Admins can download a **redacted Markdown diagnostic report** from Settings (or generate it via
a CLI script) to attach to a GitHub issue. It covers:

- Versions and **build provenance** (git SHA and build date).
- **Masked** configuration (no secrets).
- Disk and storage usage.
- LLM configuration plus a **live endpoint reachability probe**.
- Database counts.
- A **storage-integrity check** (missing or orphaned files).

## Operations checklist

- [ ] Strong `CC_ADMIN_PASSWORD` set (and a distinct reader password in multi-user mode)
- [ ] Data volume sized for expected assets (it also holds the auto-generated session key)
- [ ] Library paths mounted and writable
- [ ] Regular backup routine in place
- [ ] `CC_SITE_TITLE` / `CC_SITE_AUTHOR` set for public portfolios
