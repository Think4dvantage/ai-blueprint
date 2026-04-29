# AI Instructions — Setup & Meta-Rules

This `.ai/` folder is the **single source of truth** for all AI instructions in this project. It is intentionally tool-agnostic: the same files work with Claude Code, GitHub Copilot, Cursor, Windsurf, or any other AI assistant.

---

## The Tool-Agnostic Rule

**All AI instructions live exclusively in `.ai/`.** Never create tool-specific instruction files.

- Do **not** create `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`, or any equivalent file — even as a thin pointer.
- If a tool requires its own file to function, configure it to point to `.ai/` via its settings UI — do not create a file in the repo.

---

## Setting Up a New Project

1. Copy this `.ai/` folder to the repo root.
2. Fill in `instructions/01-project-overview.md` — services deployed, network layout, CI/CD pipelines.
3. Start filling in `context/architecture.md` as you add services, routes, and pipelines.
4. Update `context/features.md` as milestones ship.

---

## Planning Mode

**When asked to plan (e.g. `/plan`, "let's plan this"), produce a plan and stop. Never start implementing immediately after a plan is approved.**

- Write the plan file → wait for an explicit "go ahead" / "implement" instruction in a new message.
- Approval of a plan means "the plan looks good" — not "start configuring now".

### Planning Workflow

For any non-trivial change, follow this sequence. Each step produces artifacts in `specs/<NNN>-<feature-name>/`.

```
Specify → Clarify → Plan → Checklist → Tasks → Analyze → Implement
```

See the matching prompt file in `.ai/prompts/` for each step.

---

## Always Read First

Before making any changes, read the relevant `.ai/` files:

- `instructions/01-project-overview.md` — services deployed, network layout, CI/CD overview
- `instructions/02-infra-conventions.md` — Docker Compose, Traefik, secrets, networking
- `instructions/03-cicd-conventions.md` — GitHub Actions workflow patterns
- `instructions/04-constraints.md` — hard rules (what NOT to do)
- `instructions/05-user-profile.md` — who the user is, how to communicate
- `instructions/06-operability.md` — container health, monitoring, logging
- `context/architecture.md` — deployed services map, network layout, pipeline inventory
- `context/features.md` — shipped milestones and backlog

---

## General AI Behavior Rules

- Don't add services, refactor configs, or make "improvements" beyond what was asked.
- Don't add comments to files you didn't change.
- Don't add error handling for scenarios that can't happen.
- Don't create helpers or abstractions for one-time operations.
- Read files before suggesting changes to them.
- Prefer editing existing files over creating new ones.

---

## Keeping the Repo in Sync

`.ai/` is the single source of truth, but humans also read plain files — `README.md`, `PLANNING.md`, docs. Keep these in sync.

| `.ai/` change | Human-readable file(s) to update |
|---|---|
| New service added to `context/architecture.md` | `README.md` services section |
| New CI/CD pipeline | `README.md` deployment section |
| Backlog changed in `context/features.md` | `PLANNING.md` if present |
| New setup step or dependency | `README.md` getting-started |

**Before ending a session**, run `.ai/prompts/sync.md` to flush all pending updates.

### Staying in Sync with the Blueprint

To pull the latest framework improvements from the central [AI Blueprint](https://github.com/Think4dvantage/ai-blueprint):

`/prompt .ai/prompts/update-blueprint.md`

---

## File Map

```
.ai/
  instructions/
    00-ai-usage.md              ← This file: setup, meta-rules, planning mode
    01-project-overview.md      ← Services deployed, network layout, CI/CD overview
    02-infra-conventions.md     ← Docker Compose, Traefik labels, networking, secrets
    03-cicd-conventions.md      ← GitHub Actions workflow patterns, pipeline structure
    04-constraints.md           ← Hard rules: what NOT to do
    05-user-profile.md          ← Who the user is, communication style, working rules
    06-operability.md           ← Container health, monitoring, log strategy
  context/
    architecture.md             ← Deployed services, networks, pipeline inventory
    features.md                 ← Shipped milestones + backlog
  prompts/
    add-service.md              ← Add a new Docker Compose service
    add-traefik-route.md        ← Add Traefik routing for a service
    add-pipeline.md             ← Add a GitHub Actions workflow
    debug-infra.md              ← Diagnose an infrastructure problem
    specify.md                  ← Write a change specification
    clarify.md                  ← Resolve ambiguity in a spec
    plan.md                     ← Write a technical implementation plan
    checklist.md                ← Generate a requirements quality checklist
    tasks.md                    ← Break a plan into an ordered task list
    analyze.md                  ← Cross-check spec/plan/tasks for consistency
    implement.md                ← Execute a task list phase by phase
    taskstoissues.md            ← Convert tasks.md into GitHub Issues
    architect.md                ← Solution architect persona for planning
    fix-bug.md                  ← Infrastructure debugging workflow
    sync.md                     ← Flush session changes to .ai/ and human-readable files
    update-readme.md            ← Sync README and docs from .ai/ context
    update-blueprint.md         ← Pull latest framework files from central blueprint repo
```

---

## Constitution Principles

1. **Read before acting**: Always read `.ai/` before suggesting or making changes.
2. **Plan before building**: Non-trivial changes get a spec + plan before implementation.
3. **Minimal scope**: Implement what was asked, not more.
4. **Tool-agnostic instructions**: All AI context lives in `.ai/`, not in tool-specific files.
5. **Keep docs in sync**: Run `sync.md` before ending a session.
6. **No secrets committed**: `.env` and secret files are gitignored. Only `.example` files are committed.
7. **Prod is off-limits**: Never modify production infrastructure directly. All changes go through the pipeline.
