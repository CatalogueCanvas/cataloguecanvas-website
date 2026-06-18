---
title: Admin Documentation
section: documentation
order: 7
status: template
---

# Admin documentation

> Configuration, storage, AI setup, backups, and operations. For everyday use (uploading,
> editing, portfolios), see [User documentation](06-documentation-user.md). For deployment,
> see [Install](04-install.md).

## Access model

CatalogueCanvas uses a **single admin login** secured by a password and a session cookie.

- **Admin** — full access; all management screens require an authenticated session.
- **Public visitor** — no login; can only reach portfolios marked **Public**.

Security notes:
- Login is **rate-limited**: 5 failed attempts from one address in 5 minutes blocks further tries.
- Passwords are hashed (argon2); sessions use a signed cookie.
- Set a strong `CC_ADMIN_PASSWORD` and a random session key (`CC_SECRET_KEY_FILE`) in production.

> A view-only **Reader** role is planned; see the [roadmap](05-roadmap.md).

## Settings overview

The **Settings** page groups: Appearance, LLM defaults, Prompt template, Libraries, and Backup & export.

### Appearance
Theme (light/dark), accent color, navigation layout (top/side), density (airy/balanced/dense),
and enable/disable **Favorites**.

### LLM defaults
Configure description generation:

| Field | Meaning |
|---|---|
| API URL | OpenAI-compatible chat-completions endpoint |
| Model | Model name to call |
| Item type | What the items are (e.g. "image", "artwork") |
| Summary focus | What the description should emphasize |
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
- From Docker to a host LLM, use `http://host.docker.internal:<port>/v1/chat/completions`.
- Common error `[Errno 111] Connection refused` = endpoint unreachable; check URL/host.

## How items are stored (operator view)

- Each item lives under its library at `items/<item-id>/` with a `preview.webp` and an `other/` folder.
- **SVGs are LZ4-compressed** on disk; originals remain downloadable (decompressed on the fly).
- Item IDs are unique `word-NNN` strings, checked against the database to avoid collisions.
- Items are **deduplicated by content hash** at ingest.

## Backups & recovery

1. **Before any upgrade or risky change**, run **Settings → Backup & export → Full backup**.
2. Store backups off the host.
3. To restore: TODO — document your restore procedure (DB file + storage layout).

## Operations checklist

- [ ] Strong `CC_ADMIN_PASSWORD` set
- [ ] Random session key via `CC_SECRET_KEY_FILE`
- [ ] Data volume sized for expected assets
- [ ] Library paths mounted and writable
- [ ] Regular backup routine in place
- [ ] `CC_SITE_TITLE` / `CC_SITE_AUTHOR` set for public portfolios
- [ ] _TODO_

## Reference

- Environment variables and footprint: see [Install](04-install.md).
- Terminology: see [Glossary](08-glossary.md).
