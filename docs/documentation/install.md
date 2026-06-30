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

<p class="lead">Docker builds the image, generates a session key, and keeps your data in a volume. This is the recommended way to run CatalogueCanvas.</p>

### Before you start

- **Docker + Docker Compose** installed and running.
- The CatalogueCanvas source, which ships the `docker-compose.yml`:

    ```bash
    git clone https://github.com/ToroRojo-code/CatalogueCanvas.git
    cd CatalogueCanvas
    ```

### Steps

<ol class="steps" markdown>
<li markdown>
Start the app, passing an admin password. The first run builds the image, so it takes a little longer:

```bash
CC_ADMIN_PASSWORD=yourpassword docker compose up --build
```

</li>
<li markdown>
Wait for the log line that says the server is listening on port `8000`.
</li>
<li markdown>
Open `http://localhost:8000` and log in with the password you set in `CC_ADMIN_PASSWORD`.
</li>
</ol>

To run it in the background instead, add `-d`:

```bash
CC_ADMIN_PASSWORD=yourpassword docker compose up --build -d
```

Stop it with `docker compose down` (your data volume is kept).

### Using a `.env` file

Setting more than one variable on the command line gets awkward. Docker Compose reads a file
named `.env` in the same directory as `docker-compose.yml`, so you can keep your configuration
there instead.

<ol class="steps" markdown>
<li markdown>
Create a `.env` file next to `docker-compose.yml`:

```ini
# .env — CatalogueCanvas configuration
CC_ADMIN_PASSWORD=yourpassword
CC_PORT=8000
CC_SITE_TITLE=My Catalogue
CC_SITE_AUTHOR=Your Name
```

