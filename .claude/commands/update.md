---
description: Update an existing entry
argument-hint: <entry name> <update>
model: haiku
---
Update an entry in place. The input starts with the entry name (e.g., "Risk 1"), followed by the update. Don't read the whole topic file.

1. **Find it.** Get the topic from the entry name: `grep -l '^Entry name: <Entry>$' topics/*.md`. Then get its line: `grep -n '^- \*\*<Entry> <n>\*\*' <file>`. Not found: reply with one line saying so.
2. **Read only its two lines** (Read with offset and limit 2).
3. **Validation.** Ask: "how would someone verify this?" Don't write until answered. A short answer is fine.
4. **Edit them in place.** Rewrite the description with the update, and replace the validation line with the new answer. Add the updater's name (`git config user.name`) if it's not already listed.
5. If the entry mentions another entry by name, say so in one line. Don't change the other entry.
6. Commit: `[campfire] update <Entry> <n>: <short summary>`. Show only the two changed lines, then ask: "push to <branch>?"

User input: $ARGUMENTS
