
# SeaFile-Pi5 — Seafile Docker stack for Raspberry Pi / ARM
This repository contains a Docker-based Seafile stack tailored for a Raspberry Pi (Pi5) / ARM environment. The compose setup and are included so you can run Seafile, which will generate its database, and supporting services as containers.

## Overview

- Purpose: Run Seafile in Docker for a small self-hosted file sync server.
- Layout: see [docker-compose.yml](docker-compose.yml) at the repo root.

## Prerequisites

- A host running Linux (this repo was prepared for Raspberry Pi 5 / ARM).
- Docker Engine and Docker Compose (v2) installed. On many systems `docker` and `docker compose` are available.
- Sufficient disk space for user data (the `data/`, `mysql/`, and `seahub-data/` folders).

If you need to install Docker and Compose on a Debian/Ubuntu/Raspbian host, follow Docker's official instructions.

## Quick start
1. Clone the git repository

2. Create a .env file in the same directory

2. Start the stack:

```bash
docker compose pull
docker compose up -d
```

5. Open the web interface using the host and port mapped in [docker-compose.yml](docker-compose.yml). If unsure, inspect the compose ports with `docker compose ps`.

## Configuration

- Environment variables: The compose file may read environment variables (or an `.env` file) for things like passwords, hostname and ports. Inspect [docker-compose.yml](docker-compose.yml) and create an `.env` next to it if desired.
- Database credentials and Seafile admin account are commonly set via env vars. If the compose uses configured secrets, set them accordingly.

## Persistent data and volumes

This stack relies on host-mounted directories to persist data across container restarts. Typical directories in this repo:

- `data/` — Seafile storage and attachments
- `mysql/` — MySQL data files
- `seafile/`, `seahub-data/` — application-specific persistent data
- `logs/` — container or application logs (if mapped)

Do not remove these directories unless you intend to permanently delete all Seafile data.

## Recommendation

Seafile works well with TailScale, if you have set up your Pi with TailScale you would be able to access SeaFile from all locations.
- TailScale website: https://tailscale.com/

## Useful links

- Official Seafile Docker images and documentation: https://www.seafile.com/en/home/
- Seafile server manual and admin guide: https://manual.seafile.com/

