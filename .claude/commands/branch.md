---
description: Show or set the branch campfire commits and pushes to
argument-hint: "[name]"
model: haiku
---
Show or set the branch that campfire commits and pushes to. The setting is stored in this clone's git config (`campfire.branch`), not in a committed file, so it doesn't change anyone else's target.

If $ARGUMENTS is empty: run `git config --get campfire.branch` and report the target branch. If it's unset, say so and report the branch currently checked out, which is what campfire uses by default.

If $ARGUMENTS is a branch name:

1. Check the name with `git check-ref-format --branch "<name>"`. If it's invalid, say so and stop.
2. If `git status --porcelain` shows uncommitted changes, list them and stop. Don't stash, commit or discard them.
3. Run `git fetch origin`, then switch to the branch:
   - If it exists locally: `git switch <name>`.
   - If only `origin/<name>` exists: `git switch --track origin/<name>`.
   - Otherwise, ask: "<name> doesn't exist. create it from the current branch?" Only on yes, run `git switch -c <name>`.
4. Once the switch succeeds, run `git config campfire.branch <name>`.
5. Reply with the new target branch and the latest commit on it.

User input: $ARGUMENTS
