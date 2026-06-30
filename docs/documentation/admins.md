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

When multi-user mode is **on**, users sign in with a **username and password**, and every
user's password must be **unique**. When it is **off**, the original single-password admin login
is used. The signed-in username is shown next to the **Log out** button. Admins manage accounts
from the **Users** panel in Settings.

Security notes:

- Login is **rate-limited**: 5 failed attempts from one address in 5 minutes blocks further tries.
- Passwords are hashed (argon2); sessions use a signed cookie.
- Sessions are **server-tracked and revocable**, and state-changing requests are protected
  against CSRF with a double-submit token.
- The session signing key is generated automatically on first start and persisted at
  `<CC_DATA_DIR>/cc_secret_key.txt` — no manual setup required.
- Set a strong `CC_ADMIN_PASSWORD` in production (and a distinct password for every account in
  multi-user mode).

## Settings overview

The **Settings** page groups: Appearance, Users, LLM defaults, Prompt template, Libraries, and Backup & export.

### Appearance

Theme (light/dark), accent colour, navigation layout (top/side), density (airy/balanced/dense),
and enable/disable **Favourites**.

<figure markdown>
  ![Appearance settings](../assets/sc_Appearance_CatalogueCanvas.png)
  <figcaption>Settings → Appearance</figcaption>
</figure>

### Users

Enable **multi-user mode** and manage accounts. Each user has a username, a password, and a
role (Admin or Reader). Every user's password must be unique. The **last remaining admin**
cannot be demoted or deleted. With multi-user mode off, the instance uses the single-password
admin login.

<figure markdown>
  ![Multi-user access settings](../assets/sc_MultiUser_CatalogueCanvas.png)
  <figcaption>Settings → Users (multi-user mode on, with Admin and Reader accounts)</figcaption>
</figure>

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

<figure markdown>
  ![LLM defaults settings](../assets/sc_LLM_CatalogueCanvas.png)
  <figcaption>Settings → LLM defaults</figcaption>
</figure>

### Prompt template

Edit the raw **TOML** prompt used to build the LLM request. Placeholders:
`{item_type}`, `{summary_focus}`, `{bullet_count}`, `{bullet_max_words}`. Includes a
**reset-to-default** option.

<figure markdown>
  ![Prompt template settings](../assets/sc_Prompt_CatalogueCanvas.png)
  <figcaption>Settings → Prompt template</figcaption>
</figure>

### Libraries (multi-storage)

- Add libraries pointing at different **writable directories** (e.g. a second disc).
    - Paths must already be **mounted into the container** and writable.
- Set any library as the **default** for new uploads.
- Constraints: a library's **path can't change** once it holds items; a library **can't be deleted**
  while it contains items or while it is the default.

<figure markdown>
  ![Libraries settings](../assets/sc_Libraries_CatalogueCanvas.png)
  <figcaption>Settings → Libraries</figcaption>
</figure>

### Backup & export

- **Database backup** — a single SQLite snapshot.
- **Full backup** — a ZIP of the database **plus every stored asset** across all libraries.
- Live stats shown: total items, total collections, items missing a preview.

!!! warning "Exports are unencrypted"

    Database and full-data exports are admin-only but unencrypted. Download and store them over a trusted channel.

<figure markdown>
  ![Backup and export settings](../assets/sc_Backup_CatalogueCanvas.png)
  <figcaption>Settings → Backup &amp; export</figcaption>
</figure>

## Collections and portfolio visibility (multi-user mode)

In multi-user mode each collection and portfolio has a **visibility** setting. **Admin only** (the default) hides the item from reader accounts entirely — it won't appear in lists, and direct URLs return "not found". **Readers** makes it visible to reader accounts as well.

After an upgrade, all existing collections and portfolios land on admin-only. Admins need to change them to **Readers** explicitly. Public portfolios at `/p/<slug>` are unaffected by this setting.

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
- The `api_url` is validated **at settings-save time** — an invalid or non-allowlisted URL is rejected immediately with an error rather than failing silently at describe time.

### LLM endpoint allowlist (`CC_LLM_ALLOWED_HOSTS`)

`CC_LLM_ALLOWED_HOSTS` is optional. When set, it takes a comma-separated list of hostnames or IPs; any `api_url` whose host isn't on the list is rejected when you save settings. This stops the Describe feature from being redirected at internal services you didn't intend.

Self-hosted servers on your LAN need to be listed explicitly, e.g. `CC_LLM_ALLOWED_HOSTS=ollama.lan,192.168.1.50`. Leave it unset for no restriction — the previous behaviour.

See [Configuration](install.md#configuration) for all available environment variables.

## Findable metadata (FAIR)

- **Full-text search** covers every item's title, description/note, tags, and the full contents
  of its uploaded `metadata.json` / `metadata.toml`. Results are ranked by relevance, with prefix
  matching. Search is handled on the server.
- Each item exposes a **machine-readable metadata record** in **JSON-LD** (schema.org
  `VisualArtwork` / Dublin Core) at `/api/items/{id}/metadata`, linked from the item page. The
  record embeds the item's persistent ID as `@id` / `identifier`, maps title, description, and
  tags to standard terms, and carries the uploaded metadata as additional properties — so items
  are **findable and harvestable by external tools** (the "F" in FAIR).

## How items are stored (operator view)

- Each item lives under its library at `items/<item-id>/` with a `preview.webp` and an `other/` folder.
- **SVGs are LZ4-compressed** on disc. Compressed files are served **exactly as stored, as a
  download** — never decompressed or rendered in the browser. The WebP preview generated at
  ingestion remains the display image.
- Item IDs are unique `word-NNN` strings, checked against the database to avoid collisions.
- Items are **deduplicated by content hash** at ingest.
- Before extracting a ZIP, ingestion checks available disc space and enforces a per-file size cap as files are decompressed. An archive that would breach the limit is rejected with a clear error; normal uploads are not affected.

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
