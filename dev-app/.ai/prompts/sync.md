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

## Step 4 — Update Instructions (if needed)

Only update instruction files if a convention genuinely changed:
- New navigation pattern → `02-app-conventions.md`
- New API client pattern → `03-api-client-conventions.md`
- New hard rule → `04-constraints.md`

---

## Step 5 — Update Human-Readable Files

Run `.ai/prompts/update-readme.md` to sync `README.md` and `PLANNING.md` (if present).

---

## Step 6 — Leave Resume Notes

If work is in progress, update `specs/<current-feature>/tasks.md` with:
- Completed tasks marked `[x]`
- Next task clearly marked
- Blockers or open questions noted

---

## Step 7 — Final Checklist

- [ ] `context/architecture.md` reflects all new screens, state, API, and storage
- [ ] `context/features.md` reflects shipped milestones and updated backlog
- [ ] Instruction files updated if any convention changed
- [ ] README.md synced via `update-readme.md`
- [ ] In-progress work has a clear resume point
- [ ] No secrets or credentials in any committed file

Report the checklist result.
