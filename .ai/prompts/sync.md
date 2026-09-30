# Prompt: Sync the Blueprint Meta-Repo

> **Purpose**: Propagate shared file changes across all categories and keep the feature history current.
> **Use when**: After updating any shared file, after adding/modifying a category, or before ending a session.

---

## Step 1 — Audit Changes This Session

Scan for what changed:

| Check | What to look for |
|---|---|
| Shared files | Did `05-user-profile.md` change? Did any generic planning prompt change? |
| Category updates | Were conventions improved in one category that should propagate to others? |
| New prompts | Were prompts added to a category that belong in all categories? |
| New categories | Was a new category added? |
| Feature history | Were categories shipped or improved? |

Share the summary and wait for confirmation before proceeding.

---

## Step 2 — Propagate Shared Files

The following files must be identical across all categories. For each one that changed, copy it to the other two categories:

| File | Categories to update |
|---|---|
| `instructions/05-user-profile.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/specify.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/clarify.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/plan.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/checklist.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/tasks.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/analyze.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/implement.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/architect.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/taskstoissues.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/fix-bug.md` | `dev-web`, `bp-iac`, `dev-app` |
| `prompts/update-readme.md` | `dev-web`, `bp-iac`, `dev-app` |

Do not auto-propagate category-specific files (conventions, update-blueprint.md, context templates).

---

## Step 2b — Keep `manifest.json` in Sync

Each category's `manifest.json` (`{category}/.ai/manifest.json`) lists the exact same files as
that category's `prompts/update-blueprint.md` Framework row and Hardcoded Fallback List — it is
what `update-blueprint.md` Step 0 fetches first, before falling back to the hardcoded table. If
any file was added, removed, or renamed in a category this session (new prompt, renumbered
instruction file, etc.), update all three in lockstep:

1. `{category}/.ai/manifest.json` — `framework_files` array
2. `{category}/.ai/prompts/update-blueprint.md` — Framework row (line ~14) AND Hardcoded Fallback
   List table
3. `{category}/.ai/instructions/00-ai-usage.md` — File Map

These three must never disagree — that disagreement is exactly what the manifest exists to
prevent, and a stale manifest is worse than no manifest (it fetches successfully and lies).

---

## Step 3 — Update Feature History

Update `context/features.md`:
- Move completed items from Backlog to Shipped with a brief description
- Add new backlog items surfaced this session
- Update the current version if significant changes shipped

---

## Step 4 — Final Checklist

- [ ] Shared files are identical across all three categories
- [ ] `context/features.md` reflects current state
- [ ] `instructions/01-repo-overview.md` is accurate for all categories
- [ ] `prompts/apply-blueprint.md` matches the current category list
- [ ] Each category's `manifest.json`, `update-blueprint.md`, and `00-ai-usage.md` File Map agree on the file list (see Step 2b)
- [ ] No secrets or credentials in any committed file

Report the checklist result. If any item is incomplete, fix it before finishing.
