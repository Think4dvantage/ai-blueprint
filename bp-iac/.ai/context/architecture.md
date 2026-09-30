# Architecture Reference

## Deployed Services

| Service | Image | Networks | Traefik Route | Health |
|---|---|---|---|---|
| [service-name] | [image:tag] | proxy, internal | [hostname] | [/health or N/A] |

[Document every service here. This is the authoritative service inventory.]

---

## Network Map

```
Internet
  → Traefik (proxy network — external)
      → [list Traefik-routed services]

Internal network: [network-name]
  → [list internal-only services]
```

---

## Volume Inventory

| Volume | Service | Purpose |
|---|---|---|
| [volume-name] | [service] | [what's stored] |

---

## CI/CD Pipeline Inventory

| Pipeline file | Trigger | Runner | Stages |
|---|---|---|---|
| [workflow].yml | [push to main / tag / cron] | [self-hosted, lg4] | validate → build → deploy |

---

## Environment Variables

| Variable | Service | Description |
|---|---|---|
| [VAR_NAME] | [service] | [what it controls] |

[Document every variable that appears in `.env.example`. Never document actual values.]

---

## Deployment

### Traefik Label Format

This homelab requires **list format** labels, not map format:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myapp.rule=Host(`myapp.lg4.ch`)"
  - "traefik.http.routers.myapp.entrypoints=websecure"
  - "traefik.http.routers.myapp.tls.certresolver=letsencrypt"
  - "traefik.http.services.myapp.loadbalancer.server.port=8000"
```

When a container is on multiple Docker networks, add `traefik.docker.network=proxy`.

### Healthcheck

`python:3.11-slim` does not include `curl`. Use Python stdlib:

```yaml
healthcheck:
  test: ["CMD-SHELL", "python -c \"import urllib.request; urllib.request.urlopen('http://localhost:8000/')\""]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 10s
```

### Dev Overlay

`docker-compose.dev.yml` extends the base with:
- Live source volume mounts (`:ro,z`)
- `proxy` external network + Traefik labels for `[service]-dev.lg4.ch`
- Dev-only environment variables
