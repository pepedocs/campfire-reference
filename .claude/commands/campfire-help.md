List the available campfire commands. Don't read or change any entries, and don't run git.

Build the list from the files in `.claude/commands/`. Each file is one command, named after the file (`report.md` is `/report`). Read each file to work out its arguments and what it does, so the list stays accurate when commands are added or changed.

Reply with a table of every command, its usage, and a one-line purpose, grouped as:

- **Report**: `/report`, `/topic`
- **Change**: `/update`, `/resolve`
- **Look up**: `/status`, `/history`, `/relate`
- **Setup**: `/branch`, `/campfire-help`

Put any command that isn't in these groups under **Other**.

After the table, list the topics this campfire has: the title and purpose from each file under `topics/`.

If $ARGUMENTS names a command, show only that command: its usage, what it does, and what it will ask you for before writing (validation method, push confirmation).

User input: $ARGUMENTS
