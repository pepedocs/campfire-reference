Show the current state. Read all files (incidents.md, issues.md, upgrades.md, notes.md) and summarize.

If $ARGUMENTS is empty, show everything. If it's a name, show that entry's details including git history.

Check `git blame` for staleness. Flag entries older than 7 days.

User input: $ARGUMENTS
