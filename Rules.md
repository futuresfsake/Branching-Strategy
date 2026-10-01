# Branching Rules

A simple guide to our Git branches: what each one is for, and where **you** should pull from and push to.

See the diagram: `../Diagram/Gitflow.png`

## The Golden Rule

Code always flows **one direction**, like a river:

```
feature/* → dev → staging → production
```

Never skip a step. Never go backwards (except emergency hotfixes — see below).

## The Branches

| Branch | Environment | Who uses it | What it's for | Where you pull FROM | Where you push TO |
|---|---|---|---|---|---|
| `feature/*` | Your own machine, or a personal preview link | Just you, while you work on one task | Building or fixing ONE thing at a time, in isolation, so you don't break anyone else's work | `dev` (always start fresh from here) | A pull request **into** `dev` |
| `dev` (or `develop`) | The shared "Dev" server | The whole dev team | The meeting point — everyone's finished features land here first, so the team can check they all work together | `feature/*` branches (via pull requests) | A pull request **into** `staging`, once it's stable |
| `staging` | The "Staging" server (a copy of production) | QA, the product owner, testers | The dress rehearsal — final testing and sign-off (UAT) before anything goes live | `dev`, once it's been tested there | A pull request **into** `production`, once approved |
| `production` (or `main`) | The live site/app | Real customers | What is actually released and running right now | `staging`, once approved and signed off | Nowhere — this is the end of the line |

## Quick Answer: "Where do I branch from / merge into?"

**I'm starting a new feature or fix:**
1. Make sure your local `dev` is up to date.
2. Create `feature/your-feature-name` **from** `dev`.
3. When done, open a pull request **into** `dev`.

**I need to test if my feature works with everyone else's:**
- Look at `dev`. If your PR is merged there and the build passes, you're good.

**I need QA/the product owner to approve something before release:**
- That happens on `staging`. Someone (usually a lead) merges `dev` into `staging` when it's ready for testing.

**I need to release to real users:**
- That happens on `production`. Someone merges `staging` into `production` only after staging sign-off.

**Something is broken in production RIGHT NOW:**
- This is the one exception to the golden rule. Create a `hotfix/*` branch **from** `production`, fix it, then merge it back into **both** `production` and `dev` (so the fix isn't lost next release). Keep this for emergencies only.

## Rules to Remember

1. **Never commit directly to `dev`, `staging`, or `production`.** Always use a `feature/*` branch and a pull request, even for tiny changes.
2. **Never branch a feature off `staging` or `production`.** Always branch off `dev`.
3. **Keep feature branches small and short-lived.** One feature/fix per branch, merge it, delete it.
4. **`staging` should always look like `dev` did at some recent, tested point** — don't make changes directly on `staging`.
5. **`production` only ever receives code from `staging`** (except hotfixes).
6. **Delete your `feature/*` branch after it's merged.** It has done its job.

## Sources

This branch model and these rules are standard practice, not invented for this project — see [Sources-And-References.md](./Sources-And-References.md), sections 1, 2, and 6.
