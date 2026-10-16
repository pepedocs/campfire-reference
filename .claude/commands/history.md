---
description: Show the history of an entry
argument-hint: <entry name> [full]
model: haiku
---
Show who changed an entry, and when. The input is an entry name (e.g., "Risk-1"), optionally followed by "full".

1. Get the topic from the entry name: `grep -l '^Entry name: <Entry>$' topics/*.md`.
2. Summary (default) — one line per change, no diffs:
   `git log --format='%h %ad %an: %s' --date=short -G'\*\*<Entry>-<n>\*\*' -- <file>`
3. With "full", show the changed lines too, without context:
   `git log -p -U0 --format='%h %ad %an: %s' --date=short -G'\*\*<Entry>-<n>\*\*' -- <file>`

User input: $ARGUMENTS