Add any other variables from [Configuration](#configuration) as `KEY=value` lines.
</li>
<li markdown>
Start the app. Compose reads `.env`, so you do not repeat the variables:

```bash
docker compose up --build
```

</li>
</ol>

!!! warning "Keep `.env` out of version control"

    The file holds your admin password. Add `.env` to your `.gitignore` so it is never committed.

!!! note "Editors"

    Confirm Compose interpolation against the shipped `docker-compose.yml` — whether each `CC_*`
    variable is referenced as `${CC_...}` (so `.env` flows through to the container) or passed via
    `env_file:`. Verify before publishing.

### What happens on first start

??? question "Where does my data live?"
    All data — the SQLite database and every uploaded asset — persists in the `cc-data` Docker
    volume, mounted at `/data` inside the container. Removing the container does not delete it;
    `docker compose down -v` does. To keep data in a directory you control instead, see
    [Home-server install](#home-server-install).

??? question "Do I need to generate a secret key?"
    No. The **session signing key is generated automatically** on first start and saved to
    `/data/cc_secret_key.txt` in the data volume, then reused on every restart. There is no
    manual key-generation or `secrets/` step. Set `CC_SECRET_KEY` or `CC_SECRET_KEY_FILE` only
    if you want to supply your own.

??? question "Which port does it use, and how do I change it?"
    The container always listens on `8000`. The host port is mapped from `CC_PORT` (default
    `8000`). To serve on, say, port `9000`:

    ```bash
    CC_ADMIN_PASSWORD=yourpassword CC_PORT=9000 docker compose up --build
    ```

??? question "How do I set the site title or author?"
    Pass them as environment variables on the same command:

    ```bash
    CC_ADMIN_PASSWORD=yourpassword \
    CC_SITE_TITLE="My Catalogue" \
    CC_SITE_AUTHOR="Your Name" \
    docker compose up --build
    ```

    See [Configuration](#configuration) for the full list of variables.

!!! warning "Login fails silently?"

    If you cannot log in, check that `CC_ADMIN_PASSWORD` was set on the command that started the
    container — it is required. See [Troubleshooting](#troubleshooting) for other causes.

## Configuration

| Variable | Default | Description |
|---|---|---|
| `CC_ADMIN_PASSWORD` | _(empty)_ | Admin login password — required to log in |
| `CC_PORT` | `8000` | Host port mapped to the container (the container always listens on 8000) |
| `CC_SECRET_KEY_FILE` | `<CC_DATA_DIR>/cc_secret_key.txt` | Path to the session signing key. Auto-generated and persisted on first start; only set this to override the location |
| `CC_SECRET_KEY` | _(unset)_ | Session signing key as an env var — optional override; if neither this nor the key file is set, a key is generated automatically |
| `CC_SITE_TITLE` | `My Catalogue` | Title shown in the UI and on public portfolios |
| `CC_SITE_AUTHOR` | _(empty)_ | Author/owner name shown on public portfolios |
| `CC_DATA_DIR` | `/data` | Base directory for the database and storage |
| `CC_DB_PATH` | `<CC_DATA_DIR>/catalogue.db` | SQLite database file path |
| `CC_STORAGE_DIR` | `<CC_DATA_DIR>/storage` | Directory for uploaded item assets |
| `CC_STATIC_DIR` | `web/dist` | Directory of built frontend assets to serve |
| `CC_LLM_ALLOWED_HOSTS` | _(unset)_ | Comma-separated hostnames/IPs the Describe feature may call. Unset = no restriction. Set to protect against SSRF (e.g. `ollama.lan,192.168.1.50`) |

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

## Home-server install

<p class="lead">Running CatalogueCanvas on a machine you keep on your home network: a NAS, a mini-PC, or a Raspberry Pi.</p>

This is the same Docker path as the quick start, set up for a box that stays on rather than your laptop.

<ol class="steps" markdown>
<li markdown>
Pick a directory on the host for persistent data and point the data volume at it, so your database and assets live outside the container:

```bash
mkdir -p /srv/cataloguecanvas/data
```

</li>
<li markdown>
Start it with the data directory bind-mounted and a restart policy so it comes back after a reboot:

```bash
CC_ADMIN_PASSWORD=yourpassword docker compose up -d
```

<!-- TODO Editors: confirm the compose file exposes a restart policy (e.g. `restart: unless-stopped`) and a bind mount for /srv/cataloguecanvas/data. Document the exact compose override here. -->
</li>
<li markdown>Reach it from another device on the LAN at `http://<server-ip>:8000`.</li>
</ol>

!!! note "Editors"

    TODO, reconcile against the app before publishing: exposing the service on a LAN, putting it behind a reverse proxy (Caddy / Nginx / Traefik) for a hostname and TLS, and any ARM/Raspberry Pi image notes. **Do not** recommend exposing this to the public internet. The FAQ already warns against it.

## Advanced: bare-metal install (no Docker)

<p class="lead">Running the backend and frontend directly on the host, without containers. You manage the process and its environment yourself.</p>

!!! warning

    This path is unsupported. Docker is the recommended deployment. You manage the process manager, reverse proxy, and updates yourself.

Pick your operating system below. Each block lists the prerequisites and the commands to take a clean machine to a built app. The shared run and upgrade steps follow underneath.

<!-- TEMPLATE: one collapsible block per OS. `???` = collapsed. Indent contents four spaces.
     Verify every package name and command against the app before publishing. -->

??? question "Linux — Debian / Ubuntu (`apt`)"
    **Prerequisites**

    - Python 3.11+
    - [`uv`](https://docs.astral.sh/uv/)
    - Node.js 22+
    - `git`

    **Step by step**

    ```bash
    # 1. System packages
    sudo apt update
    sudo apt install -y python3 python3-venv git curl

    # 2. uv (backend package manager)
    curl -LsSf https://astral.sh/uv/install.sh | sh

    # 3. Node.js 22+ (NodeSource)
    curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
    sudo apt install -y nodejs

    # 4. Get the source
    git clone https://github.com/ToroRojo-code/CatalogueCanvas.git
    cd CatalogueCanvas

    # 5. Build frontend + install backend
    (cd web && npm ci && npm run build)
    (cd server && uv sync)
    ```

??? question "Linux — Fedora / RHEL / Rocky / Alma (`dnf`)"
    **Prerequisites**

    - Python 3.11+
    - [`uv`](https://docs.astral.sh/uv/)
    - Node.js 22+
    - `git`

    **Step by step**

    ```bash
    # 1. System packages
    sudo dnf install -y python3 git curl

    # 2. uv (backend package manager)
    curl -LsSf https://astral.sh/uv/install.sh | sh

    # 3. Node.js 22+ (NodeSource)
    curl -fsSL https://rpm.nodesource.com/setup_22.x | sudo -E bash -
    sudo dnf install -y nodejs

    # 4. Get the source
    git clone https://github.com/ToroRojo-code/CatalogueCanvas.git
    cd CatalogueCanvas

    # 5. Build frontend + install backend
    (cd web && npm ci && npm run build)
    (cd server && uv sync)
    ```

??? question "macOS (Homebrew)"
    **Prerequisites**

    - [Homebrew](https://brew.sh/)
    - Python 3.11+
    - [`uv`](https://docs.astral.sh/uv/)
    - Node.js 22+
    - `git`

    **Step by step**

    ```bash
    # 1. Toolchain via Homebrew
    brew install python@3.11 node@22 git uv

    # 2. Get the source
    git clone https://github.com/ToroRojo-code/CatalogueCanvas.git
    cd CatalogueCanvas

    # 3. Build frontend + install backend
    (cd web && npm ci && npm run build)
    (cd server && uv sync)
    ```

??? question "Windows (no Docker, no WSL)"
    Tested end to end on Windows 11. The Docker entrypoint does not run here, so you set a few environment variables by hand that Docker would otherwise set for you (see **Run it** below).

    **Prerequisites** (install once via `winget`)

    - [winget](https://learn.microsoft.com/windows/package-manager/winget/) (ships with Windows 11)
    - Node.js LTS to build the frontend
    - [`uv`](https://docs.astral.sh/uv/) to manage Python and the backend dependencies. It downloads its own CPython, so you do not need a separate Python install.
    - `git`, or download a source release ZIP instead

    **Step by step**

    ```bat
    :: 1. Toolchain via winget
    winget install OpenJS.NodeJS.LTS astral-sh.uv Git.Git

    :: 2. Get the source (open a fresh terminal so PATH updates)
    git clone https://github.com/ToroRojo-code/CatalogueCanvas.git
    cd CatalogueCanvas

    :: 3. Build the frontend
    cd web
    npm ci
    npm run build

    :: 4. Sync backend dependencies
    cd ..\server
    uv sync
    ```

    !!! warning "SVG previews need Cairo"

        On a stock Windows machine, ingesting an item whose preview is an **SVG** fails with HTTP 500 until the native **Cairo** library is installed. Raster previews (PNG/JPEG/TIFF), browsing, and login are unaffected.

        On **x64**, get Cairo from the [Cairo download page](https://cairographics.org/download/). The Windows binaries come with the GTK runtime: install an x64 GTK runtime, or grab the cairo, zlib, and libpng run-time archives directly and put `libcairo-2.dll` (with `zlib1.dll` and `libpng`) on `PATH`, then restart the server.

        On **Windows ARM64**, Cairo needs the MSYS2 toolchain instead.
        <!-- TODO Editors: port the "SVG rendering on Windows ARM64" section from inputdoc/cataloguecanvas-features.md if ARM64 support is in scope for the docs. -->

### Run it

Once built, run the backend as a long-lived process. Leave off `--reload`, and set the required environment variables.

On Linux and macOS:

```bash
CC_ADMIN_PASSWORD=yourpassword \
CC_DATA_DIR=/srv/cataloguecanvas/data \
uv run uvicorn cataloguecanvas.main:app --host 0.0.0.0 --port 8000
```

On Windows, set the variables the Docker entrypoint would normally set. A `run-cc.bat` keeps it in one place:

```bat
@echo off
cd /d C:\path\to\CatalogueCanvas\server
set CC_ADMIN_PASSWORD=changeme
set CC_SECRET_KEY=<random-hex>
set CC_DATA_DIR=C:\Users\<you>\cc-data
set CC_COOKIE_SECURE=false
set CC_STATIC_DIR=C:\path\to\CatalogueCanvas\web\dist
uv run uvicorn cataloguecanvas.main:app --host 0.0.0.0 --port 8000
```

Then open `http://localhost:8000`, or `http://<machine-ip>:8000` from another device on the LAN.

!!! danger "Windows over plain HTTP: set `CC_COOKIE_SECURE=false`"

    `CC_COOKIE_SECURE` defaults to `true`. Over plain HTTP (LAN or local testing) a secure cookie does not round-trip and **login silently fails** with no error. Set it to `false` when serving over HTTP. Generate `CC_SECRET_KEY` with `uv run python -c "import secrets; print(secrets.token_hex(32))"`; without it, the bare-metal
    server falls back to an insecure default.

!!! note "Editors"

    TODO, reconcile against the app before publishing: confirm each OS's package names, the minimum versions, and the clone URL; the production process manager (a `systemd` unit or Windows Task Scheduler / NSSM example); running behind a reverse proxy for TLS; and the upgrade procedure (pull, rebuild frontend, `uv sync`, restart). Verify all env vars against `server/src/cataloguecanvas/settings.py`. The cross-platform backslash URL issue noted in the feature handover may also be worth a troubleshooting entry.

## Connecting a local LLM

If CatalogueCanvas runs in Docker and your LLM server (LM Studio, Ollama, …) runs on the host, point the API URL at the host bridge, **not** `localhost`:

```text
http://host.docker.internal:1234
```

`localhost` inside the container refers to the container itself.

The `/v1/chat/completions` path is **appended automatically** — you only need the host and port. A full URL still works if you prefer to supply one.

## Troubleshooting

| Symptom | Likely cause / fix |
|---|---|
| LLM request fails | Endpoint unreachable or misconfigured — the error now reports the actual cause (connection, HTTP error, non-JSON, or no choices returned). Check the API URL; use `host.docker.internal` from Docker |
| Can't log in | `CC_ADMIN_PASSWORD` not set; in multi-user mode every user's password must be unique; or 5 failed attempts triggered the 5-minute rate limit |
| Upload rejected | File isn't a `.zip`, or exceeds the configured max upload size |
