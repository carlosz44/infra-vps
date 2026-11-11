# infra-vps

Host-level configuration for Carlos's VPS (Ubuntu 24.04, 2 GB RAM, 2 cores). The VPS runs two apps:

- **baby-tracker** (`carlosz44/baby-tracker`): Django, at `/srv/baby-tracker`
- **Daily Shed** (`carlosz44/guitar-tracker`): Node, at `/srv/guitar-tracker`

Adds to `~/Dev/CLAUDE.md` and never contradicts it.

## What lives here

- **Edge stack** at `/srv/edge`: the one Caddy that terminates HTTPS for every app and routes by hostname over the external Docker network `edge`.
- **Provision VPS workflow:** idempotent host setup (Docker, UFW, fail2ban, `deploy` user, `edge` network, Docker log defaults, logrotate).
- **Deploy workflow:** pushes edge changes and reloads Caddy.

## What doesn't live here

Each app's compose file, image, secrets and deploy workflow stay in that app's repo. Apps attach their web container to `edge` with a network alias. Databases never join `edge`.

## Before any work

Read the current spec in `~/Dev/private-docs/infra-vps/` (today `001-shared-edge/`) and `~/Dev/private-docs/infra-vps/2026-09-30-vps-audit.md` for the server's state. Neither lives in this repo. Work follows the flow in `~/Dev/CLAUDE.md`.

## Guardrails

- The live server is only changed through the workflows or through manual steps Carlos has approved in the plan. Say which containers restart before doing anything that restarts them.
- Never print secrets:
  - don't read `.env` files
  - don't run `docker inspect` or `docker compose config`, since both show environment variables
- Every container gets a `mem_limit` and json-file logging with `max-size: 10m`, `max-file: 3`.
- Only ports 22, 80 and 443 are open to the internet.
- Root, or `admin` with sudo, is only for provisioning and reboots. Everything else runs as `deploy`, which has no sudo rights.
- The legacy host stack (nginx, certbot, host Postgres) was removed on 2026-09-30. Don't reintroduce host-level web servers or databases.
- **This repo is public.** Never commit IPs, hostnames, keys or `.env` files. Hostnames only come in through secrets. Audits live in `~/Dev/private-docs`, never here. Workflows run only on push to `main` or manual dispatch, never on `pull_request_target`, so forks never see secrets.
