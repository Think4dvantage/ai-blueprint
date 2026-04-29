# Prompt: Add a New Docker Compose Service

Use this checklist when adding a new service to the infrastructure.

---

## Pre-flight

- [ ] Read `context/architecture.md` — confirm the service isn't already defined
- [ ] Confirm the image tag to use (never `:latest`)
- [ ] Confirm which networks the service needs (`proxy` for Traefik routing, `internal` for backend access)
- [ ] Confirm the internal port the service listens on

---

## Service Definition (`docker-compose.yml`)

- [ ] Add service block with `image`, `container_name`, `restart: unless-stopped`
- [ ] Add `env_file: - .env` (or inline non-secret env vars)
- [ ] Add `healthcheck` (Python stdlib — no curl)
- [ ] Add `networks` — only the ones actually needed
- [ ] Add named volumes if the service needs persistent data (declare in `volumes:` section)
- [ ] If Traefik-routed: add Traefik labels in **list format** (see `02-infra-conventions.md`)
- [ ] If logging should be capped: add `logging` driver config with `max-size` and `max-file`

## Dev Overlay (`docker-compose.dev.yml`)

- [ ] Add service override with dev-specific Traefik route (`[service]-dev.lg4.ch`)
- [ ] Add source volume mount if relevant (`:ro,z`)
- [ ] Add dev-only environment variables if needed

## Secrets & Config

- [ ] Add all new environment variables to `.env.example` with placeholder values and comments
- [ ] Verify no secrets appear in `docker-compose.yml` or any committed file
- [ ] Add new GitHub Actions secrets if the pipeline needs them (document in `context/architecture.md`)

## Documentation

- [ ] Add the service to `context/architecture.md` (service table, network map, volume inventory)
- [ ] Update `README.md` services section

## Verify

- [ ] `docker-compose config` validates without errors
- [ ] `docker-compose up -d [service-name]` starts the service
- [ ] Health check passes: `docker inspect [container-name] --format='{{.State.Health.Status}}'`
- [ ] Traefik route responds (if applicable): `curl -I https://[hostname]`
