# Prompt: Update AI Blueprint from Central Repo

> **Purpose**: Pull the latest framework files from the central AI Blueprint repo and apply them to this project's `.ai/` folder.
> **Category**: `bp-iac` — Infrastructure as Code (Docker Compose / Traefik / GitHub Actions)
> **Source**: https://github.com/Think4dvantage/ai-blueprint
> **Use when**: You want to sync improvements made to the central blueprint into this project.

---

## The Two Categories of Files

| Category | Files | Rule |
|---|---|---|
| **Framework** — owned by blueprint | `instructions/00-ai-usage.md`, `instructions/02-infra-conventions.md`, `instructions/03-cicd-conventions.md`, `instructions/04-constraints.md`, `instructions/05-user-profile.md`, `instructions/08-operability.md`, all `prompts/*.md` | Always overwrite with latest from repo |
| **Project data** — owned by this project | `instructions/01-project-overview.md`, `context/architecture.md`, `context/features.md`, `context/infra-notes.md`, `context/cicd-notes.md`, `context/constraints-notes.md`, `context/operability-notes.md` (project-specific conventions companion to a framework file — see `00-ai-usage.md`, "Framework vs Project Knowledge") | Never touch — project-specific |

---

## Step 0 — Fetch Blueprint Manifest

**URL**: `https://raw.githubusercontent.com/Think4dvantage/ai-blueprint/main/bp-iac/.ai/manifest.json`

- If successful, use the manifest file list in Step 1.
- If 404, use the hardcoded fallback list below.

---

## Step 1 — Fetch the Latest Framework Files

Base URL: `https://raw.githubusercontent.com/Think4dvantage/ai-blueprint/main/bp-iac/.ai/`

**Hardcoded Fallback List**:

| Remote path | Local path |
|---|---|
| `instructions/00-ai-usage.md` | `instructions/00-ai-usage.md` |
| `instructions/02-infra-conventions.md` | `instructions/02-infra-conventions.md` |
| `instructions/03-cicd-conventions.md` | `instructions/03-cicd-conventions.md` |
| `instructions/04-constraints.md` | `instructions/04-constraints.md` |
| `instructions/05-user-profile.md` | `instructions/05-user-profile.md` |
| `instructions/08-operability.md` | `instructions/08-operability.md` |
| `prompts/add-service.md` | `prompts/add-service.md` |
| `prompts/add-traefik-route.md` | `prompts/add-traefik-route.md` |
| `prompts/add-pipeline.md` | `prompts/add-pipeline.md` |
| `prompts/debug-infra.md` | `prompts/debug-infra.md` |
| `prompts/analyze.md` | `prompts/analyze.md` |
| `prompts/architect.md` | `prompts/architect.md` |
| `prompts/checklist.md` | `prompts/checklist.md` |
| `prompts/clarify.md` | `prompts/clarify.md` |
| `prompts/fix-bug.md` | `prompts/fix-bug.md` |
| `prompts/implement.md` | `prompts/implement.md` |
| `prompts/plan.md` | `prompts/plan.md` |
| `prompts/specify.md` | `prompts/specify.md` |
| `prompts/sync.md` | `prompts/sync.md` |
| `prompts/tasks.md` | `prompts/tasks.md` |
| `prompts/taskstoissues.md` | `prompts/taskstoissues.md` |
| `prompts/update-blueprint.md` | `prompts/update-blueprint.md` |
| `prompts/update-readme.md` | `prompts/update-readme.md` |

Fetch all files. If a fetch fails, note it and continue — do not abort.

---

## Step 2 — Detect New Files

Any file fetched that does not exist locally is a **new addition** — write it.

---

## Step 3 — Detect Removed Files

After writing all fetched files, check if any local framework files were NOT in the fetch list. List them and ask: "Delete? (yes/no per file)"

---

## Step 4 — Report Changes

| File | Result |
|---|---|
| `instructions/00-ai-usage.md` | Updated / No change / Added / Failed |

Summary: `N updated, N added, N unchanged, N failed, N need review`

---

## Step 5 — Check for Conflicts with Project Data

Skim the updated `00-ai-usage.md` and `02-infra-conventions.md` against project data files
(including any `context/*-notes.md` present). Flag mismatches — do not auto-fix.

Also check that every `context/*-notes.md` file present still has its companion `instructions/0X-*.md`
file cross-referencing it — a blueprint overwrite can silently drop that line if the generic
file's opening section changed. Re-add it if missing.

---

## Step 6 — Identify Contributions to the Blueprint

### 6.1 Scan for Local Additions

Identify files in `.ai/prompts/` or `.ai/instructions/` that exist locally but were NOT in the fetch list.

### 6.2 Propose the Contribution

> **Contribution Suggestion**: You've added/improved:
> - `prompts/new-iac-prompt.md`
>
> Contribute back to the central blueprint?

### 6.3 Provide the Transport Prompt

```markdown
# Contribution from Project: [PROJECT NAME] (bp-iac category)

Please update the following blueprint files in bp-iac/.ai/:

1. `prompts/new-iac-prompt.md`: [description]

[Paste full content here]
```

---

## Notes

- This prompt updates itself — that is intentional.
- Project data files (`01-project-overview.md`, `context/`) are never touched.
