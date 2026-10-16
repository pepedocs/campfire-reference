---
description: Find entries in a topic related to what you're doing
argument-hint: <topic|all> <what you're doing>
---
Return entries related to what the user describes. The input starts with a topic (or "all"), followed by the description. Search one topic by default — "all" searches every topic and costs more, so use it only when asked.

For each topic searched:

1. **Use the cache to find candidates.** If `status/<topic>.md` exists, read it — one line per entry. Also read any changes since it: `git log -p -U0 --format=%h <cache commit>..HEAD -- topics/<topic>.md`. If there's no cache, read the topic file instead.
2. **Read only the matches in full.** For each candidate, `grep -n -A1 '^- \*\*<Entry>-<n>\*\*' topics/<topic>.md` to get its description and validation lines.
3. **Follow references** only for entries named in the matches, the same way.

Reply with the matching entries (both lines, with staleness markers: 5+ days since the last update is "⏳ almost stale", 7+ days is "⚠️ stale"), then this line:

⚠️ *These entries reflect what the team has reported. They may be incomplete, stale, or inaccurate. Verify before acting.*

No commentary. If nothing matches: "No relevant entries found."

User input: $ARGUMENTS
