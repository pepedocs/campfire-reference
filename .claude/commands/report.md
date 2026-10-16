Report something new in a topic. Follow the "Adding" rules in CLAUDE.md.

The input starts with the topic (e.g., "note", "risks"), followed by the description. Match it against the topics under `topics/`. If it doesn't match a topic, list the topics and suggest `/topic add`.

Assign the next sequential number using the topic's entry name (Note 1, Note 2, etc.). Include the reporter's name from `git config user.name`.

Before writing, ask for a validation method: "how would someone verify this?" Do not write until the user provides one.

Write a two-line entry (description + validation). Commit automatically. Then ask: "push to <branch>?"

User input: $ARGUMENTS
