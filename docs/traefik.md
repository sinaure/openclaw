# Exposing OpenClaw Gateway with Traefik

Use Traefik as a reverse proxy to expose the OpenClaw gateway over a domain name with HTTPS (Let's Encrypt).

## Prerequisites

- A domain name pointing to your host (e.g. `gateway.example.com` → your server IP).
- Ports 80 and 443 open on the host.

## Setup

1. **Set environment variables** (in `.env` or export):

   ```bash
   OPENCLAW_DOMAIN=gateway.example.com
   TRAEFIK_ACME_EMAIL=you@example.com
   ```

2. **Start with the Traefik profile:**

   ```bash
   docker compose --profile traefik up -d
   ```

   This starts:

   - `openclaw-gateway` and `openclaw-cli` (as usual)
   - `traefik` listening on 80 (HTTP) and 443 (HTTPS)

3. **Routing**

   - HTTP (port 80) requests to `OPENCLAW_DOMAIN` are redirected to HTTPS.
   - HTTPS (port 443) is served with a Let's Encrypt certificate and forwards to the gateway on port 18789.

4. **Access**

   - Open `https://gateway.example.com` (or your `OPENCLAW_DOMAIN`) and use the Control UI.
   - The gateway token is unchanged; use it in Settings as before.

## Without a real domain (e.g. local)

If you only want to try Traefik locally:

- Leave `OPENCLAW_DOMAIN` unset (default `openclaw.localhost`) or set it to a hostname you resolve locally (e.g. via `/etc/hosts`).
- For HTTPS with Let's Encrypt you must set `OPENCLAW_DOMAIN` and `TRAEFIK_ACME_EMAIL` to a valid domain and email; otherwise Traefik may log certificate errors.

## Traefik dashboard

Traefik is started with the API dashboard. To expose it (optional), add a router and service in Traefik’s config or via labels; by default it is not exposed externally.

## Stopping Traefik

```bash
docker compose --profile traefik down
```

To stop only Traefik and keep the gateway:

```bash
docker compose stop traefik
```
