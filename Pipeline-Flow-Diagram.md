# The Full Pipeline, Visually

This file is the picture that ties every other doc in this folder together: feature branches, the `dev → staging → production` promotion chain, which server each stage deploys to, and where a `hotfix/*` fits in.

## The Diagram

```mermaid
%%{init: { 'gitGraph': {'mainBranchName': 'dev'} } }%%
gitGraph
    commit id: "dev-0"
    branch staging
    branch production
    checkout dev
    branch feature/login
    commit id: "login-1"
    commit id: "login-2"
    checkout dev
    branch feature/cart
    commit id: "cart-1"
    commit id: "cart-2"
    checkout dev
    merge feature/login id: "PR + review: login"
    merge feature/cart id: "PR + review: cart"
    commit id: "🚀 deploy → DEV server"
    checkout staging
    merge dev id: "promote (PR)"
    commit id: "🚀 deploy → STAGING server"
    checkout production
    merge staging id: "promote (PR + approval)"
    commit id: "🚀 deploy → PRODUCTION"
    branch hotfix/bug-123
    commit id: "fix bug-123"
    checkout production
    merge hotfix/bug-123 id: "merge → redeploy PROD"
    checkout staging
    merge production id: "back-merge fix"
    checkout dev
    merge production id: "back-merge fix"
```

Read it top to bottom, in order:

1. `feature/login` and `feature/cart` are built in isolation, off `dev`.
2. Each opens a **PR into `dev`** and goes through code review before merging.
3. `dev` deploys automatically to the **DEV server** for integration testing.
4. A **promotion PR** moves `dev` → `staging`, which deploys to the **STAGING server** for QA/UAT against prod-like data.
5. A **promotion PR + approval** moves `staging` → `production`, which deploys live to real users.
6. A `hotfix/*` branches **off `production`** (never off `dev`), merges straight back into `production` to redeploy the fix, then is **manually back-merged** into `staging` and `dev` so the fix isn't lost on the next release.

## Branch → Environment Map

| Branch | Deploys to | Purpose | How code gets in |
|---|---|---|---|
| `feature/*` | Nowhere (local/preview only) | Build one thing, in isolation | — |
| `dev` | DEV server | Integration testing — do everyone's changes work together? | PR + code review, from `feature/*` |
| `staging` | STAGING server | QA, UAT, testing against prod-like data | Promotion PR, from `dev` |
| `production` | PROD | Real users | Promotion PR + approval, from `staging` |
| `hotfix/*` | Rides along with `production` | Emergency fix for a live bug | Branched off `production`, merged into `production`, then back-merged into `staging` and `dev` |

## One-Line Summary

Code flows one way — `feature/* → dev → staging → production` — gated by review, CI, and approval at every step; the only exception is a `hotfix/*`, which jumps straight onto `production` and must be deliberately carried back down into `staging` and `dev`.

## Related

See [Rules.md](./Rules.md) for the golden rule and branch table, [Access-Control.md](./Access-Control.md) for who can approve each gate, and [Hotfix-Workflow.md](./Hotfix-Workflow.md) for the hotfix steps in detail.
the previous file — see [Sources-And-References.md](./Sources-And-References.md), section 6.

