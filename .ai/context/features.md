# Feature History & Backlog

## Current Version: v2.0 (shipped)

### Shipped Milestones

| Milestone | What shipped |
|---|---|
| v1.0 | Full `.ai/` blueprint: instructions (00–05), context templates, 14 prompts covering planning workflow (specify→clarify→plan→checklist→tasks→analyze→implement), sync/update prompts, user profile, architect persona, central update mechanism. Pushed to GitHub at Think4dvantage/ai-blueprint. |
| v2.0 | Multi-category restructure: blueprint split into `dev-web` (FastAPI + Vanilla JS), `bp-OPS` (Docker/Traefik/GitHub Actions infrastructure), and `dev-app` (Flutter mobile). Root `.ai/` repurposed as meta-repo instructions. Each category is self-contained with adapted instructions, context templates, and category-specific prompts. |

---

## Backlog (unordered)

- Add a PostgreSQL variant of `dev-web/02-backend-conventions.md` for projects that outgrow SQLite
- Add a `dev-web/prompts/add-auth-provider.md` scaffold prompt for OAuth/OIDC integration
- Add a `bp-OPS/prompts/add-backup.md` prompt for scheduled backup pipelines
- Add a `dev-data` category for data science / notebook repos (Python, Jupyter, pandas, DVC)
