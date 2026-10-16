---
description: List the campfire commands
argument-hint: "[command]"
model: haiku
---
Reply with this help text exactly. Don't read any files except for the topic list at the end.

| Command | Purpose |
|---|---|
| **Report** | |
| `/report <topic> <description>` | Add a new entry to a topic |
| `/topic [list]` | List topics with open and resolved counts |
| `/topic add <name> <purpose>` | Add a topic |
| **Change** | |
| `/update <entry> <update>` | Update an entry in place |
| `/resolve <entry> <resolution>` | Mark an entry as resolved |
| **Look up** | |
| `/status <topic>` | Show a topic's status, cached or fresh |
| `/history <entry> [full]` | Who changed an entry and when; "full" adds the changes |
| `/relate <topic\|all> <what you're doing>` | Find related entries in a topic |
| **Setup** | |
| `/branch [name]` | Show or set the branch to commit and push to |
| `/campfire-help [command]` | This help |

Then list the topics from `grep -H '^Purpose:' topics/*.md`, one line each.

If $ARGUMENTS names a command, show only its row, plus what it asks before writing (the validation method for `/report`, `/update` and `/resolve`, the purpose for `/topic add`, push confirmation for anything that commits).

User input: $ARGUMENTS
