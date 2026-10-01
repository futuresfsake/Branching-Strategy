# Sources & References

These Gitflow/branching documents were written with AI assistance, but every practice described in them is a documented, widely-used industry standard — not something invented for this project. This file maps each concept to where it comes from, so the reasoning can be checked independently.

**How to verify any of this yourself:** don't just trust this list — open the source, read the relevant section, and compare it to the claim. That's the actual answer to "is this real or made up": check the primary source, not the file that cites it.

## 1. The `feature → dev → staging → production` branch model

**Used in:** `README.md`, `Rules.md`, `Developer-Workflow.md`

This is the core idea of **Gitflow**, originally defined by Vincent Driessen in 2010.

- Vincent Driessen, "A successful Git branching model" — https://nvie.com/posts/a-successful-git-branching-model/
  (The original source. Defines `develop`, `feature/*`, `release`, `hotfix/*`, and `master`/`main` — the same roles used in these files, just with `staging` added as a common real-world variant.)
- Atlassian, "Gitflow Workflow" — https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow
  (Industry-standard explanation of the same model, widely used as team onboarding material.)

Note: many modern teams use lighter variants (GitHub Flow, trunk-based development) instead of full Gitflow. The multi-environment version in these files (`dev`/`staging`/`production` as mapped environments) is a common adaptation, not the only valid one — worth knowing if a teammate argues "we don't do Gitflow."

- GitHub, "GitHub flow" — https://docs.github.com/en/get-started/using-github/github-flow
- Trunk Based Development — https://trunkbaseddevelopment.com/

## 2. Never commit directly to protected branches; use Pull Requests + review + CI

**Used in:** `Access-Control.md`, `Developer-Workflow.md`, `Rules.md`

- GitHub Docs, "About protected branches" — https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches
- GitLab Docs, "Protected branches" — https://docs.gitlab.com/ee/user/project/protected_branches.html
- GitHub Docs, "About pull request reviews" — https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/about-pull-request-reviews

## 3. CODEOWNERS and required reviewers

**Used in:** `Access-Control.md`

- GitHub Docs, "About code owners" — https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners
- GitLab Docs, "Code Owners" — https://docs.gitlab.com/ee/user/project/codeowners/

## 4. "Bus factor" (why approval shouldn't rest on one person)

**Used in:** `Access-Control.md`

This is an established software engineering term, not a made-up concept.

- Wikipedia, "Bus factor" — https://en.wikipedia.org/wiki/Bus_factor
  (Summarizes the concept and its origin in software project risk management; cites earlier usage in software engineering literature.)

## 5. Hotfix branches off production, then back-merged into dev/staging

**Used in:** `Hotfix-Workflow.md`, `Developer-Workflow.md`

This is directly from the original Gitflow model's `hotfix` branch definition.

- Vincent Driessen, "A successful Git branching model" (same as above) — https://nvie.com/posts/a-successful-git-branching-model/
  (Explicitly describes: hotfix branches are created from the stable production branch, merged back into production, AND back-merged into develop so the fix isn't lost in the next release — exactly what's described in `Hotfix-Workflow.md`.)
- Atlassian, "Gitflow Workflow" (same as above) — https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

## 6. Keep feature branches short-lived and sync them with the base branch often

**Used in:** `Always-Pull-From-Dev-Dont-Be-Stupid.md`, `Pulling-From-Dev-Explained.md`, `Developer-Workflow.md`, `Rules.md`

This is a widely-cited best practice, closely associated with **Continuous Integration** itself (the "CI" in CI/CD) — the core idea of CI is literally "integrate frequently, in small batches, to catch conflicts early."

- Martin Fowler, "Continuous Integration" — https://martinfowler.com/articles/continuousIntegration.html
  (Describes the foundational reasoning: integrating infrequently causes large, painful merge conflicts; integrating often keeps them small. This is the direct justification for "always pull from dev regularly," not just "finish your branch and merge once.")
- Atlassian, "Git branching strategies" — https://www.atlassian.com/git/tutorials/comparing-workflows

## 7. Rollback as an alternative to a rushed hotfix

**Used in:** `Hotfix-Workflow.md`

- Google SRE Book, Chapter 16, "Managing Incidents" — https://sre.google/sre-book/managing-incidents/
  (Standard incident-response guidance: a safe rollback to the last known-good version is often preferable to writing a fix under time pressure.)

## File → Section Map

Quick lookup in the other direction — which sections above back up each file:

| File | Relevant section(s) |
|---|---|
| `README.md` | 1 |
| `Rules.md` | 1, 2, 6 |
| `Access-Control.md` | 2, 3, 4 |
| `Developer-Workflow.md` | 1, 2, 5, 6 |
| `Always-Pull-From-Dev-Dont-Be-Stupid.md` | 6 |
| `Pulling-From-Dev-Explained.md` | 6 |
| `Hotfix-Workflow.md` | 5, 7 |

## Honest Caveat

These sources describe **general, widely-adopted industry practice** — they are not your specific company's written policy (unless your company has explicitly adopted Gitflow, which you should confirm with a lead if it matters for the argument). What they do establish is: none of the practices in these files were invented from nothing. They match the original Gitflow spec, mainstream vendor documentation (GitHub/GitLab), and established engineering writing (Fowler, Google SRE). If a groupmate wants to argue a specific point, the right move is to go to the specific source above and look at the actual sentence being disputed — not just assert either way.
