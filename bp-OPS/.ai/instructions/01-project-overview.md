# Project Overview — [PROJECT NAME]

## What This Is

[One paragraph describing what this infrastructure repo manages — which services, which environment, and who depends on it.]

---

## Deployed Services

| Service | Image | Purpose | Traefik Route |
|---|---|---|---|
| [service-name] | [image:tag] | [what it does] | [hostname or path] |

[Document every service here. This is the authoritative service inventory.]

---

## Network Layout

```
Internet
  → Traefik (proxy network)
      → [service-a] (proxy + internal)
      → [service-b] (proxy only)

Internal network: [network name]
  → [service-c] (no external exposure)
  → [service-d]
```

| Network | Type | Purpose |
|---|---|---|
| `proxy` | External (shared) | Traefik-routed public services |
| `internal` | Internal bridge | Backend-only, no external exposure |

---

## CI/CD Overview

| Pipeline | Trigger | What it does |
|---|---|---|
| [pipeline name] | [push/tag/schedule] | [brief description] |

Runner: [self-hosted / GitHub-hosted] — `[runner label]`

---

## Secrets & Config

| Secret/Config | Where it lives | How it's injected |
|---|---|---|
| [secret name] | `.env` (gitignored) | Docker env_file |
| [secret name] | GitHub Actions secret | `${{ secrets.NAME }}` |

All secrets are gitignored. Only `.env.example` (with placeholder values) is committed.

---

## Repository Layout

```
[repo-root]/
├── docker-compose.yml          # Production service definitions
├── docker-compose.dev.yml      # Dev overlay (volume mounts, dev Traefik labels)
├── .env.example                # All required env vars with placeholder values
├── .github/
│   └── workflows/
│       └── [pipeline].yml      # CI/CD pipeline definitions
└── [service]/                  # Per-service config files if needed
```
