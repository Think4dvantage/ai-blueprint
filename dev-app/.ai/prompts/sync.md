# Prompt: Sync Session & Prepare for /clear

> **Purpose**: Flush everything learned this session into `.ai/` and human-readable files, then close all open loops.
> **Use when**: Before running `/clear`, ending a long session, or any time context has drifted ahead of the files.

---

## Step 1 — Audit What Changed This Session

Scan the conversation and working directory. Note what is new or different:

| Category | What to check |
|---|---|
| Screens | New screens added or removed |
| State | New Cubits or state classes |
| API | New endpoints consumed |
| Local storage | New keys or Hive collections |
| Dependencies | New pub.dev packages added |
| Features | Milestones shipped, backlog items completed or added |
| Open issues | Bugs found but not fixed, TODOs left in code |
| In-progress work | Partially completed features |

Share the summary and wait for user confirmation before proceeding to Step 2.

---

## Step 2 — Update `.ai/context/architecture.md`

For every new or changed item:
- Add new screens to the screen map
- Add new Cubits to the state classes table
- Add new API endpoints to the API contracts section
- Add new local storage keys to the local storage table
- Add new dependencies to the dependencies table

Do not remove existing entries unless explicitly deleted this session.

---

## Step 3 — Update `.ai/context/features.md`

- Move completed milestones from Backlog to Shipped
- Add new backlog items
- Update the current version if a milestone shipped

---

## Step 4 — Update `context/*-notes.md`

This is where most session discoveries land — not `instructions/`. Write here whenever the
session surfaced:
- A new navigation or state-management pattern, or a fixed bug in the screen/state structure → `app-notes.md`
- A new API client quirk or pattern → `api-client-notes.md`
- A fixed bug with a root cause worth guarding against → `constraints-notes.md`
- A new test fixture, gotcha, or coverage change → `testing-notes.md`
- A new logging or error-reporting instance → `operability-notes.md`

If the matching file doesn't exist yet, create it (see `00-ai-usage.md` — "Framework vs Project
Knowledge") and add a one-line cross-reference from the relevant `instructions/0X-*.md` file.

---

## Step 5 — Update `.ai/instructions/` (rare)

`instructions/00-ai-usage.md`, `02`, `03`, `04`, `06`, `08` are blueprint-owned —
`update-blueprint.md` overwrites them. Only touch them when something **generic and reusable by
any project on this blueprint** changed. If what changed is project-specific, it belongs in
Step 4's `context/*-notes.md` files instead, not here.

---

## Step 6 — Update Human-Readable Files

Run `.ai/prompts/update-readme.md` to sync `README.md` and `PLANNING.md` (if present).

---

## Step 7 — Leave Resume Notes

If work is in progress, update `specs/<current-feature>/tasks.md` with:
- Completed tasks marked `[x]`
- Next task clearly marked
- Blockers or open questions noted

---

## Step 8 — Final Checklist

- [ ] `context/architecture.md` reflects all new screens, state, API, and storage
- [ ] `context/features.md` reflects shipped milestones and updated backlog
- [ ] `context/*-notes.md` files reflect any new project-specific conventions or fixed bugs
- [ ] Instruction files updated only if a genuinely generic, blueprint-reusable pattern changed
- [ ] README.md synced via `update-readme.md`
- [ ] In-progress work has a clear resume point
- [ ] No secrets or credentials in any committed file

Report the checklist result.
