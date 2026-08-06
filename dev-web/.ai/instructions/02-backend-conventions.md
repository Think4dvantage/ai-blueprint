# Backend Conventions

> Generic, blueprint-owned patterns. This project's own auth/role model and backend history live
> in `context/backend-notes.md` — read both.

## New API Router

Create `src/[package]/api/routers/<domain>.py`, register it in `main.py`.

```python
# src/[package]/api/routers/widgets.py
router = APIRouter(prefix="/api/widgets", tags=["widgets"])

@router.get("")
def list_widgets(current_user: User = Depends(get_current_user), db: Session = Depends(get_db)):
    ...
```

```python
# main.py
from [package].api.routers import widgets as widgets_router
app.include_router(widgets_router.router)
```

Add a page route in the same router file if a new HTML page is needed:

```python
@router.get("/widgets-page", include_in_schema=False)
async def widgets_page():
    return FileResponse("static/widgets.html")
```

---

## New SQLite Table & Migrations

> **No Alembic.** Migrations are raw `ALTER TABLE` statements guarded by `PRAGMA table_info()`
> checks in `_run_column_migrations()` in `src/[package]/database/db.py`.
> There is no `_migrations` table and no `.sql` migration files.

### 1. Define ORM Model
Add the ORM model in `models.py`:

```python
class Widget(Base):
    __tablename__ = "widgets"
    id = Column(String, primary_key=True, default=lambda: str(uuid.uuid4()))
    ...
```

### 2. Schema creation
`Base.metadata.create_all()` in `init_db()` handles the initial schema on first boot. It is
idempotent — run it every startup; it only creates tables that are missing.

### 3. Adding new columns (`_run_column_migrations`)
For columns added after the initial schema, add an idempotent guard in `_run_column_migrations()`:

```python
cols = {row[1] for row in conn.execute(text("PRAGMA table_info(widgets)")).fetchall()}
if "new_col" not in cols:
    conn.execute(text("ALTER TABLE widgets ADD COLUMN new_col TEXT"))
    conn.commit()
    logger.info("Migration: added widgets.new_col column")
```

Never use Alembic. Never create `.sql` migration files. Never maintain a `_migrations` table.

#### SQLite WAL Mode
Enable **Write-Ahead Logging (WAL)** mode via a SQLAlchemy `connect` event listener in
`init_db()`, so it is applied to every connection the pool opens, not just the one used at startup:

```python
@event.listens_for(_engine, "connect")
def _set_sqlite_pragma(dbapi_conn, _rec):
    cur = dbapi_conn.cursor()
    cur.execute("PRAGMA journal_mode=WAL")
    cur.execute("PRAGMA synchronous=NORMAL")
    cur.execute("PRAGMA busy_timeout=30000")
    cur.close()
```

---

## Testing Conventions

See `06-testing-conventions.md` for the full strategy.
- **Backend**: Pytest in `tests/backend/`. Use `httpx.AsyncClient` with `transport=ASGITransport(app=...)` — the bare `app=` kwarg was removed in httpx 0.28.
- **Frontend**: Playwright in `tests/frontend/`.

---

## New InfluxDB Query

Add a method to `InfluxClient` in `influx.py`. Keep Flux query strings inside the method. Return plain Python dicts/lists (no ORM objects).

**Batch, never loop.** Add a `query_x_for_stations(ids: list[str])` method and call it once,
instead of calling a single-ID method inside a per-station loop. A per-item Influx round trip
inside a loop turns an O(1) query into O(n) queries under load.

---

## Auth Dependencies

Import from `[package].api.dependencies`:

| Dependency | Who passes |
|---|---|
| `get_current_user` | Any logged-in user |
| `require_admin` | `admin` only |

[Add additional role-based dependencies here as they are introduced.]

---

## Config

Add new keys to `config.py` Pydantic models **and** to `config.yml.example`. Never read `os.environ` directly — always go through `get_config()`.

**Resolve config at the call site, not at import time or in a module-level global.** A shared/base
module that calls `get_config()` itself (or reads any other patchable global, e.g.
`datetime.now()`) binds its own reference to that name — a test that `monkeypatch`s
`get_config()` on the *caller's* module namespace does not affect it, and the patch silently does
nothing. Take the resolved value as a parameter instead, passed in by the caller that already
went through `get_config()`. See `06-testing-conventions.md` for the test-side half of this.

---

## Scheduler Jobs

Add to `CollectorScheduler` in `scheduler.py`. Use `AsyncIOScheduler` + `IntervalTrigger`. Track health in `_collector_health` dict.

**Guard every job against overlapping runs with an `asyncio.Lock`.** If a trigger fires while the
previous run of the same job is still active, skip and log — never let two runs of the same
collector execute concurrently:

```python
_widget_lock = asyncio.Lock()

async def _run_widget_collector() -> None:
    if _widget_lock.locked():
        logger.info("widget collection already in progress — skipping trigger")
        return
    async with _widget_lock:
        ...
```

---

## Coding Standards

- **Always use type hints** on function signatures and class attributes.
- **Async/await** for all I/O — HTTP calls, DB writes, InfluxDB queries.
- **Pydantic v2** for all data schemas and config validation.
- **SQLAlchemy 2.0 style** — use `select()`, not legacy `query()`.
- **One router per domain** — never put all routes in `main.py`.
- **Abstract base classes** (ABC + `@abstractmethod`) for collectors.
- **Log extensively** — startup sequence, every request, every job run, every config value loaded. See `08-operability.md` for the full doctrine.
- **No print statements** in production code — always use the `logging` module.
- **Never call blocking synchronous I/O inside `async def` without `asyncio.to_thread()`.** InfluxDB
  client methods and most non-`httpx` HTTP libraries are synchronous — calling them directly in a
  handler or scheduler job blocks the entire event loop and stalls every concurrent request,
  `/health` included. The tell: a long gap between consecutive log lines with no output while the
  service otherwise looks idle.
  ```python
  # WRONG — blocks the event loop
  data = request.app.state.influx.query_latest(station_id)
  # RIGHT
  data = await asyncio.to_thread(request.app.state.influx.query_latest, station_id)
  ```
