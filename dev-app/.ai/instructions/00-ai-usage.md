# AI Instructions — Setup & Meta-Rules

This `.ai/` folder is the **single source of truth** for all AI instructions in this project. It is intentionally tool-agnostic: the same files work with Claude Code, GitHub Copilot, Cursor, Windsurf, or any other AI assistant.

---

## The Tool-Agnostic Rule

**All AI instructions live exclusively in `.ai/`.** Never create tool-specific instruction files.

- Do **not** create `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`, or any equivalent file — even as a thin pointer.
- If a tool requires its own file to function, configure it to point to `.ai/` via its settings UI — do not create a file in the repo.

---

## Framework vs Project Knowledge

`instructions/00-ai-usage.md`, `02`, `03`, `04`, `06`, `08`, and all of `prompts/*.md` are
**blueprint-owned** — `update-blueprint.md` overwrites them with the latest central version. They
must stay generic and reusable across any project built from this blueprint.

Project-specific conventions — the actual screen/state structure, the real API client quirks,
fixed-bug history, real test fixtures and coverage, real logging/error-reporting setup — do
**not** belong in those files, even though they're topically about "app conventions" or
"constraints." They belong in a `context/*-notes.md` companion file instead:

| Generic file | Project companion |
|---|---|
| `instructions/02-app-conventions.md` | `context/app-notes.md` |
| `instructions/03-api-client-conventions.md` | `context/api-client-notes.md` |
| `instructions/04-constraints.md` | `context/constraints-notes.md` |
| `instructions/06-testing-conventions.md` | `context/testing-notes.md` |
| `instructions/08-operability.md` | `context/operability-notes.md` |

`context/*-notes.md` files are **never** touched by `update-blueprint.md` — same guarantee as
`architecture.md` and `features.md`. When you discover a new project-specific rule, fixed bug, or
convention, write it to the matching `context/*-notes.md` file and leave a one-line
cross-reference in the generic `instructions/` file if none exists yet. See `prompts/sync.md`.

---

## Setting Up a New Project

1. Copy this `.ai/` folder to the repo root.
2. Find and replace every `[PROJECT NAME]` / `[package]` placeholder with real values.
3. Fill in `instructions/01-project-overview.md` — app purpose, target platforms, API base URL, feature areas.
4. Start filling in `context/architecture.md` as you add screens, state classes, and API integrations.
5. Update `context/features.md` as milestones ship.
6. Create `context/*-notes.md` companion files as project-specific conventions accumulate — see
   "Framework vs Project Knowledge" above. Don't create them empty; add the first one when the
   first project-specific rule shows up.

---

## Planning Mode

**When asked to plan (e.g. `/plan`, "let's plan this"), produce a plan and stop. Never start implementing immediately after a plan is approved.**

- Write the plan file → wait for an explicit "go ahead" / "implement" instruction in a new message.
- Approval of a plan means "the plan looks good" — not "start coding now".

### Planning Workflow

For any non-trivial feature, follow this sequence. Each step produces artifacts in `specs/<NNN>-<feature-name>/`.

```
Specify → Clarify → Plan → Checklist → Tasks → Analyze → Implement
```

See the matching prompt file in `.ai/prompts/` for each step.

Once a spec's feature has shipped (landed in `features.md` as a released version), move its
folder to `specs/archive/<NNN>-<feature-name>/` (`git mv`, so history follows) and update any
cross-references. This keeps `specs/` at the top level showing only what's still active —
drafted, planned, or shelved.

---

## Always Read First

Before making any changes, read the relevant `.ai/` files:

