---
description: Update an existing entry
argument-hint: <entry name> <update>
model: haiku
---
Update an entry in place. The input starts with the entry name (e.g., "Risk 1"), followed by the update. Don't read the whole topic file.

1. **Find it.** Get the topic from the entry name: `grep -l '^Entry name: <Entry>$' topics/*.md`. Then get its line: `grep -n '^- \*\*<Entry> <n>\*\*' <file>`. Not found: reply with one line saying so.
2. **Read only its two lines** (Read with offset and limit 2).
3. **Edit them in place.** Add the updater's name (`git config user.name`) if it's not already listed. Change the validation line only if needed.
4. If the entry mentions another entry by name, say so in one line. Don't change the other entry.
5. Commit: `[campfire] update <Entry> <n>: <short summary>`. Show only the two changed lines, then ask: "push to <branch>?"

User input: $ARGUMENTS
