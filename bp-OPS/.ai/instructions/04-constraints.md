# Constraints — What NOT to Do

## AI Files

**All AI-related content lives exclusively in `.ai/`.** Never create tool-specific instruction files such as `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`, or any equivalent — not even as thin pointers.

---

## Production

**Never modify production directly.** All production changes go through the CI/CD pipeline. No direct SSH, no manual `docker-compose` on the prod host, no in-place config edits on running containers.

---

## Image Tags

**Never use `:latest` in `docker-compose.yml`.** Always pin to a specific version tag. `latest` makes rollbacks impossible and deployments non-deterministic.

---

## Traefik Labels

**Always use list format for Traefik labels.** Never use map format. The homelab Traefik instance requires list-style labels.

---

## Secrets

**Never commit secrets.** `.env` files are gitignored. Only `.env.example` (with placeholder values and comments) is committed. No secrets in `docker-compose.yml`, no secrets in workflow files (use `${{ secrets.NAME }}`).

---

## Healthchecks

**Never skip healthchecks.** Every service must have a `healthcheck` definition. `python:3.11-slim` has no `curl` — use Python stdlib for HTTP checks.

---

## Networks

**Never expose backend services to the `proxy` network unless they need Traefik routing.** Backend-only services (databases, internal APIs) belong on `internal` only. Mixing networks unnecessarily increases attack surface.

---

## Code Quality

- Don't add services, refactor configs, or make "improvements" beyond what was asked.
- Don't add comments to files you didn't change.
- Don't create abstractions for one-time operations.
- Don't design for hypothetical future requirements.

---

## Application Code

**This repo contains infrastructure definitions only.** No application source code belongs here. If a service needs application code, it lives in a separate repo with its own `dev-web` or `dev-app` blueprint.
