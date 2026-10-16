Show a topic's status. Follow the "Status cache" rules in CLAUDE.md. Save tokens: don't read the topic file when a cache exists.

$ARGUMENTS must name a topic. If it's empty or doesn't match a topic, reply with one line: "Usage: /status <topic>" followed by the topic names. For an entry's full details, the user should use `/history <name>`.

1. If `status/<topic>.md` exists: show it with staleness markers and "Cached <time> · <N> changes since." If N > 0, ask: "use this, or get a fresh status?" and stop.
2. If the user asks for fresh: update the cache from the commits to `topics/<topic>.md` since the cache commit. Don't re-read the whole topic file.
3. If there's no cache: build it from the topic file.
4. After 2 or 3: commit the cache on its own, show it, then ask: "push to <branch>?"

User input: $ARGUMENTS
