# Simulation: emergency department

A fictional ER team using campfire from Thu 8 Oct to Fri 16 Oct 2026
(about 75 commits).
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
- **Fri day shift — code orange:** a bus rollover sends 17 patients in.
  The ED goes on diversion (Capacity 5), electives are suspended
  (Capacity 6), a surge unit opens (Capacity 7), and triage reports
  Cases 5–11 within half an hour. Priya and Elena report at the same
  minute again (Cases 7 and 8). The massive transfusion for Case 8
  drains O-negative blood (Alert 4). A portable X-ray fails
  (Equipment 2). Code orange stands down at 14:00, and Marcus books a
  debrief (Note 1). Status caches are refreshed mid-incident and at
  the end of the shift.

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

- `/status cases`: the cache is current, so it should say so and not
  ask. Then run `/update Case 8 packing removed, pelvis fixed` (it asks
  for a validation, e.g. "op note"), and `/status cases` again: it
  should show the cache with 1 change since and offer a fresh status.
- `/status notes`: there's no cache yet, so it should build one from
  the topic file.
- `/status alerts`: Alert 2 (sugammadex, reported 14 Oct) should be
  close to "⏳ almost stale" depending on the day you run it, and show
  its validation.
- `/resolve Case 10 observation done, discharged home`: should ask for a
  validation before writing.
- `/relate equipment CT for a head injury`: should find Equipment 1.

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

Status caches were rebuilt on 16 Oct to include a validation for every
entry. Each piece of information has its own validation — the entries
in `topics/` always had one, and the caches now carry it too.
