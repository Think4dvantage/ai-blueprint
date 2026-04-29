# CI/CD Conventions

## Pipeline Structure

Every GitHub Actions workflow follows this stage order:

```
validate → build → deploy
```

Never merge build and deploy into one job. They must be separate jobs so failures are isolated.

---

## Workflow File Naming

| File | Trigger | Purpose |
|---|---|---|
| `deploy.yml` | Push to `main`, or tag `v*` | Build image + deploy to homelab |
| `validate.yml` | Pull request | Lint, config validation, dry-run checks |
| `schedule.yml` | Cron | Recurring maintenance (backups, cleanup) |

One workflow file per concern. Never put unrelated jobs in the same file.

---

## Standard Deploy Workflow

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  validate:
    runs-on: [self-hosted, lg4]
    steps:
      - uses: actions/checkout@v4
      - name: Validate compose file
        run: docker-compose config

  deploy:
    needs: validate
    runs-on: [self-hosted, lg4]
    steps:
      - uses: actions/checkout@v4
      - name: Pull latest images
        run: docker-compose pull
      - name: Deploy
        run: docker-compose up -d --remove-orphans
      - name: Verify health
        run: |
          sleep 10
          docker-compose ps
```

**Rules**:
- Always pin `actions/checkout` to a major version tag (e.g., `@v4`).
- Always run `docker-compose config` to validate YAML before deploying.
- Always run `--remove-orphans` to clean up removed services.
- Health verification step is mandatory — check container status after deploy.

---

## Self-Hosted Runner

The homelab runner label is `[self-hosted, lg4]`. Always use this for deployments to `lg4.ch`.

GitHub-hosted runners (`ubuntu-latest`) are acceptable only for pure validation steps that don't need Docker or homelab network access.

---

## Secrets in Pipelines

Reference GitHub repository secrets as `${{ secrets.SECRET_NAME }}`. Never echo secrets to logs.

```yaml
- name: Login to registry
  run: echo "${{ secrets.REGISTRY_TOKEN }}" | docker login registry.example.com -u ci --password-stdin
```

---

## Image Tagging

| Trigger | Tag strategy |
|---|---|
| Push to `main` | `image:latest` + `image:sha-{git_sha[:7]}` |
| Tag `v1.2.3` | `image:1.2.3` + `image:latest` |
| Pull request | `image:pr-{number}` (never pushed to prod) |

Always push the SHA tag alongside `latest` so rollbacks are possible:

```yaml
- name: Tag and push
  run: |
    docker tag myapp:build myapp:latest
    docker tag myapp:build myapp:sha-${{ github.sha }}
    docker push myapp:latest
    docker push myapp:sha-${{ github.sha }}
```

---

## Rollback

To roll back a deployment, redeploy the previous SHA tag:

```bash
docker-compose stop my-service
docker tag registry/myapp:sha-{previous-sha} registry/myapp:latest
docker-compose up -d my-service
```

Document the rollback procedure in `README.md`.

---

## Validate-Only Jobs (PRs)

For pull requests, run validation without deploying:

```yaml
- name: Validate docker-compose
  run: docker-compose -f docker-compose.yml config

- name: Check for hardcoded secrets
  run: grep -r "password\s*=\s*['\"][^$]" . --include="*.yml" && exit 1 || true
```
