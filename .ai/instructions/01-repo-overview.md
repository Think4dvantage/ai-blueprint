# Blueprint Categories

## dev-web — Web Service

**Use for**: Backend web services with a browser-based UI.

**Stack**: Python 3.11+, FastAPI, SQLite (SQLAlchemy), InfluxDB, APScheduler, Vanilla JS (no build step).

**Key contents**:
- Backend: routers, SQLite migrations, InfluxDB queries, JWT auth
- Frontend: no-build JS, i18n, dark theme, console logging policy
- Testing: Pytest + Playwright
- API: standardized success/error response format (RFC 7807)
- Operability: structured logging, health endpoint, config transparency

**When NOT to use**: React/Vue frontend, build step required, PostgreSQL at scale, or the primary concern is infrastructure (use `bp-OPS`).

---

## bp-OPS — Operating Infrastructure

**Use for**: Infrastructure repos — Docker Compose service definitions, Traefik configuration, GitHub Actions pipelines, homelab deployments.

**Stack**: Docker, docker-compose, Traefik, GitHub Actions (self-hosted runner), shell scripts.

**Key contents**:
- Infra: service definitions, Traefik labels, network layout, secrets management
- CI/CD: GitHub Actions workflow patterns, pipeline structure, self-hosted runners
- Operability: container health, log aggregation, service status

**When NOT to use**: The repo contains application code. OPS repos contain only infrastructure definition files. If you need both, split into separate repos: one `dev-web` / `dev-app` for the app, one `bp-OPS` for its deployment.

---

## dev-app — Mobile App

**Use for**: Flutter mobile applications targeting iOS and/or Android.

**Stack**: Flutter 3.x, Dart, REST API clients (Dio), BLoC/Cubit state management.

**Key contents**:
- Flutter/Dart: project structure, state management, navigation, local storage
- API client: consuming REST APIs, auth headers, error handling, retry
- Testing: Flutter unit, widget, and integration tests

**When NOT to use**: Native Swift/Kotlin codebase (create a new category for that).

---

## Shared Files

These files are identical across all three categories. When one changes in this repo, update all three before committing.

| File | Why it's shared |
|---|---|
| `instructions/05-user-profile.md` | Who the user is — same person across all project types |
| Planning prompts (`specify`, `clarify`, `plan`, `checklist`, `tasks`, `analyze`, `implement`, `architect`, `taskstoissues`, `fix-bug`, `update-readme`) | Tool-agnostic workflow — applies equally to all stacks |

The canonical version of `05-user-profile.md` lives at `.ai/instructions/05-user-profile.md` in this root. Run `.ai/prompts/sync.md` to propagate changes to all categories.

---

## Applying a Blueprint to a New Project

See `.ai/prompts/apply-blueprint.md` for the step-by-step workflow.

## Adding a New Category

See `.ai/prompts/new-category.md` for the workflow.
