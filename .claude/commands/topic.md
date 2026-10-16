---
description: List topics, or add a new one
argument-hint: "[list | add <name> <purpose>]"
model: haiku
---
If $ARGUMENTS is empty or "list": don't read topic files in full. Run:
`for f in topics/*.md; do echo "$f|$(sed -n 1p $f)|$(grep -m1 '^Purpose:' $f)|$(grep -c '^- \*\*' $f)|$(grep -c '^- \*\*[^*]*\*\* ([^)]*): Resolved\.' $f)"; done`
and reply with a table: topic, purpose, open entries, resolved entries.

If $ARGUMENTS starts with "add": the rest is the topic name, optionally followed by its purpose.

1. Name the file after the plural title in lowercase with hyphens: `topics/open-questions.md`.
2. If no purpose was given, ask: "what belongs in this topic?"
3. If an existing topic's purpose overlaps, say which one and stop.
4. Write the header only: `# <Title>`, a blank line, `Purpose: <purpose>`, `Entry name: <Singular>`.
5. Commit: `[campfire] add topic: <name>`. Then ask: "push to <branch>?"

User input: $ARGUMENTS
