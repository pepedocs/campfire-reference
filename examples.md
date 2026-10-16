# Examples

These examples assume Alice, Bob, Charlie, and Diana are on a team
preparing a product launch. Campfire doesn't assume any kind of team —
the topics below are the ones this team chose.

---

## Setting up topics

A new campfire has one topic, `notes`. The team adds the topics it
needs.

### Listing topics

> `/topic`

> **Notes** (`topics/notes.md`) — Anything the team should know that doesn't fit another topic. 0 entries.

### Adding a topic

> `/topic add risks Things that could delay or derail the launch`

Agent creates `topics/risks.md`, commits, then asks: *push to main?*

```markdown
# Risks

Purpose: Things that could delay or derail the launch.
Entry name: Risk
```

> `/topic add decisions Choices the team has made, and why`

```markdown
# Decisions

Purpose: Choices the team has made, and why.
Entry name: Decision
```

---

## Reporting

### New risk

> `/report risk The payment provider hasn't signed off on the new checkout flow`

Agent: *How would someone verify this?*

> `check the sign-off thread with the payment provider`

Agent writes to `topics/risks.md`, commits, then asks: *push to main?*

```markdown
- **Risk-1** (Alice): The payment provider hasn't signed off on the new checkout flow.
  Validate: Check the sign-off thread with the payment provider.
```

### New decision

> `/report decision Launch moves from the 3rd to the 10th to leave time for the provider review`

Agent: *How would someone verify this?*

> `the launch date on the project plan`

```markdown
- **Decision-1** (Bob): Launch moves from the 3rd to the 10th to leave time for the provider review. Related: Risk-1.
  Validate: Check the launch date on the project plan.
```

### New note

> `/report note Diana is out from the 6th to the 8th; Charlie covers support questions`

```markdown
- **Note-1** (Diana): Out from the 6th to the 8th. Charlie covers support questions.
  Validate: Check the team calendar.
```

### A topic that doesn't exist

> `/report bug The signup page is slow on mobile`

Agent: *There's no "bug" topic. Topics are: Notes, Risks, Decisions.
Report it under one of those, or add a topic with `/topic add`.*

---

## Updating

### Updating an entry

> `/update Risk-1 provider replied, review scheduled for the 5th`

Agent: *How would someone verify this?*

> `provider's calendar invite for the 5th`

Agent edits the entry in `topics/risks.md`, replacing its validation
line so it matches the new information:

```markdown
- **Risk-1** (Alice): The payment provider hasn't signed off on the new checkout flow. Review scheduled for the 5th.
  Validate: Provider's calendar invite for the 5th.
```

### A different person updates

> `/update Risk-1 review done, they asked for one change to the receipt email`

Agent: *How would someone verify this?*

> `review notes in the sign-off thread`

Agent adds Charlie's name since they're not the original reporter, and
mentions that Decision-1 refers to this risk:

```markdown
- **Risk-1** (Alice, Charlie): The payment provider hasn't signed off on the new checkout flow. Review done — one change requested to the receipt email.
  Validate: Review notes in the sign-off thread with the payment provider.
```

### Resolving an entry

> `/resolve Risk-1 receipt email fixed, provider signed off`

Agent: *How would someone verify this?*

> `sign-off thread, signed on the 7th`

```markdown
- **Risk-1** (Alice, Charlie): Resolved. The payment provider signed off on the new checkout flow after a fix to the receipt email.
  Validate: Check the sign-off thread with the payment provider — sign-off is on the 7th.
```

---

## Querying

### Finding related entries

> `/relate risks I'm about to change the checkout page copy`

Agent searches the risks topic — its status cache first, then the
full text of matching entries only — and returns:

> - **Risk-1** (Alice, Charlie): Resolved. The payment provider signed off on the new checkout flow after a fix to the receipt email.
>   Validate: Check the sign-off thread with the payment provider — sign-off is on the 7th.
>
> ⚠️ *These entries reflect what the team has reported. They may be incomplete, stale, or inaccurate. Verify before acting.*

### Checking status for the first time

> `/status risks`

There's no status yet, so the agent reads `topics/risks.md` once,
consolidates it into `status/risks.md`, commits it, and shows it:

> **Risks** — Open: 1 · Resolved: 1
> - **Launch date:** The provider signed off on checkout, so nothing blocks the 10th. *(Risk-1, Decision-1)* · Validate: sign-off thread, project plan.
> - **Traffic:** Launch-day load may exceed the current plan's limits. *(Risk-2)* · Validate: hosting plan limits page. ⏳ *5 days since last update.*

Agent: *push to main?*

The stored file is terse, for the agent:

```
open:1 resolved:1
Launch date: provider signed off on checkout → nothing blocks the 10th [Risk-1@2026-08-07, Decision-1@2026-08-03] v:sign-off thread, project plan
Traffic: launch-day load may exceed plan limits [Risk-2@2026-08-09] v:hosting plan limits page
```

### Checking status again

> `/status risks`

The agent shows the stored status without reading the topic file:

> **Risks** — Open: 1 · Resolved: 1
> - **Launch date:** The provider signed off on checkout, so nothing blocks the 10th. *(Risk-1, Decision-1)* · Validate: sign-off thread, project plan.
> - **Traffic:** Launch-day load may exceed the current plan's limits. *(Risk-2)* · Validate: hosting plan limits page. ⚠️ *7 days since last update.*
>
> Cached 2026-08-14 09:12 · 2 changes since.

Agent: *Use this, or get a fresh status?*

> `fresh`

The agent reads only the 2 commits to `topics/risks.md` since then,
folds them into the summary, commits it, and shows it:

> **Risks** — Open: 2 · Resolved: 1
> - **Launch date:** The provider signed off, but the app store review may take over a week, which could push past the 10th. *(Risk-1, Risk-3, Decision-1)* · Validate: app store review status, project plan.
> - **Traffic:** Load may exceed plan limits; an upgrade was requested. *(Risk-2)* · Validate: hosting plan limits page.
> - **Recheck:** the upgrade isn't confirmed yet. *(Risk-2)*

Agent: *push to main?*

If nothing changed since the status, the agent says it's current and
doesn't ask.

### Viewing full history

> `/history Risk-1`

Agent shows one line per change: who, when, and the commit message.
`/history Risk-1 full` also shows the changed lines.

---

## What Git captures

Every action above produces a commit, labelled so it can be told apart
from changes to campfire itself. To see only the team's knowledge:

```
$ git log --oneline --format='%h %an: %s' --grep='^\[campfire\]'

a1b2c3d Charlie: [campfire] resolve Risk-1: receipt email fixed, provider signed off
d4e5f6g Charlie: [campfire] update Risk-1: review done, one change requested
h7i8j9k Alice: [campfire] update Risk-1: review scheduled for the 5th
l0m1n2o Diana: [campfire] report Note-1: out from the 6th to the 8th
p3q4r5s Bob: [campfire] report Decision-1: launch moves to the 10th
t6u7v8w Alice: [campfire] report Risk-1: provider hasn't signed off on checkout
x9y0z1a Alice: [campfire] add topics: risks, decisions
```

Status cache commits are labelled `[campfire-status]`, and changes to
campfire itself have no label.

Who said what, when they said it, and exactly what changed — all auditable.
