---
description: Show a topic's consolidated status from `status/<topic>.md`. Never read the whole topic file when a status exists.

$ARGUMENTS must name a topic (match it like `/report` does). If it's empty or unknown, reply with one line: "Usage: /status <topic>" and the topic names. For one entry's history, use `/history <name>`.

**What status is:** a summary of what's true now for the topic, built from its entries. It groups related entries, draws consequences across them, marks what a newer entry superseded, and flags conflicts and things to recheck. It sits on top of the entries and never replaces them: never edit `topics/` while building it.

**Format** (for the agent, not people — keep it terse):
```
open:1 resolved:2
Launch date: provider sign-off done, launch on the 10th as planned [Risk-1@2026-08-07, Decision-1@2026-08-03] v:sign-off thread, project plan
Traffic: launch-day load may exceed plan limits; upgrade requested [Risk-2@2026-08-15] v:hosting plan limits page
Recheck: upgrade has no confirmation yet [Risk-2]
```
- One line per theme: `<label>: <summary> [<sources>] v:<validations>`. Each source is `<entry>@<date of its last change>`.
- Every open entry appears in at least one line. Resolved entries can share a line.
- Condense the wording, never the content: keep each entry's facts, reasons and caveats. Drop a point only when a newer entry supersedes it, and say so (`CT-2 down → fixed 15 Oct`). Keep both sides of a conflict.
- Every line keeps its validation. Store dates, not staleness markers.

**Steps:**

1. **Status exists:** `git log -1 --format='%h %ci' -- status/<topic>.md` gives its commit and time. Count changes since with `git rev-list --count <commit>..HEAD -- topics/<topic>.md`. Show the status (see Showing) and "Cached <time> · <N> changes since." If N is 0, say it's current and stop. Otherwise ask: "use this, or get a fresh status?" and stop.
2. **Fresh:** read only the changes since the status:
   `git log -p -U0 --format='%h %an %ad' --date=short <commit>..HEAD -- topics/<topic>.md`
   Fold them into the existing summary: update the lines whose sources changed, add new entries to a fitting line or a new one, remove removed entries, update counts and source dates (each changed entry's date is its commit's date). Re-check the other lines only for consequences of the changes.
3. **No status:** the only time to read the whole topic file. Build the summary, taking source dates from `git blame --date=short`.
4. After 2 or 3: commit `[campfire-status] update status for <topic>`, show it, then ask: "push to <branch>?"

**Showing:** render it for people as short plain sentences, one bullet per line, with its validation, and the counts on top. Mark a line by its oldest open source: 5+ days since its last change is "⏳ almost stale", 7+ days is "⚠️ stale" (always show the validation for these). Resolved entries are never stale.

**Conflicts:** if a pull or push conflicts on a status file, resolve the topic file first, then rebuild the status from it.

User input: $ARGUMENTS
