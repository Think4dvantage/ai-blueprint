# Prompt: Add a GitHub Actions Pipeline

Use this checklist when adding a new CI/CD workflow.

---

## Pre-flight

- [ ] Read `context/architecture.md` — confirm the pipeline doesn't already exist
- [ ] Confirm the trigger: push to `main`, tag, pull request, or schedule
- [ ] Confirm the runner: `[self-hosted, lg4]` for homelab deployments, `ubuntu-latest` for pure validation

---

## Workflow File

Create `.github/workflows/{purpose}.yml`. Naming:
- `deploy.yml` — push to main / tag triggers deployment
- `validate.yml` — PR validation (no deployment)
- `schedule.yml` — cron-triggered maintenance

## Required Sections

- [ ] `name:` — human-readable workflow name shown in the Actions UI
- [ ] `on:` — trigger definition (push/pull_request/schedule/workflow_dispatch)
- [ ] At least two jobs: `validate` (always) + `deploy` (for deployment workflows)
- [ ] `deploy` job has `needs: validate`
- [ ] All jobs have descriptive `name:` on each step

## Validate Job

```yaml
validate:
  runs-on: [self-hosted, lg4]
  steps:
    - uses: actions/checkout@v4
    - name: Validate compose file
      run: docker-compose config
```

## Deploy Job

```yaml
deploy:
  needs: validate
  runs-on: [self-hosted, lg4]
  steps:
    - uses: actions/checkout@v4
    - name: Pull latest images
      run: docker-compose pull
    - name: Deploy
      run: docker-compose up -d --remove-orphans
    - name: Verify container health
      run: |
        sleep 10
        docker-compose ps
```

## Secrets

- [ ] All secrets referenced as `${{ secrets.NAME }}` — never echoed to logs
- [ ] New secrets documented in `context/architecture.md` (name + what it's for, not value)
- [ ] New secrets added to the GitHub repo's Actions secrets

## Documentation

- [ ] Add the pipeline to `context/architecture.md` (pipeline inventory table)
- [ ] Update `README.md` deployment section

## Verify

- [ ] Workflow YAML is valid: `python -c "import yaml; yaml.safe_load(open('.github/workflows/{file}.yml'))"`
- [ ] First run completes without errors in the Actions UI
