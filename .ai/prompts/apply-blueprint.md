# Prompt: Apply a Blueprint Category to a New Project

> **Purpose**: Copy the right `.ai/` category from this central repo into a new project.
> **Use when**: Starting a new project that needs AI context bootstrapped.

---

## Step 1 — Identify the Category

Determine which category fits the project:

| # | Category | Choose if... |
|---|---|---|
| 1 | `dev-web` | Python/FastAPI backend + Vanilla JS frontend |
| 2 | `bp-OPS` | Docker Compose services, Traefik routing, GitHub Actions pipelines |
| 3 | `dev-app` | Flutter mobile app |

If none fit, run `.ai/prompts/new-category.md` first to create one.

---

## Step 2 — Copy the Category

Copy the category's `.ai/` folder to the target project's root:

```bash
cp -r {category}/.ai/ /path/to/target-project/.ai/
```

Or, from the target project directory after cloning this repo:

```bash
cp -r /path/to/ai-blueprint/{category}/.ai/ .ai/
```

---

## Step 3 — Fill In Placeholders

The following files contain `[PROJECT NAME]` and `[package]` placeholders that must be replaced with real values before the first AI session:

- `instructions/01-project-overview.md` — tech stack, repo layout, data sources, user roles
- `context/architecture.md` — tables/services, API contracts

Open each file and replace every `[PLACEHOLDER]`.

---

## Step 4 — Verify the Blueprint Source URL

Open `prompts/update-blueprint.md` in the copied folder. Confirm the base URL points to the correct category path:

- dev-web: `https://raw.githubusercontent.com/Think4dvantage/ai-blueprint/main/dev-web/.ai/`
- bp-OPS: `https://raw.githubusercontent.com/Think4dvantage/ai-blueprint/main/bp-OPS/.ai/`
- dev-app: `https://raw.githubusercontent.com/Think4dvantage/ai-blueprint/main/dev-app/.ai/`

This is set correctly if you copied from the right category folder.

---

## Step 5 — Configure the AI Tool

Point the AI tool at `.ai/instructions/`:

- **Claude Code**: reads `.ai/instructions/` automatically when Claude Code is started in the project root. No extra config needed.
- **Cursor / Windsurf / Copilot**: point the tool's context path to `.ai/instructions/` via its settings UI. Do NOT create tool-specific instruction files in the repo.

---

## Step 6 — Verify

Ask the AI: "Read `.ai/instructions/00-ai-usage.md` and tell me what project this is and what stack it uses." A correct response means the blueprint loaded successfully.
