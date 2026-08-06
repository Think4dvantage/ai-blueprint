# Constraints — What NOT to Do

> Generic, blueprint-owned patterns. This project's own fixed-bug history and hard-rule instances
> live in `context/constraints-notes.md` — read both.

## AI Files

**All AI-related content lives exclusively in `.ai/`.** Never create tool-specific instruction files such as `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`, or any equivalent — not even as thin pointers. Instructions, context, prompts, and plans all go in `.ai/` and nowhere else.

---

## Production

**Never touch prod directly.** All production changes go through the IaC repo. No direct SSH, no direct `docker-compose` on the prod host.

---

## Frontend

**Never add npm or a build step.** The frontend is intentionally dependency-free. No webpack, vite, rollup, parcel, or any bundler. No `package.json`.

---

## Secrets

**Never commit secrets.** `config.yml` and `.env` are gitignored. Only `config.yml.example` (with placeholder values) is committed.

---

## Database Migrations

**No Alembic. No `.sql` migration files. No `_migrations` table.**

All schema changes use raw `ALTER TABLE` statements guarded by `PRAGMA table_info()` inside `_run_column_migrations()` in `database/db.py`. `Base.metadata.create_all()` handles the initial schema at startup — it is idempotent. See `02-backend-conventions.md` for the exact pattern.

---

## i18n

**Never hardcode user-visible strings in JS** without a corresponding key in all locale files. All locales must be updated simultaneously.

---

## Code Quality

- Don't add features, refactor code, or make "improvements" beyond what was asked.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen.
- Don't create helpers or abstractions for one-time operations.
- Don't design for hypothetical future requirements.
- Don't add docstrings, comments, or type annotations to code you didn't change.
- Don't use feature flags or backwards-compatibility shims when you can just change the code.

---

## Security

**Never interpolate a user-supplied value directly into a query string** (SQL, Flux, or any
query language built by string formatting). Validate with an allowlist regex first, then
interpolate the validated value:

```python
# WRONG — injection
query = f'|> filter(fn: (r) => r.station_id == "{station_id}")'

# RIGHT — validate first with an allowlist, then interpolate a known-safe value
if not re.match(r'^[\w\-]{1,64}$', station_id):
    raise HTTPException(status_code=404)
query = f'|> filter(fn: (r) => r.station_id == "{station_id}")'
```

**The app must refuse to start if a secret (JWT signing key, API key) is empty, too short, or a
known placeholder value.** Fail closed at startup — never fall back to a default secret in any
deployed environment.

**Never assign untrusted data to `element.innerHTML`, `element.outerHTML`, or
`document.write()`** in frontend JS. Use `element.textContent` for plain text. If markup must be
rendered, sanitize it first against a known-safe allowlist of tags/attributes — never trust it raw.

**Never put an access or refresh token in a URL** — query param, hash fragment, or redirect target.
URLs get logged (proxies, browser history, referrer headers). Tokens belong in `localStorage` or
an HttpOnly cookie, set via a POST response body, never via a redirect URL.

**Never silence an exception in a background task, scheduler job, or async callback.** A swallowed
exception makes a job stop doing its work with no visible signal:

```python
# WRONG
try:
    await do_thing()
except Exception:
    pass

# RIGHT — log with full traceback, then re-raise or let it propagate
try:
    await do_thing()
except Exception:
    logger.exception("do_thing failed")
    raise
```

**Every module-level cache dict must have a maximum size.** An unbounded cache eventually OOMs
the process. Bound it and evict (LRU or oldest-first) when full.

---

## Dependencies

**Never let the Dockerfile's lockfile `COPY` fall back silently to a fresh resolve.** Use
`COPY pyproject.toml poetry.lock ./` (the literal filename), never a glob like `poetry.lock*` that
succeeds even when the file is missing. A missing lock must fail the build loudly — silently
re-resolving lets dependency versions drift under a fixed image tag.

---

## Architecture

- No Alembic — schema migrations are done with raw `ALTER TABLE` in `_run_column_migrations()`.
- No print statements in production code — use the standard `logging` module.
- Never read `os.environ` directly — always go through `get_config()`.
- Never put all routes in `main.py` — one router per domain.
- Never call blocking synchronous I/O inside `async def` without `asyncio.to_thread()`.
