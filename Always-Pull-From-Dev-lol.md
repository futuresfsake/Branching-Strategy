# Always Pull From Dev — No Excuses

This file exists because of a real, repeated mistake: a developer skips pulling the latest `dev` into their feature branch, and justifies it with an excuse. **None of these excuses are valid.** This file explains why.

## The Rule

**While your feature branch is open, you pull `dev` into it regularly — every day, or at minimum before you open your PR.** Not once. Not "at the start." Regularly, the whole time the branch is alive.

## The Excuses People Make (and Why They're Wrong)

### "My branch is already finished, I don't need to pull."

Wrong. "Finished" means your code compiles and does what you intended — it says nothing about whether it still works **together with everyone else's finished code.** While you were building your feature, other people merged theirs into `dev`. If you don't pull, you have no idea whether:
- You and someone else touched the same file in a conflicting way.
- Someone changed a function signature, config, or shared file your code depends on.
- Your "finished" feature actually breaks the moment it's combined with current `dev`.

Finished in isolation is not finished. Finished means finished **with everyone else's work included.**

### "My branch started first / I was here before the other changes."

Irrelevant. Git doesn't care about arrival order, and neither does the codebase. `dev` is a moving target — it keeps changing while you work. The question is never "who was first," it's "does my code work with `dev` as it is right now, today." If you refuse to pull because you "got there first," you're choosing to merge stale code into a branch that has moved on without you.

### "I don't want to deal with merge conflicts."

That's exactly backwards. Pulling `dev` often means conflicts show up **small and early** — a few lines, easy to resolve, while the change is fresh in your head. Avoiding pulls doesn't avoid the conflict. It just delays it until your PR is huge, old, touches a dozen files, and the person who could explain the conflicting change has moved on to something else. Small frequent conflicts are cheap. One giant conflict at the end is expensive and dangerous.

### "It worked on my machine / in my branch, that's good enough."

"Works on my branch" only proves your code works against a snapshot of `dev` that is already out of date. It proves nothing about the current, real `dev`. The only thing that matters is: does it work against `dev` **as it exists at merge time.**

## Why This Actually Matters

| If you skip pulling from `dev`... | This happens |
|---|---|
| Someone else changed a shared function | Your code silently breaks, or breaks CI, after merge |
| Someone else edited the same file | A conflict appears, but now it's buried under weeks of changes, not one afternoon's |
| Your branch sits open a long time without syncing | Review takes longer, since reviewers have to reason about a bigger, staler diff |
| Multiple people do this at once | `dev` becomes unstable, nobody trusts it, and the whole team slows down |

This is the entire reason `dev` exists in the first place: it's the one place where everyone's work is proven to work *together*. If you don't keep syncing with it, you're not really using `dev` — you're just working alone and hoping for the best.

## The Standard You're Held To

1. Pull `dev` into your feature branch regularly while it's open — not just once at the start.
2. Pull `dev` again right before opening your PR, even if you pulled yesterday.
3. If a conflict shows up, resolve it immediately, in that branch, with a review if it's non-trivial. Don't "fix it later."
4. "It's finished" and "I started first" are not reasons to skip a pull. They are not exceptions to this rule — they don't apply to it at all.

## One-Line Summary

**Being done with your code and being in sync with `dev` are two different things — you need both, and "I'm done" or "I started first" are not excuses to skip the second one.**

## Sources

This isn't just a house rule — it's the standard justification for Continuous Integration itself. See [Sources-And-References.md](./Sources-And-References.md), section 6.
