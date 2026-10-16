# Token optimizations

Every message to the agent costs tokens: the fixed instructions sent
each time, whatever it reads, and whatever it writes. Campfire keeps
all three small.

## Fixed cost

- **Short `CLAUDE.md`.** It's sent with every message, so it holds only
  the rules every command shares: topics, entry format, strict mode,
  commit labels, branch. Each command's detailed steps live in its own
  file under `.claude/commands/`, which is loaded only when that command
  runs.
- **Cheaper model for simple commands.** `/report`, `/update`,
  `/resolve`, `/topic`, `/history`, `/branch` and `/campfire-help` set
  `model: haiku` in their frontmatter. `/status` and `/relate` use the
  default model because summarizing and judging relevance need it.

## Reading less

| Command | Reads |
|---|---|
| `/report` | Topic headers (`grep`) and the last entry number from the file and past commit labels. Appends the entry without reading the file. |
| `/update`, `/resolve` | The entry name gives the topic, and `grep -n` gives the line. Only those two lines are read and edited. |
| `/status` | The topic's status cache. A fresh status reads only the changes since the cache (`-U0`). The topic file is read in full once, when there's no cache. |
| `/relate` | One topic by default (`/relate <topic> ...`). The cache finds candidates, then only matching entries are read in full. `all` is an explicit, costlier opt-in. |
| `/topic list` | Header lines and `grep -c` counts, not whole files. |
| `/history` | One line per change by default. `full` adds the changed lines, without context. |
| `/campfire-help` | Nothing but the topics' purpose lines. The help text is fixed in the command. |

## Writing less

- After a write, the agent shows only the entry lines that changed.
- Git output is compact: short hashes, `--format`, `-q`, `--porcelain`.
- Strict command mode rules out chat, preamble and summaries.

## Trade-offs

- **`/relate` with one topic** misses related entries in other topics.
  Use `/relate all` when cross-topic links matter.
- **The `/campfire-help` text is fixed.** Adding a command means
  updating it by hand.
- **Haiku** follows the steps well, but may be weaker with odd input.
  Remove `model: haiku` from a command file to use the default model.
- **Status caches can lag.** A cached status is only as current as its
  last refresh. The "N changes since" line says how far behind it is.
