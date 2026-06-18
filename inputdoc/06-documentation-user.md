---
title: User Documentation
section: documentation
order: 6
status: template
---

# User documentation

> Day-to-day use of CatalogueCanvas for an authenticated user (admin session) and for
> public portfolio viewers. For deployment and configuration, see [Admin documentation](07-documentation-admins.md).

## Roles you'll encounter

- **Authenticated user (admin session)** — can upload, edit, organize, and publish.
- **Public viewer** — no login; can only view portfolios marked **Public** via their link.

> The current release uses a single admin login. A view-only **Reader** role is on the [roadmap](05-roadmap.md).

## Signing in

Open the app and enter the admin password. Your session is held in a secure cookie.

> After 5 failed attempts within 5 minutes, login is temporarily blocked.

## Adding work (upload)

1. Click the **upload** button in the top bar.
2. Select or **drag-and-drop** one or more **ZIP files**.
3. Pick the destination **library** (defaults to the default library).
4. Watch the ingestion result — notes may say things like *"SVG compressed (lz4)"* or *"already ingested"*.

**What goes in a ZIP?** One ZIP = one item. Include the main image (PNG/JPEG/TIFF/SVG) plus any
supporting files (code, text, JSON, etc.). Optionally include `metadata.json` or `metadata.toml`
and it will be read automatically.

→ See [Install → ingestion details](04-install.md) and the [Glossary](08-glossary.md) for how previews are chosen.

## Working with an item

On an item's page you can:

- Edit the **title** and **tags**.
- Write **notes in Markdown** — they render formatted; switch to raw mode to edit the Markdown.
- Assign the item to one or more **collections**.
- Toggle **Favorite** (heart icon), if favorites are enabled.
- **Generate an LLM description** (if configured) and apply it to your notes.
- **Navigate** to the previous/next item with the **← / → arrow keys**.
- Open linked files — images and text open inline; other types download.
- **Download** the item as a ZIP, or download all its files.

## Bulk actions

Select multiple items in the catalogue to:

- Add **tags** to all selected
- **Clear notes** on all selected
- **Favorite / unfavorite** in bulk
- **Download** all selected as one ZIP
- Add to / remove from collections

## Collections

- Create, rename, and describe collections.
- Set a **cover item**.
- Delete a collection (the system **Favorites** collection can't be edited or deleted).

## Portfolios (sharing your work)

1. Create a portfolio with a **title** and **description**.
2. Add and order the items it contains.
3. A **slug** is auto-generated (e.g. `quiet-amber-loom`) — or set your own.
4. Mark it **Public** to expose it at `/p/<slug>` as a slide-deck presentation.
5. Share the link — no login needed to view a public portfolio.

> Private portfolios return "not found" to anyone without admin access.

## Appearance

In **Settings → Appearance** you can set theme (light/dark), accent color, navigation layout
(top bar / sidebar), density, and turn **Favorites** on or off.

## Tips & FAQ

- **Re-uploading the same ZIP?** It's deduplicated by content hash — no duplicate is created.
- **Several images in one ZIP?** The preview is chosen by priority (PNG → JPEG → TIFF → SVG); a note explains which was used.
- **Keyboard:** ← / → move between items on the item page.
- _Add common questions from real users:_ TODO
