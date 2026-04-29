# Prompt: Add a New Blueprint Category

> **Purpose**: Create a new blueprint category when no existing one fits a project type.
> **Use when**: A new project type needs AI context and the existing categories don't cover it.

---

## When to Add vs. Adapt

**Adapt an existing category** if fewer than 3 instruction files would need significant rewrites.

**Add a new category** if the stack, workflow, or constraints are fundamentally different — e.g., a data science notebook repo, a native iOS app, a Kubernetes infra repo.

---

## Step 1 — Define the Category

Answer these before starting:

1. **Identifier**: Short folder name using kebab-case (e.g., `dev-api`, `bp-k8s`, `dev-data`)
2. **Stack**: Which technologies does this category cover?
3. **Differentiator**: What makes it meaningfully different from existing categories?
4. **Closest existing**: Which existing category is most similar? (Start from there.)

---

## Step 2 — Scaffold the Folder

Create this structure:

```
{category}/
  .ai/
    instructions/
      00-ai-usage.md              ← Adapted from closest existing category
      01-project-overview.md      ← Template with [PLACEHOLDERS]
      02-conventions.md           ← Main stack conventions (add more files if needed)
      04-constraints.md           ← Adapted from closest existing category
      05-user-profile.md          ← Copy from root .ai/instructions/05-user-profile.md unchanged
    context/
      architecture.md             ← Template matching this stack's data model
      features.md                 ← Clean template
    prompts/
      new-feature.md              ← Category-specific implementation checklist
      fix-bug.md                  ← Copy from any existing category (generic)
      specify.md                  ← Copy from any existing category (generic)
      clarify.md                  ← Copy (generic)
      plan.md                     ← Copy (generic)
      checklist.md                ← Copy (generic)
      tasks.md                    ← Copy (generic)
      analyze.md                  ← Copy (generic)
      implement.md                ← Copy (generic)
      architect.md                ← Copy (generic)
      taskstoissues.md            ← Copy (generic)
      update-readme.md            ← Copy (generic)
      sync.md                     ← Adapted for this category's file structure
      update-blueprint.md         ← Set base URL to this category's path in the central repo
```

---

## Step 3 — Write Category-Specific Files

1. **`00-ai-usage.md`** — copy the closest existing category's version, then update:
   - "Always Read First" section (file references)
   - File map

2. **`01-project-overview.md`** — write a template for the new stack. Include:
   - Tech stack table
   - Repository layout diagram
   - Data flow description
   - Placeholder sections for project-specific values

3. **`02-conventions.md`** (or split into numbered files) — document:
   - How to add the primary building block (route, service, screen, etc.)
   - Coding standards specific to this stack
   - Auth / security patterns
   - Anything that must be done the same way every time

4. **`04-constraints.md`** — list hard rules. What MUST NOT happen in this stack?

5. **`context/architecture.md`** — design a template that captures the data model for this stack (tables, services, screens, API contracts — whatever applies).

6. **`prompts/new-feature.md`** — an end-to-end checklist for adding a feature in this stack.

7. **`prompts/sync.md`** — adapt the session sync prompt for this category's files.

8. **`prompts/update-blueprint.md`** — copy from any category, update:
   - Base URL: `https://raw.githubusercontent.com/Think4dvantage/ai-blueprint/main/{category}/.ai/`
   - Hardcoded fallback file list to match this category's actual files

---

## Step 4 — Update the Blueprint Index

1. Add an entry to `.ai/instructions/01-repo-overview.md` under the new category name.
2. Add the category to the Step 1 table in `.ai/prompts/apply-blueprint.md`.
3. Add the category's `05-user-profile.md` to the shared-file propagation list in `.ai/prompts/sync.md`.
4. Add the category to `context/features.md` as a shipped item.

---

## Step 5 — Verify

Copy the new category to a test project and run a dry-run:
1. Ask the AI to read `00-ai-usage.md` and confirm it understands the stack.
2. Ask the AI to add a feature — verify it follows the new conventions.
