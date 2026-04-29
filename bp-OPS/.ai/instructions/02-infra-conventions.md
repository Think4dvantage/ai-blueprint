# Infrastructure Conventions

## Docker Compose — Service Definition

Every service definition must include:

```yaml
services:
  my-service:
    image: image:tag
    container_name: my-service
    restart: unless-stopped
    networks:
      - proxy          # only if Traefik-routed
      - internal       # only if it needs backend access
    env_file:
      - .env
    healthcheck:
      test: ["CMD-SHELL", "python -c \"import urllib.request; urllib.request.urlopen('http://localhost:PORT/health')\""]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
    labels:
      - "traefik.enable=true"
      # ... Traefik labels (see below)
```

**Rules**:
- Always set `restart: unless-stopped` — never `always` (masks crash loops) or absent.
- Always define a `healthcheck`. `python:3.11-slim` has no `curl` — use Python stdlib.
- Use `container_name` to make logs and `docker ps` readable.
- Pin image tags. Never use `:latest` in `docker-compose.yml`.

---

## Traefik Labels — Format

**Always use list format**, never map format. This homelab's Traefik requires it.

```yaml
# CORRECT — list format
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.my-service.rule=Host(`my-service.lg4.ch`)"
  - "traefik.http.routers.my-service.entrypoints=websecure"
  - "traefik.http.routers.my-service.tls.certresolver=letsencrypt"
  - "traefik.http.services.my-service.loadbalancer.server.port=8000"

# WRONG — map format (do not use)
labels:
  traefik.enable: "true"
```

When a container is on multiple Docker networks, add the network hint so Traefik knows which one to route through:

```yaml
  - "traefik.docker.network=proxy"
```

### Traefik Label Naming

Use the service name as the Traefik router/service identifier — consistent with `container_name`.

| Label | Value |
|---|---|
| `traefik.http.routers.{name}.rule` | `Host(\`{name}.lg4.ch\`)` |
| `traefik.http.routers.{name}.entrypoints` | `websecure` |
| `traefik.http.routers.{name}.tls.certresolver` | `letsencrypt` |
| `traefik.http.services.{name}.loadbalancer.server.port` | container's internal port |

---

## Networks

Declare networks at the bottom of `docker-compose.yml`:

```yaml
networks:
  proxy:
    external: true
  internal:
    driver: bridge
```

- `proxy` is always `external: true` — it is managed by the Traefik container.
- `internal` is created by this compose file — use a descriptive name per project (e.g., `myapp_internal`).

---

## Dev Overlay (`docker-compose.dev.yml`)

Use `docker-compose.dev.yml` to add development-only settings without touching the base file:

```yaml
services:
  my-service:
    volumes:
      - ./src:/app/src:ro,z
    labels:
      - "traefik.http.routers.my-service-dev.rule=Host(`my-service-dev.lg4.ch`)"
    environment:
      - PYTHONPYCACHEPREFIX=/tmp/pycache
```

Start with: `docker-compose -f docker-compose.yml -f docker-compose.dev.yml up -d`

---

## Secrets & Environment Variables

- All secrets live in `.env` (gitignored). Only `.env.example` is committed with placeholder values.
- Reference via `env_file: - .env` in the service definition.
- Never hardcode secrets in `docker-compose.yml`.
- For GitHub Actions: use repository secrets (`${{ secrets.NAME }}`).
- Document every required variable in `.env.example` with a comment explaining it.

---

## Volume Strategy

| Data type | Mount type | Example |
|---|---|---|
| App source (dev only) | Bind mount (read-only) | `./src:/app/src:ro,z` |
| Persistent data | Named volume | `myapp_data:/data` |
| SQLite database | Named volume | `myapp_db:/app/data` |
| Config files | Bind mount | `./config.yml:/app/config.yml:ro` |

Named volumes must be declared at the bottom of `docker-compose.yml`:

```yaml
volumes:
  myapp_data:
  myapp_db:
```

Never use host paths for persistent data — named volumes survive `docker-compose down`.
