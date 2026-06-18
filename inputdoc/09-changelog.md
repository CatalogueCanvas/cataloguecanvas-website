---
title: Changelog
section: documentation
order: 9
status: template
---

# Changelog

> Recent changes to CatalogueCanvas. Newest first. This is a template — confirm
> each entry against the repository before publishing.

## 2026-06-18

A round of work focused on multi-user access, deployment simplicity, LLM
reliability, file handling, and operability. The notes below describe the
current behaviour after these changes.

### Multi-user mode with a Reader role

CatalogueCanvas is no longer admin-only. An instance can enable **multi-user
mode** with two roles:

- **Admin** — full access: upload, edit, organize, configure, and publish.
- **Reader** — can view the whole catalogue and download files, but cannot
  modify anything; all admin-only menus and controls are hidden.

When multi-user mode is on, users log in with a **username and password** (admin
and reader passwords must differ). When it is off, the original single-password
admin login is unchanged. The signed-in **username is shown next to the Log out
button**, adapting to the top-bar or sidebar layout. Admins manage users from a
new Users panel in Settings.

> This supersedes the previous "single admin login" model and the roadmap entry
> that listed multi-user mode as not yet implemented.

### Reader downloads

Readers can download any file — individual files, a single item as a ZIP, and a
multi-select bulk ZIP — without gaining edit access. Readers get a download-only
selection mode in the catalogue; editing, tagging, favouriting, and LLM actions
remain admin-only.

### Simpler, self-contained deployment

- **The session signing key is now generated automatically** on first start and
  persisted in the data volume (`/data/cc_secret_key.txt`), then reused on every
  restart. The old manual `openssl rand` / Docker-secret setup step is gone — no
  pre-creation of a `secrets/` file is required.
- Running the app is now just:
  ```bash
  CC_ADMIN_PASSWORD=yourpassword docker compose up --build
  ```
- The container records **build provenance** (git SHA and build date) so the
  exact running version is identifiable.

### LLM descriptions: more robust

- **API URL is auto-completed.** You can enter just the server host and port
  (e.g. `http://host.docker.internal:1234`) and the `/v1/chat/completions` path
  is added automatically; a full URL still works.
- **Clearer failures.** Connection, HTTP-error, non-JSON, and "model didn't
  return choices" cases now report the actual cause instead of an opaque error.
- **Reasoning is stripped from results.** Output from "thinking" models (e.g.
  `<think>` blocks or reasoning preambles) is removed so descriptions contain
  only the intended summary and bullet points, and the prompt now requests a
  clean JSON-only response.
- **Request timeout raised to 90 seconds** to accommodate slower local models.
- **Batch generation can use an API key.** When generating descriptions for many
  selected items at once, an optional API-key field is available; the key is used
  only for that run and is never stored.

### File handling

- **Compressed (LZ4) files download correctly.** Fixed a bug where the raw URL
  for an LZ4-stored file (e.g. a compressed SVG) returned "file not found". These
  files are now served exactly as stored, as a download — never decompressed or
  rendered in the browser. The WebP preview generated at ingestion remains the
  display image.

### Operability and UI polish

- **Diagnostic report.** Admins can download a redacted Markdown diagnostic
  report from Settings (or generate it via a CLI script) for attaching to a
  GitHub issue. It covers versions and build provenance, masked configuration (no
  secrets), disk and storage usage, LLM configuration plus a live endpoint
  reachability probe, database counts, and a storage-integrity check (missing or
  orphaned files).
- **Proper 404 page.** Unknown pages now show a simple, on-brand 404 page in the
  browser, while API clients still receive JSON errors.
- **Tag editing feedback.** Saving tags on an item now confirms the save and
  shows the item's current tags as chips, matching the catalogue view.
- **Batch controls scoped to the Items page.** The batch-edit / select control
  appears only on the Items page; leaving the page clears any active selection.
- **Footer cleanup.** Removed the "Instance Details (more to come)" placeholder.
