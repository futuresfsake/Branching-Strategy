# TODO — Implement the CI/CD Pipeline (Not Yet Done)

> **Status: not implemented.** This file is the spec for the automation that should
> eventually enforce everything described in [Rules.md](./Rules.md),
> [Access-Control.md](./Access-Control.md), and
> [Pipeline-Flow-Diagram.md](./Pipeline-Flow-Diagram.md). Until the steps below are
> done, branch protection and deploys are manual/trust-based, not automated.

## What this is

An automated pipeline on GitHub has two parts: files you add to the repo, and
settings you configure on GitHub.

| Part | Where | What it does |
|---|---|---|
| 1. Workflow files (code) | `.github/workflows/*.yml` in the repo | Define **what runs and when**: build, test, deploy, open promotion PRs |
| 2. Repo settings (configuration) | GitHub → Settings | Define **who's allowed and what's required**: branch protection, environments, approvers, secrets |

The tool is **GitHub Actions** — GitHub's built-in CI/CD (Continuous Integration /
Continuous Delivery). It runs YAML instructions on GitHub's machines whenever
something happens in the repo, like a push or a PR.

## What the pipeline looks like

```
feature/chatbot ──PR──► dev ──────────► staging ──────────► production
      │                  │                 │                     │
  ① CI runs:         ② auto-deploy     ② auto-deploy        ② deploy waits for
  build + tests        to DEV server     to STAGING server    human approval ✋
  (must pass to          │                                         │
   merge)                ③ auto-opens PR "dev → staging"          then deploys
                           (approver reviews and merges)
```

This matches the branch flow already documented in
[Pipeline-Flow-Diagram.md](./Pipeline-Flow-Diagram.md) — this file is just the
GitHub Actions implementation of that diagram.

## Part 1 — Workflow files

### ① CI: test every PR

`.github/workflows/ci.yml`

```yaml
name: CI

on:
  pull_request:
    branches: [dev, staging, main]   # runs on any PR targeting these branches

jobs:
  build-and-test:
    runs-on: ubuntu-latest           # GitHub provides this machine for free
    steps:
      - uses: actions/checkout@v4    # download the repo's code
      - uses: actions/setup-node@v4  # example uses Node.js; swap for your stack
        with:
          node-version: 20
      - run: npm ci                  # install exact dependency versions
      - run: npm run lint
      - run: npm test
```

### ② Deploy: when a branch is updated, deploy to its environment

`.github/workflows/deploy.yml`

```yaml
name: Deploy

on:
  push:
    branches: [dev, staging, main]   # a merged PR counts as a push

jobs:
  deploy:
    runs-on: ubuntu-latest
    # map branch → GitHub Environment (configured in Part 2)
    environment: ${{ github.ref_name == 'main' && 'production' || github.ref_name }}
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run build
      - name: Deploy
        run: ./scripts/deploy.sh      # depends on where you host
        env:
          API_KEY: ${{ secrets.DEPLOY_API_KEY }}   # each environment has its own secret
```

The deploy step depends on where the app runs: Vercel, Netlify, Azure, AWS, a VPS
over SSH, Docker, etc. Each has its own ready-made Action or CLI command — fill
in `./scripts/deploy.sh` once hosting is decided.

### ③ (Optional) Automatically open a "dev → staging" PR

`.github/workflows/promote.yml`

```yaml
name: Open promotion PR

on:
  push:
    branches: [dev]

permissions:
  contents: read
  pull-requests: write

jobs:
  open-pr:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: |
          gh pr create --base staging --head dev \
            --title "Promote dev → staging" \
            --body "Automated promotion PR. Review before merging." \
          || echo "Promotion PR already open, skipping."
        env:
          GH_TOKEN: ${{ github.token }}
```

## Part 2 — GitHub settings

| Setting | Where | What to set |
|---|---|---|
| Default branch | Settings → General | `dev` |
| Branch protection for `dev`, `staging`, `main` | Settings → Branches (or Rules → Rulesets) | Require a PR, require approvals (1 for `dev`, 2 for `main`), require status checks to pass (`build-and-test`), block force pushes |
| Environments | Settings → Environments | Create `dev`, `staging`, `production`. On `production`, add required reviewers so deploys wait for approval. Limit it to deploy only from `main`. |
| Secrets | Settings → Environments → (env) → Secrets | Separate credentials per environment — e.g. a different `DEPLOY_API_KEY` for staging vs. production |
| Allow Actions to create PRs (only for ③) | Settings → Actions → General | Turn on "Allow GitHub Actions to create and approve pull requests" |

> **Plan limitation:** environments and protection rules like required reviewers
> are fully available on public repos. Private repos need a paid plan (Pro, Team,
> or Enterprise depending on the feature) for some of this. If a setting is
> greyed out, that's why.

## Implementation steps (do in this order)

1. [ ] **CI first.** Add `ci.yml` and turn on "require status checks" for `dev`,
       `staging`, `main`. This gives the most value immediately: broken code can't
       be merged.
2. [ ] **Auto-deploy `dev`**, then **auto-deploy `staging`**, using `deploy.yml`.
3. [ ] **Production with manual approval** — add required reviewers to the
       `production` environment so `main` deploys wait for a human.
4. [ ] **Promotion PR automation** (`promote.yml`) — only if the team wants
       `dev → staging` PRs opened automatically.
5. [ ] Fill in `./scripts/deploy.sh` for the actual hosting target.
6. [ ] Create the `dev`, `staging`, `production` environments and their secrets
       in GitHub Settings (Part 2 table above).
7. [ ] Verify branch protection rules match
       [Access-Control.md](./Access-Control.md) (who approves what).

## Common mistakes to avoid

- **Putting secrets in the YAML.** Never write passwords or API keys in workflow
  files — use `secrets.*`. Anything committed stays in git history forever.
- **PRs created with `GITHUB_TOKEN` don't trigger other workflows** (GitHub does
  this on purpose to prevent infinite loops). The auto-created "dev → staging" PR
  from ③ won't re-run CI — rely on the fact that the same commits already passed
  CI on `dev`, or use a GitHub App token / fine-grained PAT instead.
- **Fully automatic deploys to production with no gate.** Keep a human approval
  step before production.
- **Workflows that never fail.** If tests are skipped or always pass, CI gives a
  false sense of safety.

## Things to keep in mind while implementing

- **Build once, deploy many.** Ideally build the app once (e.g. a Docker image)
  and promote that same artifact through `dev → staging → production`, so
  production runs exactly what was tested.
- **Least privilege.** The production deploy secret should only be available to
  the `production` environment, and only from `main`. A feature branch should
  never be able to reach production credentials.
- **Observability and rollback.** Every deploy should be traceable (which commit,
  who approved it). Keep a one-command way to redeploy the previous version.
- **Cost.** GitHub Actions is free for public repos. Private repos get a monthly
  allowance of free minutes — avoid slow jobs and cache dependencies.

## Key takeaways

- An automated pipeline = workflow YAML files in the repo + settings on GitHub.
- CI on PRs, auto-deploy on merge, and a manual approval before production is a
  solid, standard setup.
- Start with CI and branch protection, then add deploys one environment at a
  time.

## Related

[Rules.md](./Rules.md) for the branch flow this automates,
[Access-Control.md](./Access-Control.md) for who the required reviewers/approvers
should be, and [Pipeline-Flow-Diagram.md](./Pipeline-Flow-Diagram.md) for the
visual this implements.
