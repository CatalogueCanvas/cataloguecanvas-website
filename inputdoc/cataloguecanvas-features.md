<DO NOT REMOVE>!!! This File handover changes done in CatalogueCanvas to the website-documentation incorporation in here </DO NOT REMOVE>
# CatalogueCanvas — Functionality & Instructions

## 2026-06-23 — Portfolio themes & static-site export

### New: selectable portfolio themes
- Public portfolios (`/p/<slug>`) can now be dressed in one of four presentation
  themes, chosen per portfolio in the portfolio editor: **Ledger** (default,
  archival specimen sheet), **Kinetic** (dark, animated capabilities ribbon and
  cursor-following thumbnail), **Brutalist** (stark uppercase, hard rules), and
  **Riso** (risograph overprint).
- Each theme carries its own fixed colour and type set, so a published portfolio
  looks the same regardless of the studio's own light/dark/accent appearance.
- Existing portfolios default to **Ledger** after upgrade.

### New: export a portfolio as a static site
- A new **Export static site (.zip)** button (next to "Preview deck" on a public
  portfolio) downloads a self-contained folder: one `index.html` with the chosen
  theme baked in, the webp previews, and a `README.txt` with hosting steps.
- The export needs no server and uses only relative paths, so it can be hosted on
  any static host — Codeberg Pages, GitHub Pages, Netlify, Cloudflare Pages.
- Only webp previews are bundled (the same images the public deck shows); source
  attachments are not included.

## 2026-06-22 — Security hardening (branch `audit-remediation`)

The following changes affect documentation. They are operator/admin-facing; most
have no impact on day-to-day cataloguing.

### New: LLM endpoint allowlist (`CC_LLM_ALLOWED_HOSTS`)
- New optional environment variable. When set (comma-separated hostnames/IPs),
  the LLM `api_url` host used by the **Describe** feature must match one of the
  listed hosts; anything else is rejected.
- Purpose: prevent the describe feature from being pointed at arbitrary internal
  services (SSRF guard) while still allowing intentional internal targets.
- Self-hosted LLM servers on your LAN (Ollama, LM Studio, etc.) must be added
  explicitly, e.g. `CC_LLM_ALLOWED_HOSTS=ollama.lan,192.168.1.50`.
- Leaving it unset = no host restriction (previous behaviour).
- The `api_url` is now validated at **Settings save time**, not only when a
  describe call runs — an invalid or non-allowlisted URL is rejected immediately
  with an error when you save settings.

### New: per-collection / per-portfolio visibility (multi-user mode)
- Collections and portfolios now have a **visibility** setting: `admin` (default)
  or `readers`.
- In multi-user mode, reader accounts only see collections/portfolios marked
  `readers`. Admin-only items are hidden from readers entirely (they don't appear
  in lists and return "not found" if accessed directly).
- All pre-existing collections/portfolios default to `admin`-only after upgrade —
  admins must explicitly mark items as reader-visible to share them.
- Public portfolios (the `/p/<slug>` share link) are unaffected and continue to
  work as before.

### Sessions: logout now fully revokes older tokens
- Legacy "stateless" session tokens are no longer accepted. Every session is now
  bound to the server, so logging out reliably invalidates the session.
- Impact: any user holding a very old session cookie from before this change will
  need to log in again once. Normal login/logout is otherwise unchanged.

### Uploads: safer large/compressed ZIP handling
- ZIP ingestion now checks available disk space before extracting and enforces a
  hard size limit on each file as it is decompressed (protection against
  "zip bomb" uploads). Normal uploads are unaffected; oversized or malicious
  archives are rejected with a clear error.

### Backups/exports
- Documentation note only: database and full-data exports from **Settings** are
  admin-only and **unencrypted** — download and store them over a trusted
  channel.
