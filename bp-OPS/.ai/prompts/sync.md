# Prompt: Sync Session & Prepare for /clear

> **Purpose**: Flush everything learned this session into `.ai/` and human-readable files, then close all open loops.
> **Use when**: Before running `/clear`, ending a long session, or any time context has drifted ahead of the files.

---

## Step 1 — Audit What Changed This Session

Scan the conversation and working directory. Note what is new or different:

| Category | What to check |
|---|---|
| Services | New or removed Docker Compose services |
| Networks | New networks added |
| Volumes | New named volumes |
| Pipelines | New or changed GitHub Actions workflows |
| Secrets | New environment variables or GitHub secrets |
| Traefik routes | New or changed routing rules |
| Features | Milestones shipped, backlog items completed or added |
| Open issues | Bugs found but not fixed, TODOs left |

Share the summary and wait for user confirmation before proceeding to Step 2.

---

## Step 2 — Update `.ai/context/architecture.md`

For every new or changed item:
- Add new services to the service table
- Update the network map
- Add new volumes to the volume inventory
- Add new pipelines to the pipeline inventory
- Add new environment variables to the env var table

Do not remove existing entries unless they were explicitly deleted this session.

---

## Step 3 — Update `.ai/context/features.md`

- Move completed milestones from Backlog to Shipped
- Add new backlog items
- Update the current version if a milestone shipped

---

## Step 4 — Update `context/*-notes.md`

This is where most session discoveries land — not `instructions/`. Write here whenever the
session surfaced:
- A new deployed service or fixed incident with a root cause worth guarding against → `infra-notes.md`
- A new pipeline quirk or CI/CD pattern → `cicd-notes.md`
- A fixed bug with a root cause worth guarding against → `constraints-notes.md`
- A new health-check, logging, or config-transparency instance → `operability-notes.md`

If the matching file doesn't exist yet, create it (see `00-ai-usage.md` — "Framework vs Project
Knowledge") and add a one-line cross-reference from the relevant `instructions/0X-*.md` file.

---

## Step 5 — Update `.ai/instructions/` (rare)

`instructions/00-ai-usage.md`, `02`, `03`, `04`, `08` are blueprint-owned — `update-blueprint.md`
overwrites them. Only touch them when something **generic and reusable by any project on this
blueprint** changed. If what changed is project-specific, it belongs in Step 4's
`context/*-notes.md` files instead, not here.

Do not rewrite instructions just to add detail.

---

## Step 6 — Update Human-Readable Files

Run `.ai/prompts/update-readme.md` to sync `README.md` and `PLANNING.md` (if present).

---

## Step 7 — Leave Resume Notes

If work is in progress, update `specs/<current-feature>/tasks.md` with:
- Completed tasks marked `[x]`
- Next task clearly marked
- Blockers or open questions noted

If no spec folder exists, write a brief `RESUME.md` at the repo root.

---

## Step 8 — Final Checklist

- [ ] `context/architecture.md` reflects all new/changed services, networks, volumes, pipelines
- [ ] `context/features.md` reflects shipped milestones and updated backlog
- [ ] `context/*-notes.md` files reflect any new project-specific conventions or fixed incidents
- [ ] Instruction files updated only if a genuinely generic, blueprint-reusable pattern changed
- [ ] README.md synced via `update-readme.md`
- [ ] In-progress work has a clear resume point
- [ ] No secrets or credentials in any committed file
- [ ] `.env.example` is up to date with all variables

Report the checklist result. Complete any incomplete item before finishing.
