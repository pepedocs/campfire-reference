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
- **Risk 1** (Alice): The payment provider hasn't signed off on the new checkout flow.
  Validate: Check the sign-off thread with the payment provider.
```

### New decision

> `/report decision Launch moves from the 3rd to the 10th to leave time for the provider review`

Agent: *How would someone verify this?*

> `the launch date on the project plan`

```markdown
- **Decision 1** (Bob): Launch moves from the 3rd to the 10th to leave time for the provider review. Related: Risk 1.
  Validate: Check the launch date on the project plan.
```

### New note

> `/report note Diana is out from the 6th to the 8th; Charlie covers support questions`

```markdown
- **Note 1** (Diana): Out from the 6th to the 8th. Charlie covers support questions.
  Validate: Check the team calendar.
```

### A topic that doesn't exist

> `/report bug The signup page is slow on mobile`

Agent: *There's no "bug" topic. Topics are: Notes, Risks, Decisions.
Report it under one of those, or add a topic with `/topic add`.*

---

## Updating

### Updating an entry

> `/update Risk 1 provider replied, review scheduled for the 5th`

Agent edits the existing line in `topics/risks.md`:

```markdown
- **Risk 1** (Alice): The payment provider hasn't signed off on the new checkout flow. Review scheduled for the 5th.
  Validate: Check the sign-off thread with the payment provider.
```

### A different person updates

> `/update Risk 1 review done, they asked for one change to the receipt email`

Agent adds Charlie's name since he's not the original reporter, and
mentions that Decision 1 refers to this risk:

```markdown
- **Risk 1** (Alice, Charlie): The payment provider hasn't signed off on the new checkout flow. Review done — one change requested to the receipt email.
  Validate: Check the sign-off thread with the payment provider.
```

### Resolving an entry

> `/resolve Risk 1 receipt email fixed, provider signed off`

```markdown
- **Risk 1** (Alice, Charlie): Resolved. The payment provider signed off on the new checkout flow after a fix to the receipt email.
  Validate: Check the sign-off thread with the payment provider — sign-off is on the 7th.
```

---

## Querying

### Finding related entries

> `/relate I'm about to change the checkout page copy`

Agent searches all topics and returns:

> - **Risk 1** (Alice, Charlie): Resolved. The payment provider signed off on the new checkout flow after a fix to the receipt email.
>   Validate: Check the sign-off thread with the payment provider — sign-off is on the 7th.
>
> ⚠️ *These entries reflect what the team has reported. They may be incomplete, stale, or inaccurate. Verify before acting.*

### Checking status for the first time

> `/status risks`

There's no cache yet, so the agent reads `topics/risks.md` once,
writes `status/risks.md`, commits it, and shows it:

> **Risks status** — Open: 1 · Resolved: 1
> - **Risk 2** (Bob) · updated 2026-08-09: Launch-day traffic may exceed the current plan's limits. ⏳ *5 days since last update.*
> - **Risk 1** (Alice, Charlie) · updated 2026-08-07: Resolved. Provider signed off on checkout.

Agent: *push to main?*

### Checking status again

> `/status risks`

The agent shows the cache without reading the topic file:

> **Risks status** — Open: 1 · Resolved: 1
> - **Risk 2** (Bob) · updated 2026-08-09: Launch-day traffic may exceed the current plan's limits. ⚠️ *7 days since last update.*
> - **Risk 1** (Alice, Charlie) · updated 2026-08-07: Resolved. Provider signed off on checkout.
>
> Cached 2026-08-14 09:12 · 2 changes since.

Agent: *Use this, or get a fresh status?*

> `fresh`

The agent reads only the 2 commits to `topics/risks.md` since the
cache, applies them, commits the updated `status/risks.md`, and shows
it:

> **Risks status** — Open: 2 · Resolved: 1
> - **Risk 3** (Diana) · updated 2026-08-16: The app store review may take longer than a week.
> - **Risk 2** (Bob, Charlie) · updated 2026-08-15: Launch-day traffic may exceed the current plan's limits. Upgrade requested.
> - **Risk 1** (Alice, Charlie) · updated 2026-08-07: Resolved. Provider signed off on checkout.

Agent: *push to main?*

If nothing changed since the cache, the agent says the cache is
current and doesn't ask.

### Viewing full history

> `/history Risk 1`

Agent shows the complete git log with diffs for that entry.

---

## What Git captures

Every action above produces a commit. The full trail is always available:

```
$ git log --oneline

a1b2c3d Charlie: resolve Risk 1: receipt email fixed, provider signed off
d4e5f6g Charlie: update Risk 1: review done, one change requested
h7i8j9k Alice: update Risk 1: review scheduled for the 5th
l0m1n2o Diana: report Note 1: out from the 6th to the 8th
p3q4r5s Bob: report Decision 1: launch moves to the 10th
t6u7v8w Alice: report Risk 1: provider hasn't signed off on checkout
x9y0z1a Alice: add topics: risks, decisions
```

Who said what, when they said it, and exactly what changed — all auditable.
