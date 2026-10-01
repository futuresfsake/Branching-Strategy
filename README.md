# Git Branching Strategy — Documentation Index

This folder documents our Gitflow-based branching strategy: which branches exist, who can touch them, and exactly how a developer should work day-to-day. Read the files in this order — each one builds on the last.

## Reading Order

1. **[Rules.md](./Rules.md)**
   The foundation. What each branch (`feature/*`, `dev`, `staging`, `production`) is for, who uses it, and the golden rule: code only flows one direction, `feature/* → dev → staging → production`.

2. **[Access-Control.md](./Access-Control.md)**
   Who's actually allowed to merge into each branch, and why it's a *role* (e.g. "release approvers") with more than one person in it, not a single individual — covers branch protection, required reviewers, and CODEOWNERS.

3. **[Developer-Workflow.md](./Developer-Workflow.md)**
   The step-by-step, command-by-command guide for one developer working on one feature: branch from `dev`, commit, sync, open a PR, merge, clean up.

4. **[Always-Pull-From-Dev-Dont-Be-Stupid.md](./Always-Pull-From-Dev-Dont-Be-Stupid.md)**
   Why "my branch is finished" or "I started first" are not valid reasons to skip syncing with `dev` — and what actually breaks when people skip this.

5. **[Pulling-From-Dev-Explained.md](./Pulling-From-Dev-Explained.md)**
   Goes deeper on the same point: `dev` keeps moving every time anyone merges, so pulling is a repeated habit for the life of your branch, not a one-time setup step.

6. **[Hotfix-Workflow.md](./Hotfix-Workflow.md)**
   The one exception to the normal flow: what to do when `production` breaks and needs an emergency fix, and why that fix must be deliberately back-merged into `staging` and `dev`.

7. **[Sources-And-References.md](./Sources-And-References.md)**
   Where every practice above comes from — links to the original Gitflow spec, GitHub/GitLab official docs, and established engineering writing (Martin Fowler, Google SRE). Use this if someone asks "is this actually standard practice, or did you just make it up?"

## Diagram

A visual overview of the full Gitflow model is also available at [`../Diagram/Gitflow.png`](../Diagram/Gitflow.png).

## One-Paragraph Summary

Every developer branches a `feature/*` off `dev`, pulls from `dev` regularly while working, and opens a PR back into `dev` — never committing directly to any protected branch. `dev` is promoted to `staging` for QA/UAT, and `staging` is promoted to `production` once approved — never the other way around. The only exception is a `hotfix/*`, branched off `production` for an urgent live bug, which must be manually merged back into both `production` and `dev` so the fix survives the next release.

## Sources

Every file in this folder ends with its own "Sources" section. [Sources-And-References.md](./Sources-And-References.md) is the master list, with a file-by-file map at the bottom of that document.
