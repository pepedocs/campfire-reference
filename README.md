# Campfire

Shared team knowledge, maintained by an LLM agent backed by Git.

Producers report what they know. The agent coordinates — it turns
those reports into one coherent, current view. Consumers read that
view instead of piecing it together themselves. Git remembers
everything.

No team member has to be the coordinator.

## Getting started

1. Clone the repo.
2. Start Claude Code from the repo directory:

```bash
cd campfire
claude
```

3. Add the topics your team needs (`/topic add`), then use slash
   commands to report and query:

```
/topic add risks Things that could delay or derail the launch
/report risk The payment provider hasn't signed off on checkout
/update Risk 1 review scheduled for the 5th
/resolve Risk 1 provider signed off
/status risks
/relate risks I'm about to change the checkout page copy
```

See [examples.md](examples.md) for full walkthroughs.

## Commands

| Command | Purpose |
|---------|---------|
| `/report <topic> <description>` | Report something new in a topic |
| `/topic [list]` | List topics |
| `/topic add <name> <purpose>` | Add a topic |
| `/update <name> <description>` | Update an existing entry |
| `/resolve <name> <description>` | Mark an entry as resolved |
| `/status <topic>` | Show a topic's status, cached or fresh |
| `/history <name> [full]` | Who changed an entry and when; `full` adds the changes |
| `/relate <topic\|all> <message>` | Find related entries in a topic (or all topics) |
| `/branch [name]` | Show or set the branch to commit and push to |
| `/campfire-help [command]` | List the available commands, or explain one |

---

## Why this exists

### The problem

Teams communicate through conversations, handovers, and tribal knowledge.
Many people produce information, and many people consume it. Consumers
don't want every raw report. They want a coherent view of the current
state: what's broken, what's known, what's changing.

Someone has to build that view. When a team is small and colocated, a
coordinator usually does it — a lead or project manager who hears
everything and keeps the picture straight. The problem appears when
there is no coordinator. Reports pile up in scattered places, and each
consumer is left to piece the view together alone:

- **No acknowledgement** — information may not reach everyone.
- **No freshness guarantee** — information goes stale without anyone noticing.
- **No durability** — information is lost when people leave or forget.

Without a coordinator, knowledge consistency is not guaranteed.
Distributed, asynchronous teams usually can't keep one.

### Collection works, consolidation doesn't

Sharing knowledge takes two steps:

1. **Information collection** — people report what they know. This is
   not the problem. It already happens through normal work.
2. **Information consolidation** — someone must continuously turn those
   reports into a coherent current view. This is the coordinator's job,
   and without a coordinator nobody does it.

We can't have a dedicated coordinator. Consolidation is what campfire
solves.

### The approach

```
User ←→ LLM agent ←→ Git repo
```

The user talks to the agent through slash commands. The agent maintains
one file per topic under `topics/` and commits every change to Git.

`/status <topic>` caches its output in `status/<topic>.md` and commits
it. The next time, the agent shows the cached status with its timestamp
and how many changes came after it, and asks whether that's good enough.
A fresh status starts from the cache and applies only the commits since,
so the topic file isn't re-read. Consumers can also open `status/`
directly in Git or on GitHub without running the agent. When someone queries, the agent reads
the files and git history to answer.

The agent is both the reader and the writer. The user never edits files
or runs git commands directly.

### Topics

A topic is a kind of knowledge the team wants to track: risks,
decisions, open questions, customer feedback, incidents — whatever fits
the team. Each topic is a file under `topics/` with a short header
saying what belongs in it. A new campfire starts with one topic,
`notes`, and the team adds the rest with `/topic add`.

Topics live in their own files, not in `CLAUDE.md`, so a team's
campfire can pull updates from the reference without conflicts.

### What Git provides

Git handles what would otherwise need custom infrastructure:

- **Ordering** — commit sequence establishes what happened when.
- **Ownership** — the committer owns the information.
- **History** — `git log` and `git blame` show who changed what, when.
- **Auditability** — immutable commit log. Nothing is lost.

Every commit the agent makes is labelled — `[campfire]` for knowledge,
`[campfire-status]` for status caches — so the team's knowledge history
can be read on its own with `git log --grep='^\[campfire\]'`, without
changes to campfire itself mixed in.

### What the agent provides

- Writes entries on behalf of users.
- Reads files and git history to answer questions.
- Finds entries relevant to what you're working on (`/relate`).
- Keeps token use low — see [optimizations.md](optimizations.md).
- Flags stale entries (older than 7 days).
- Requires validation methods so entries can be re-verified.
- Commits automatically, asks before pushing to the target branch (set with `/branch`).

### What validation means

Every entry has a "Validate:" line — a short instruction for how
someone can check whether the information is still true. The agent
does not perform the validation. It surfaces the method so anyone
can verify when needed.

### What the agent does not do

- Invent information.
- Resolve disagreements between team members.
- Verify facts.
- Treat entries as authoritative.
- Modify entries without being told to.
