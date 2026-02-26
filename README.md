
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

1. Review the compose file and port mappings in [docker-compose.yml](docker-compose.yml).
2. Create any required directories and set appropriate permissions (if not already present):

```bash
mkdir -p data mysql seafile seahub-data logs
sudo chown -R 1000:1000 data seafile seahub-data mysql || true
```

3. Start the stack:

```bash
docker compose pull
docker compose up -d
```

5. Open the web interface using the host and port mapped in [docker-compose.yml](docker-compose.yml). If unsure, inspect the compose ports with `docker compose ps`.

## Configuration

- Environment variables: The compose file may read environment variables (or an `.env` file) for things like passwords, hostname and ports. Inspect [docker-compose.yml](docker-compose.yml) and create an `.env` next to it if desired.
- Database credentials and Seafile admin account are commonly set via env vars. If the compose uses configured secrets, set them accordingly.

If you change ports, update any firewall/NAT rules or reverse-proxy configuration.

## Persistent data and volumes

This stack relies on host-mounted directories to persist data across container restarts. Typical directories in this repo:

- `data/` — Seafile storage and attachments
- `mysql/` — MySQL data files
- `seafile/`, `seahub-data/` — application-specific persistent data
- `logs/` — container or application logs (if mapped)

Do not remove these directories unless you intend to permanently delete all Seafile data.

## Backups

Recommended minimal backup strategy:

1. Stop the containers that write to storage:

```bash
docker compose stop
```

2. Archive the persistent directories (example):

```bash
tar -czvf seafile-backup-$(date +%F).tar.gz data mysql seafile seahub-data
```

3. For MySQL logical backup (preferable for portability):

```bash
docker compose exec -T db mysqldump -u<user> -p<password> --databases seafile_db > seafile_db.sql
```

Restore basic workflow:

1. Stop the stack: `docker compose down`
2. Extract the archive back into the repo root (or the configured volume paths).
3. Import SQL dump into the database container (if using logical backup):

```bash
docker compose up -d db
docker compose exec -T db mysql -u<user> -p<password> seafile_db < seafile_db.sql
```

Adjust service names (`db`) to match the ones in your `docker-compose.yml`.

## Logs & troubleshooting

- View container logs:

```bash
docker compose logs -f
```

- If Seafile or the web UI won't start:
	- Check the database container is healthy and accessible.
	- Verify that file permissions allow the container user to read/write the mounted volumes.
	- Confirm ports are not blocked by a firewall or already in use.

- If there are migrations or startup errors, check `seahub` and `seafile` container logs specifically:

```bash
docker compose logs -f seahub
docker compose logs -f seafile
```

## Useful links

- Official Seafile Docker images and documentation: https://www.seafile.com/en/home/
- Seafile server manual and admin guide: https://manual.seafile.com/

