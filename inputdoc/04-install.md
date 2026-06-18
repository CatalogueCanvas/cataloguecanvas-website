---
title: Install
section: documentation
order: 4
status: template
---

# Install

> How to deploy and run CatalogueCanvas. Docker is the recommended path.

## Requirements

- **Docker + Docker Compose** (recommended).
- For local development without Docker:
  - Python 3.11+ and [`uv`](https://docs.astral.sh/uv/) (backend)
  - Node.js 22+ (frontend)

### Footprint

- Docker image: ~436 MB
- RAM: ~256 MB for light use; more for heavy LLM/ingest use
- Disk: scales with uploaded assets — size the data volume accordingly
- CPU: a single core is sufficient

## Quick start (Docker)

1. Generate a session secret once (stored as a Compose secret file, not an env var):

   ```bash
   mkdir -p secrets
   openssl rand -hex 32 > secrets/cc_secret_key.txt
   ```

2. Start the app with an admin password:

   ```bash
   CC_ADMIN_PASSWORD=mysecretpassword docker compose up --build
   ```

3. Open `http://localhost:8000` and log in with `CC_ADMIN_PASSWORD`.

All data (SQLite database + uploaded assets) persists in the `cc-data` Docker volume under `/data`.

## Configuration

Environment variables (set via `docker-compose.yml` or your shell):

| Variable | Default | Description |
|---|---|---|
| `CC_ADMIN_PASSWORD` | _(empty)_ | Admin login password — required to log in |
| `CC_SECRET_KEY_FILE` | _(unset)_ | Path to a file holding the session signing key (used by Docker secrets); takes precedence over `CC_SECRET_KEY` |
| `CC_SECRET_KEY` | `dev-secret-change-me` | Session signing key — set a random value (local dev only) |
| `CC_SITE_TITLE` | `My Catalogue` | Title shown in the UI and on public portfolios |
| `CC_SITE_AUTHOR` | _(empty)_ | Author/owner name shown on public portfolios |
| `CC_DATA_DIR` | `/data` | Base directory for the database and storage |
| `CC_DB_PATH` | `<CC_DATA_DIR>/catalogue.db` | SQLite database file path |
| `CC_STORAGE_DIR` | `<CC_DATA_DIR>/storage` | Directory for uploaded item assets |
| `CC_STATIC_DIR` | `web/dist` | Directory of built frontend assets to serve |

> _Verify this table against `server/src/cataloguecanvas/settings.py` before publishing._

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
http://host.docker.internal:1234/v1/chat/completions
```

`localhost` inside the container refers to the container itself.

## Updating

<!-- Fill in your real upgrade flow. -->

1. Back up first (Settings → Backup & export, or copy the `cc-data` volume).
2. Pull the new image / source: `TODO`
3. `docker compose up --build -d`

## Troubleshooting

| Symptom | Likely cause / fix |
|---|---|
| `LLM request failed: [Errno 111] Connection refused` | LLM server unreachable — check the API URL; use `host.docker.internal` from Docker |
| Can't log in | `CC_ADMIN_PASSWORD` not set, or 5 failed attempts triggered the 5-minute rate limit |
| Upload rejected | File isn't a `.zip`, or exceeds the configured max upload size |
| _TODO_ | _TODO_ |
