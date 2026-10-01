



# Who Owns What: Access Control, Not People

A common misconception: "everyone owns `dev`, and one person manages `staging` and `production`."

That's close, but not quite right. The real model isn't about *people owning branches* — it's about **access control and gates**. Nobody, including leads, commits straight to `dev`, `staging`, or `production`. Those branches are **protected**.

## How It Actually Works

### 1. Everyone contributes to `dev` — through a gate, not directly

- Every developer works on their own `feature/*` branch.
- To get code into `dev`, they open a **Pull Request (PR)**.
- A teammate has to review it.
- **CI** (Continuous Integration — automated build and tests) has to pass.
- Only then does it get merged.

No one, not even senior engineers, pushes directly to `dev`.

### 2. Promotions to `staging` and `production` are more tightly controlled

- Usually a **tech lead, release manager, or senior developer** approves the promotion.
- Or the pipeline does it automatically once tests pass (fully automated CD).
- This is configured with:
  - **Branch protection rules** — blocks direct pushes, requires PRs.
  - **Required reviewers** — a PR can't merge until approved.
  - **CODEOWNERS files** — automatically assigns the right people as required reviewers for certain paths/branches.

(These settings live in GitHub or GitLab, under repository/branch settings.)

### 3. It should never depend on a single person

If only one person can approve a release and they're sick, on leave, or unreachable — nothing ships. This is called a low **"bus factor"** (how many people can be hit by a bus before the project stalls).

Good teams avoid this by:
- Creating a **role**, not naming an individual — e.g. "release approvers."
- Putting **at least two people** in that role.
- Letting anyone in the role approve, so no single point of failure exists.

## Summary

| Stage | Who can touch it | How changes get in |
|---|---|---|
| `feature/*` | The individual developer | Direct commits — it's their own branch |
| `dev` | Everyone (via PR only) | PR + peer review + passing CI |
| `staging` | Release approvers / automated pipeline | PR or automated promotion from `dev`, after `dev` is stable |
| `production` | Release approvers / automated pipeline | PR or automated promotion from `staging`, after sign-off/UAT |

**In short:** everyone contributes to `dev` through PRs, a small group (not one person) approves promotions to `staging` and `production`, and the pipeline does the actual deploying.
