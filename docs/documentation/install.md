---
title: Install
---

<p class="kicker">Documentation</p>

# Install

<p class="lead">How to deploy and run CatalogueCanvas. Docker is the recommended path.</p>

## Requirements

- **Docker + Docker Compose** (recommended).
- For local development without Docker:
    - Python 3.11+ and [`uv`](https://docs.astral.sh/uv/) (backend)
    - Node.js 22+ (frontend)

### Footprint

- Docker image: ~436 MB
- RAM: ~256 MB for light use
- Disk: scales with uploaded assets — size the data volume accordingly
- CPU: a single core is sufficient

## Quick start (Docker)

<ol class="steps" markdown>
<li markdown>
Start the app with an admin password:

```bash
CC_ADMIN_PASSWORD=yourpassword docker compose up --build
```
</li>
<li markdown>Open `http://localhost:8000` and log in with `CC_ADMIN_PASSWORD`.</li>
</ol>

The **session signing key is generated automatically** on first start and saved to
`/data/cc_secret_key.txt` in the data volume, then reused on every restart — there's no
manual key-generation or `secrets/` step.

All data (SQLite database + uploaded assets) persists in the `cc-data` Docker volume under `/data`.

## Configuration

| Variable | Default | Description |
|---|---|---|
| `CC_ADMIN_PASSWORD` | _(empty)_ | Admin login password — required to log in |
| `CC_SECRET_KEY_FILE` | `<CC_DATA_DIR>/cc_secret_key.txt` | Path to the session signing key. Auto-generated and persisted on first start; only set this to override the location |
| `CC_SECRET_KEY` | _(unset)_ | Session signing key as an env var — optional override; if neither this nor the key file is set, a key is generated automatically |
| `CC_SITE_TITLE` | `My Catalogue` | Title shown in the UI and on public portfolios |
| `CC_SITE_AUTHOR` | _(empty)_ | Author/owner name shown on public portfolios |
| `CC_DATA_DIR` | `/data` | Base directory for the database and storage |
| `CC_DB_PATH` | `<CC_DATA_DIR>/catalogue.db` | SQLite database file path |
| `CC_STORAGE_DIR` | `<CC_DATA_DIR>/storage` | Directory for uploaded item assets |
| `CC_STATIC_DIR` | `web/dist` | Directory of built frontend assets to serve |

!!! note "Editors"

    Verify this table against `server/src/cataloguecanvas/settings.py` before publishing.

## Local development (without Docker)

**Backend:**

```bash
cd server
uv sync
uv run uvicorn cataloguecanvas.main:app --reload
```

**Frontend:**

```bash
cd web
npm ci
npm run dev
```

The Vite dev server proxies API requests to the backend (see `web/vite.config.ts`).

## Connecting a local LLM

If CatalogueCanvas runs in Docker and your LLM server (LM Studio, Ollama, …) runs on the host,
point the API URL at the host bridge, **not** `localhost`:

```
http://host.docker.internal:1234
```

`localhost` inside the container refers to the container itself.

The `/v1/chat/completions` path is **appended automatically** — you only need the host and
port. A full URL still works if you prefer to supply one.

## Troubleshooting

| Symptom | Likely cause / fix |
|---|---|
| LLM request fails | Endpoint unreachable or misconfigured — the error now reports the actual cause (connection, HTTP error, non-JSON, or no choices returned). Check the API URL; use `host.docker.internal` from Docker |
| Can't log in | `CC_ADMIN_PASSWORD` not set; in multi-user mode the admin and reader passwords must differ; or 5 failed attempts triggered the 5-minute rate limit |
| Upload rejected | File isn't a `.zip`, or exceeds the configured max upload size |
