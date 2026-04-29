# AI Instructions — Blueprint Meta-Repo

This repository IS the AI Blueprint. It contains category-specific `.ai/` folder templates that are copied into real projects to give AI assistants consistent, opinionated context.

---

## What This Repo Is

Three blueprint categories are maintained here:

| Category | Folder | Use for |
|---|---|---|
| `dev-web` | `dev-web/.ai/` | FastAPI + Vanilla JS web services |
| `bp-OPS` | `bp-OPS/.ai/` | Infrastructure: Docker Compose, Traefik, GitHub Actions |
| `dev-app` | `dev-app/.ai/` | Flutter mobile apps |

Each category is a self-contained `.ai/` folder. Copy the right one into a new project's root to bootstrap it with full AI context.

---

## Rules for This Repo

- **Never create tool-specific files** (`CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`, etc.) — all AI instructions live in `.ai/`. No wrapper files, ever.
- **Category folders are the source of truth** — edit in the category folder, not in any consuming project.
- **Shared files must stay in sync** — `05-user-profile.md` is identical across all categories. When it changes here, propagate to all three.
- **Don't mix categories** — each category is independent. No cross-references between `dev-web/` and `bp-OPS/`.

---

## Planning Mode

**When asked to plan, produce a plan and stop. Never start implementing immediately after a plan is approved.**

- Write the plan → wait for an explicit "go ahead" instruction in a new message.
- Approval of a plan means "looks good" — not "start coding now".

---

## Always Read First

Before making any changes to this repo, read:

- `instructions/01-repo-overview.md` — category descriptions, when to use each
- The relevant category folder for any category-specific changes

---

## General AI Behavior Rules

- Don't add features, refactor, or make improvements beyond what was asked.
- Don't add comments to files you didn't change.
- Read files before suggesting changes to them.
- Prefer editing existing files over creating new ones.

---

## File Map

```
.ai/
  instructions/
    00-ai-usage.md         ← This file: meta-rules for the blueprint repo itself
    01-repo-overview.md    ← Category descriptions, when to use each, shared files
    05-user-profile.md     ← Canonical user profile — propagate changes to all categories
  prompts/
    apply-blueprint.md     ← How to copy a category into a new project
    new-category.md        ← How to add a new blueprint category
    sync.md                ← Sync shared files across categories; update features.md

dev-web/.ai/               ← Blueprint for FastAPI + Vanilla JS web services
bp-OPS/.ai/                ← Blueprint for Docker/Traefik/GitHub Actions infra repos
dev-app/.ai/               ← Blueprint for Flutter mobile apps
```

---

## When to Add a New Category

Add a new category when an existing one would need more than 3 significant instruction changes to fit a new project type. Run `.ai/prompts/new-category.md` for the workflow.
