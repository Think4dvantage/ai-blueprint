# Prompt: Debug an Infrastructure Problem

> **Approach**: Reproduce → Isolate → Fix → Verify. Never change config to mask a symptom.

---

## Step 1 — Reproduce and Observe

Before touching any config:

1. **Container status**: `docker-compose ps` — which containers are unhealthy or exited?
2. **Logs**: `docker-compose logs --tail=100 [service]` — what's the last error?
3. **Health**: `docker inspect [container] --format='{{.State.Health}}'` — what's the health check returning?
4. **Traefik**: Is the router visible in the Traefik dashboard? Is the backend healthy?

Write down the exact error before proposing any fix.

---

## Step 2 — Isolate the Failure Layer

| Symptom | Likely layer | Check |
|---|---|---|
| Container exits immediately | App crash / missing env var | `docker logs [container]` — look for startup error |
| Container runs but health fails | App not ready / wrong port | Check healthcheck command and port |
| Container healthy but Traefik 404 | Traefik label / network issue | Check labels, `traefik.docker.network`, `proxy` network membership |
| TLS error | Cert not issued | Check Traefik cert resolver logs |
| Pipeline fails | Config error / secret missing | Check Actions run log, step by step |

---

## Step 3 — Form a Hypothesis

State the hypothesis explicitly before making changes:

> "The service can't start because `DATABASE_URL` is missing from `.env`."

Never make multiple changes at once. One hypothesis → one change → verify.

---

## Step 4 — Fix

Make the minimal change that addresses the root cause:

- **Missing env var** → add to `.env` (and `.env.example` if it's new)
- **Wrong port in Traefik label** → correct the label, redeploy the service only
- **Image tag changed API** → pin to the last working tag, then plan the upgrade separately
- **Pipeline secret missing** → add secret to GitHub repo, re-run the workflow

---

## Step 5 — Verify

After the fix:

1. `docker-compose up -d [service]` and wait for health to pass
2. Confirm the original symptom is gone
3. Check that no other service was affected by the change

---

## Step 6 — Document

If the fix revealed a missing `.env.example` entry, missing healthcheck, or undocumented secret → fix those too before closing. A silent gap that caused this bug will cause it again.
