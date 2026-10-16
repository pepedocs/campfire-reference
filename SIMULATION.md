# Simulation: emergency department

A fictional ER team using campfire from Thu 8 Oct to Fri 16 Oct 2026.
Every person, case and event is made up. Each commit is authored by the
staff member who made the change, at the time they made it.

## Cast

| Person | Role |
|---|---|
| Marcus Lee | Charge nurse — sets up topics, runs `/status` at handover, removes resolved entries |
| Priya Raman | Triage nurse |
| Elena Ortiz | ED physician |
| Sam Okafor | Trauma / general surgeon |
| Hana Kim | Anesthesiologist |
| Tom Becker | OR coordinator |
| Grace Ali | Bed manager |
| Ruth Mensah | Infection control |
| Leo Duarte | Biomedical engineering |

## Topics

`capacity`, `equipment`, `cases` (patients by bay only, never by name),
`alerts`, plus the default `notes`.

## What happens

- **Thu evening:** CT 2 goes down. The waiting room fills and admitted
  patients board in the ED. A perforated appendix (Case 1) gets booked
  for the OR, then is bumped by a motorcycle trauma (Case 2) because
  only one OR is staffed overnight (Capacity 3). Measles exposure in the
  waiting room (Alert 1).
- **Thu 23:45:** Priya and Elena report cases a minute apart from
  different workstations (Case 2 and Case 3).
- **Fri–Mon:** cases are operated on and stepped down. Boarding clears.
  The measles contacts are traced and cleared. CT 2's part is
  backordered.
- **Tue:** Marcus removes entries resolved on 9 Oct.
- **Wed–Thu:** sugammadex shortage (Alert 2). Hip fracture on a blood
  thinner (Case 4).
- **Fri 16 Oct handover:** Marcus runs `/status equipment` (no cache,
  so it's built) and `/status cases` (cache from 9 Oct, refreshed from
  the commits since).

## Things to check

```bash
# The team's knowledge history only (no status caches, no campfire changes)
git log --format='%ad %an: %s' --date=format:'%a %d %H:%M' --grep='^\[campfire\]'

# Status cache commits only
git log --oneline --grep='^\[campfire-status\]'

# One case's life, across five people
git log -p --format='%ad %an' --date=short -S'**Case 1**' -- topics/cases.md

# Entry ages that drive staleness
git blame --date=short topics/capacity.md topics/equipment.md
```

In Claude, from this branch:

- `/status capacity`: the cache is from Fri 9 Oct with 4 changes since,
  so it should offer a fresh status. Capacity 3 should show as
  "⚠️ stale".
- `/status equipment`: Equipment 1 should show as "⏳ almost stale",
  and its "part due ~15 Oct" has already passed.
- `/relate CT for a head injury in Bay 6`: should find Equipment 1.

## Gaps this surfaced

1. **Numbering race.** When two people report at the same moment from
   separate clones, both agents pick the same next number. Here Elena's
   agent took Case 3 because Priya's commit had landed first. With real
   clones, the second push is rejected, and nothing in the rules says
   to pull and renumber.
2. **One staleness threshold doesn't fit every topic.** 5 and 7 days
   is far too slow for cases and capacity, which change hourly, and
   about right for equipment. Topics probably need their own thresholds.
3. **Sensitive data.** The `cases` purpose line keeps patient names
   out, but only by convention. Campfire has no general rule about
   what must never be written into a topic.
