# Campfire

Shared team knowledge, maintained by an LLM agent backed by Git.

You talk. The agent writes. Git remembers everything.

## Getting started

1. Clone the repo.
2. Start Claude Code from the repo directory:

```bash
cd campfire
claude
```

3. Use slash commands to report and query:

```
/incident CrashLoopBackOff on payment-api pods in prod-east/payments
/update Incident 1 mitigated, scaled replicas to 8
/resolve Incident 1 pods healthy, root cause was OOM
/status
/relate I'm going to update certificates on prod-east
```

See [examples.md](examples.md) for full walkthroughs.

## Commands

| Command | Purpose |
|---------|---------|
| `/incident <description>` | Report a new incident |
| `/issue <description>` | Report a new issue |
| `/upgrade <description>` | Report an upgrade or change |
| `/note <description>` | Add a note |
| `/update <name> <description>` | Update an existing entry |
| `/resolve <name> <description>` | Mark an entry as resolved |
| `/status` | Show current state of everything |
| `/status <name>` | Show a specific entry with history |
| `/history <name>` | Show full history of an entry |
| `/relate <message>` | Find entries relevant to what you're doing |

---

## Why this exists

### The problem

Teams communicate through conversations, handovers, and tribal knowledge.
This works when the team is small and colocated. It breaks down when the
team is distributed and asynchronous:

- **No acknowledgement** — information may not reach everyone.
- **No freshness guarantee** — information goes stale without anyone noticing.
- **No durability** — information is lost when people leave or forget.

Knowledge consistency is not guaranteed.

### Two sub-problems

1. **Information collection** — people report what they know. This already
   happens through normal work.
2. **Information consolidation** — someone must continuously turn those
   reports into a coherent current view.

We can't have a dedicated coordinator. The second problem is what
campfire solves.

### The approach

```
User ←→ LLM agent ←→ Git repo
```

The user talks to the agent through slash commands. The agent maintains
four files — `incidents.md`, `issues.md`, `upgrades.md`, `notes.md` —
and commits every change to Git. When someone queries, the agent reads
the files and git history to answer.

The agent is both the reader and the writer. The user never edits files
or runs git commands directly.

### What Git provides

Git handles what would otherwise need custom infrastructure:

- **Ordering** — commit sequence establishes what happened when.
- **Ownership** — the committer owns the information.
- **History** — `git log` and `git blame` show who changed what, when.
- **Auditability** — immutable commit log. Nothing is lost.

### What the agent provides

- Writes entries on behalf of users.
- Reads files and git history to answer questions.
- Finds entries relevant to what you're working on (`/relate`).
- Flags stale entries (older than 7 days).
- Requires validation methods so entries can be re-verified.
- Commits automatically, asks before pushing to the target branch (set with `/branch`).

### What validation means

Every entry has a "Validate:" line — a short instruction for how
someone can check whether the information is still true. The agent
does not perform the validation. It surfaces the method so an
engineer can verify when needed.

### What the agent does not do

- Invent information.
- Resolve disagreements between team members.
- Verify facts.
- Treat entries as authoritative.
- Modify entries without being told to.
