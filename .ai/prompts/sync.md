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
| `instructions/05-user-profile.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/specify.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/clarify.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/plan.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/checklist.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/tasks.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/analyze.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/implement.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/architect.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/taskstoissues.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/fix-bug.md` | `dev-web`, `bp-OPS`, `dev-app` |
| `prompts/update-readme.md` | `dev-web`, `bp-OPS`, `dev-app` |

Do not auto-propagate category-specific files (conventions, update-blueprint.md, context templates).

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
- [ ] No secrets or credentials in any committed file

Report the checklist result. If any item is incomplete, fix it before finishing.
