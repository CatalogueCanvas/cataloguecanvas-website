---
title: User documentation
---

<p class="kicker">Documentation</p>

# User documentation

<p class="lead">Day-to-day use of CatalogueCanvas for an authenticated user (admin session) and for public portfolio viewers.</p>

For deployment and configuration, see [Admin documentation](admins.md).

## Roles you'll encounter

- **Admin** — can upload, edit, organise, configure, and publish.
- **Reader** _(multi-user mode)_ — can view the whole catalogue and download files, but cannot
  modify anything; admin-only menus and controls are hidden.
- **Public viewer** — no login; can only view portfolios marked **Public** via their link.

## Signing in

If the instance runs in **multi-user mode**, sign in with your **username and password**;
otherwise enter the admin password. Your session is held in a secure cookie, and your username
is shown next to the **Log out** button.

!!! warning

    After 5 failed attempts within 5 minutes, login is temporarily blocked.

## What Readers can do

Readers get a **download-only** view of the catalogue. They can download individual files, a
single item as a ZIP, and a multi-select bulk ZIP — without gaining edit access. Editing,
tagging, favouriting, and LLM actions remain admin-only.

## Adding work (upload)

<ol class="steps" markdown>
<li markdown>Click the **upload** button in the top bar.</li>
<li markdown>Select or **drag-and-drop** one or more **ZIP files**.</li>
<li markdown>Pick the destination **library** (defaults to the default library).</li>
<li markdown>Watch the ingestion result — notes may say things like *"SVG compressed (lz4)"* or *"already ingested"*.</li>
</ol>

**What goes in a ZIP?** One ZIP = one item. Include the main image (PNG/JPEG/TIFF/SVG) plus any
supporting files (code, text, JSON, etc.). Optionally include `metadata.json` or `metadata.toml`
and it will be read automatically.

## Working with an item

On an item's page you can:

- Edit the **title** and **tags**.
- Write **notes in Markdown** — they render formatted; switch to raw mode to edit the Markdown.
- Assign the item to one or more **collections**.
- Toggle **Favourite** (heart icon), if favourites are enabled.
- **Generate an LLM description** (if configured) and apply it to your notes.
- **Navigate** to the previous/next item with the **← / → arrow keys**.
- Open linked files — images and text open inline; other types download.
- **Download** the item as a ZIP, or download all its files.

## Bulk actions

Select multiple items in the catalogue to:

- Add **tags** to all selected
- **Clear notes** on all selected
- **Favourite / unfavourite** in bulk
- **Download** all selected as one ZIP
- Add to / remove from collections

## Collections

- Create, rename, and describe collections.
- Set a **cover item**.
- Delete a collection (the system **Favourites** collection can't be edited or deleted).

## Portfolios (sharing your work)

<ol class="steps" markdown>
<li markdown>Create a portfolio with a **title** and **description**.</li>
<li markdown>Add and order the items it contains.</li>
<li markdown>A **slug** is auto-generated (e.g. `quiet-amber-loom`) — or set your own.</li>
<li markdown>Mark it **Public** to expose it at `/p/<slug>` as a slide-deck presentation.</li>
<li markdown>Share the link — no login needed to view a public portfolio.</li>
</ol>

!!! note

    Private portfolios return "not found" to anyone without admin access.

## Tips & FAQ

- **Re-uploading the same ZIP?** It's deduplicated by content hash — no duplicate is created.
- **Several images in one ZIP?** The preview is chosen by priority (PNG → JPEG → TIFF → SVG); a note explains which was used.
- **Keyboard:** ← / → move between items on the item page.
