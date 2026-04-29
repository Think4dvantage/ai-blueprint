# Operability Conventions

Operability is goal #1. An operator who has never read the config files should be able to determine the health of every service from container status, logs, and the health endpoint alone.

---

## Container Health

Every service must define a `healthcheck` in `docker-compose.yml`. Health checks must:

- Test the actual service, not just that the process is running
- Use Python stdlib (no `curl` — not available in `python:3.11-slim`)
- Have a `start_period` that accounts for slow startup (10s minimum)

```yaml
healthcheck:
  test: ["CMD-SHELL", "python -c \"import urllib.request; urllib.request.urlopen('http://localhost:8000/health')\""]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 10s
```

Services without a `/health` endpoint can use a TCP check:

```yaml
healthcheck:
  test: ["CMD-SHELL", "python -c \"import socket; s=socket.socket(); s.connect(('localhost', 5432)); s.close()\""]
```

---

## Health Endpoint (for services you control)

Every service you build must expose a `GET /health` endpoint that:

- Returns `200 OK` with JSON when all critical subsystems are up
- Returns `503 Service Unavailable` with descriptive JSON when degraded
- Is callable without authentication
- Includes: service name, version, uptime, and status of each critical dependency

```json
{
  "status": "ok",
  "service": "my-service",
  "version": "1.2.0",
  "uptime_seconds": 3621,
  "checks": {
    "database": "ok",
    "external-api": "ok"
  }
}
```

---

## Logging Strategy

### Container Logs

Configure Docker logging driver for all services. Use `json-file` with rotation:

```yaml
logging:
  driver: json-file
  options:
    max-size: "10m"
    max-file: "3"
```

Never let container logs grow unbounded — disk exhaustion kills the whole host.

### Service Logs

For services you build: see `dev-web` blueprint's operability doctrine. The same structured logging rules apply: Python `logging` module, named loggers per module, startup sequence logged at `INFO`, requests logged with method + path + status + latency.

### Pipeline Logs

GitHub Actions workflow steps must have descriptive `name` fields — they become the log headers in the Actions UI. Avoid:

```yaml
- run: docker-compose up -d   # Bad: no name
```

Prefer:

```yaml
- name: Deploy services
  run: docker-compose up -d --remove-orphans
```

---

## Monitoring Posture

For each deployed service, know the answer to these three questions:

1. **Is it running?** — `docker ps` / container health status
2. **Is it healthy?** — `/health` endpoint response
3. **What did it last do?** — container logs

If any of these three are not answerable without SSH-ing into the host and running manual commands, the service is not operable. Fix it.

---

## Config Transparency

- All tunable values (ports, domains, intervals, resource limits) must be in `.env`, not hardcoded in `docker-compose.yml`.
- `.env.example` must document every variable with a comment explaining what it controls and a sensible default.
- If a required variable is missing at startup, the service must fail fast with a clear error — never silently fall back to a magic default.