- `instructions/01-project-overview.md` — what the app is, target platforms, tech stack, feature areas
- `instructions/02-app-conventions.md` — project structure, state management, navigation, coding standards
- `instructions/03-api-client-conventions.md` — how to consume REST APIs
- `instructions/04-constraints.md` — hard rules (what NOT to do)
- `instructions/05-user-profile.md` — who the user is, how to communicate, working rules
- `instructions/06-testing-conventions.md` — Flutter test strategy
- `instructions/08-operability.md` — logging, error reporting, debug vs. release behavior
- `context/architecture.md` — screen map, state classes, API contracts consumed
- `context/features.md` — shipped milestones and backlog
- `context/app-notes.md`, `api-client-notes.md`, `constraints-notes.md`, `testing-notes.md`,
  `operability-notes.md` (if present) — the project's own instantiation of the generic
  conventions above (see "Framework vs Project Knowledge"). Each companion generic file
  cross-references its notes file; read both.

---

## General AI Behavior Rules

- Don't add features, refactor code, or make "improvements" beyond what was asked.
- Don't add comments, docstrings, or type annotations to code you didn't change.
- Don't add error handling for scenarios that can't happen.
- Don't create helpers or abstractions for one-time operations.
- Don't design for hypothetical future requirements.
- Read files before suggesting changes to them.
- Prefer editing existing files over creating new ones.

---

## Keeping the Repo in Sync

`.ai/` is the single source of truth, but humans also read `README.md`, `PLANNING.md`, and docs. Keep these in sync.

| `.ai/` change | Human-readable file(s) to update |
|---|---|
| New screen or feature shipped | `README.md`, `PLANNING.md` |
| New API endpoint consumed | `context/architecture.md` API contracts section |
| Backlog changed | `PLANNING.md` or `README.md` roadmap |
| New dependency added | `README.md` getting-started section |

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
    01-project-overview.md      ← What the app is, platforms, tech stack, feature areas
    02-app-conventions.md       ← Project structure, state management, navigation, Dart standards
    03-api-client-conventions.md ← REST API consumption, auth headers, error handling
    04-constraints.md           ← Hard rules: what NOT to do
    05-user-profile.md          ← Who the user is, communication style, working rules
    06-testing-conventions.md   ← Flutter test strategy: unit, widget, integration
    08-operability.md           ← Logging, error reporting, debug vs. release
  context/
    architecture.md             ← Screen map, state classes, API contracts consumed
    features.md                 ← Shipped milestones + backlog
    app-notes.md                ← (if present) project's real screen/state structure, fixed bugs — see 02
    api-client-notes.md         ← (if present) project's real API client quirks — see 03
    constraints-notes.md        ← (if present) project's fixed-bug history, hard-rule instances — see 04
    testing-notes.md            ← (if present) project's real fixtures, coverage, gotchas — see 06
    operability-notes.md        ← (if present) project's real logging/error-reporting setup — see 08
  prompts/
    new-feature.md              ← End-to-end Flutter feature checklist
    add-screen.md               ← Add a new screen to an existing feature
    fix-bug.md                  ← Bug-fixing workflow (Reproduction First)
    specify.md                  ← Write a feature specification
    clarify.md                  ← Resolve ambiguity in a spec
    plan.md                     ← Write a technical implementation plan
    checklist.md                ← Generate a requirements quality checklist
    tasks.md                    ← Break a plan into an ordered task list
    analyze.md                  ← Cross-check spec/plan/tasks for consistency
    implement.md                ← Execute a task list phase by phase
    taskstoissues.md            ← Convert tasks.md into GitHub Issues
    architect.md                ← Solution architect persona for project planning
    sync.md                     ← Flush session changes to .ai/ and human-readable files
    update-readme.md            ← Sync README and docs from .ai/ context
    update-blueprint.md         ← Pull latest framework files from central blueprint repo
```

---

## Constitution Principles

1. **Read before acting**: Always read `.ai/` before suggesting or making changes.
2. **Plan before building**: Non-trivial features get a spec + plan before implementation.
3. **Minimal scope**: Implement what was asked, not more.
4. **Tool-agnostic instructions**: All AI context lives in `.ai/`, not in tool-specific files.
5. **Keep docs in sync**: Run `sync.md` before ending a session.
6. **No secrets committed**: API keys and tokens are never hardcoded or committed. Use `--dart-define` or environment injection.
7. **Prod is off-limits**: Never push directly to production channels (App Store / Play Store) — always go through the release pipeline.
