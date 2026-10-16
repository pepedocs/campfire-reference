---
description: Report something new in a topic
argument-hint: <topic> <description>
model: haiku
---
Report a new entry. Don't read the whole topic file.

1. **Find the topic.** Run `grep -H '^Entry name:' topics/*.md`. Match the first word of the input against each file name and entry name, ignoring case and plurals (`risk`, `risks` → `topics/risks.md`). No match: reply with one line listing the topics and suggesting `/topic add`. Don't create topics.
2. **Next number.** Highest number seen in the file or in past report commits, plus one, so removed numbers are never reused:
   `{ grep -o '^- \*\*<Entry>-[0-9]*' topics/<topic>.md; git log --format=%s | grep -o '^\[campfire\] report <Entry>-[0-9]*'; } | grep -o '[0-9]*$' | sort -n | tail -1`
3. **Validation.** Ask: "how would someone verify this?" Don't write until answered.
4. **Append** without reading the file:
   ```
   cat >> topics/<topic>.md <<'EOF'
   - **<Entry>-<n>** (<git user.name>): <description>
     Validate: <validation>
   EOF
   ```
5. Commit: `[campfire] report <Entry>-<n>: <short summary>`. Show only the new entry, then ask: "push to <branch>?"

User input: $ARGUMENTS
