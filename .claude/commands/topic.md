List topics, or add a new one. Follow the "Adding topics" rules in CLAUDE.md.

If $ARGUMENTS is empty: list every file under `topics/` with its title, purpose, entry name, and how many entries it has (resolved and unresolved).

If $ARGUMENTS starts with "add": the rest is the topic name, optionally followed by its purpose. Create `topics/<name>.md` with the header (title, purpose, entry name) and no entries. If no purpose was given, ask: "what belongs in this topic?" Commit automatically. Then ask: "push to <branch>?"

User input: $ARGUMENTS
