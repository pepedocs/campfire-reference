---
description: Mark an entry as resolved
argument-hint: <entry name> <resolution>
model: haiku
---
Resolve an entry in place. The input starts with the entry name (e.g., "Risk 1"), followed by the resolution. Don't read the whole topic file.

1. **Find it.** Get the topic from the entry name: `grep -l '^Entry name: <Entry>$' topics/*.md`. Then get its line: `grep -n '^- \*\*<Entry> <n>\*\*' <file>`. Not found: reply with one line saying so.
2. **Read only its two lines** (Read with offset and limit 2).
3. **Validation.** Ask: "how would someone verify this?" Don't write until answered. A short answer is fine.
4. **Edit them in place.** Start the description with "Resolved." and the resolution, and replace the validation line with the new answer. Add the resolver's name (`git config user.name`) if it's not already listed.
5. Commit: `[campfire] resolve <Entry> <n>: <short summary>`. Show only the two changed lines, then ask: "push to <branch>?"

User input: $ARGUMENTS
