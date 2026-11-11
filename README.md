# infra-vps

Shared Caddy and host setup for the VPS that runs baby-tracker and Daily Shed.

- `edge/`: `compose.yml` and `Caddyfile` for the shared Caddy, deployed to `/srv/edge`
- `host/`: files the provision workflow installs (`daemon.json`, logrotate rules)
- `.github/workflows/provision.yml`: idempotent host setup (manual trigger)
- `.github/workflows/deploy.yml`: deploys `edge/` and reloads Caddy on push to `main`

To add an app:
1. Give its web container the `edge` network with an alias.
2. Add a site block to `edge/Caddyfile`.
3. Add its hostname secret here.
