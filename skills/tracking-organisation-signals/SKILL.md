---
name: tracking-organisation-signals
description: The shared method for tracking a named set of organisations against a defined set of event signals in Dcipher and delivering it as a recurring digest - building the trigger set, researching entities in parallel, assembling the organisation-by-signal matrix, reading it, and templating the digest so each period is a single call. Use directly when the user wants a set of organisations watched and none of the specific framings fits. Use tracking-competitor-moves when the organisations are rivals, tracking-customer-accounts when they are customers or prospects, monitoring-portfolio-companies when they are holdings or investees, screening-counterparty-risk when the signals sought are purely negative, and mapping-stakeholder-ecosystems when the question is who the actors are and how they relate rather than what they did.
---

# Tracking organisation signals

The shared machine behind every "watch these organisations for me" deliverable. The
specific skills - competitors, customers, portfolio holdings - add the trigger set, the
commercial reading and the audience; the build below is common to all of them.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## The four framing questions

**Which organisations, and what is missing from the list?** Users supply the obvious set.
Say what it omits - new entrants, adjacent-sector players, the subsidiary that actually
does the activity - and let them cut your additions.

**What is the relationship?** Rival, customer, holding, supplier, grantee, counterparty.
This determines what counts as good news, which is the whole interpretation layer. Ask if
it is not obvious; the wrong assumption inverts every conclusion.

**What counts as a signal?** Users name one trigger and mean several. Propose the full set
for the relationship, then cut. The triggers they did not ask about are where the surprises
are.

**One-off or standing?** If standing - and it usually is - everything below gets a
`schedule`, and the digest gets templated on the first run rather than the third.

## Build

**1. Research agents, not a news KB alone.** News measures attention, not activity; a
disciplined organisation that does not issue press releases will be invisible. Use
`create_research_agent_kb` with organisations and triggers as variables:

```json
{
  "kbName": "<set> - signal watch",
  "task": "Research $trigger at $organisation over the last <window>. Prioritise primary sources - company announcements, filings, regulator and court records - over secondary commentary. For each item give the date, a one-line factual description, the figure if disclosed, and the source URL. Report at most three items. If there was no significant activity of this type, write 'No significant activity identified' rather than reporting minor or unrelated news. Research in the local language of the organisation's home market.",
  "variables": [
    { "label": "organisation", "values": ["..."] },
    { "label": "trigger", "values": ["..."] }
  ],
  "multivariablePolicy": "combinations",
  "selectedSources": ["web", "news"]
}
```

Organisations x triggers is the run count. Above roughly 60, tell the user the size and
trim the **trigger** set rather than the organisation list - a focused read on the right
signals beats a broad read on irrelevant ones.

Add a scheduled news KB over the organisation names for continuous coverage between
research runs.

**2. Wait, then sample.** `wait_for_flow` until done, then `sample_kb` across
several entities. Check that cells carry dated events with URLs rather than summaries of
the organisation's about page, and that entities differ from one another. Near-identical
rows mean the agent is reasoning from general knowledge, not researching - tighten the
source constraints and re-run before building anything on top.

**3. Matrix.** Organisations as rows, triggers as columns. Put the longer list on rows;
matrices read better tall than wide.

```
instruction: "Summarise $row's activity in $column over the last <window> using only the
attached sources. Give at most three concrete items, each with a date and source. Then add
one line stating what this implies for <the user's relationship to these organisations>.
If the sources show no significant activity, write 'No significant activity identified'
and nothing further - do not infer activity from adjacent or unrelated news."
summarizeRows: true
summarizeColumns: true
```

The "state that nothing was found" clause is what stops every cell filling with plausible
fiction. Never omit it. `summarizeRows` gives the per-organisation verdict and
`summarizeColumns` gives "who is most active on this signal" - both are what gets quoted.

**4. Movement, where the data allows.** A bump workbench with `sourceType: "document"` over
the organisation field shows who is gaining share of activity. Needs timestamps and enough
history; skip it rather than producing noise.

**5. Template on the first run.** This deliverable exists to repeat. Build the digest once
as a report, agree the format with the user, then `create_insight_booster_report_template`
and schedule the KBs to the digest cadence. See `../dcipher-mechanics/references/reports.md`.

## Reading it like an analyst

- **Lead with the delta.** The audience already knows these organisations. The value is
  what changed since last period, which is the argument for templating early.
- **Empty cells are findings.** A year of no partnership activity says something. Report
  the silence rather than leaving a blank.
- **Compare across the row.** "Who is moving fastest" beats "what did each one do".
- **Sequence matters.** A leadership change followed by a divestment is a strategy shift;
  either alone is noise.
- **Separate announced from delivered.** Press releases describe intent.
- **Coverage is uneven by design.** Large listed organisations generate constant signal,
  small private ones almost none. Say when an entity is quiet because it is private rather
  than inactive, or the digest silently becomes a list of the biggest names.

## Push back on

- A flat unprioritised list of hundreds of organisations. Tier it and research fewer,
  deeper.
- A generic trigger set when the user can say what has historically preceded the outcome
  they care about.
- Inferring intent from a single item.
- Tracking named individuals rather than organisations - see the data policy in
  `analyst-standards.md`.

## Deliver

Per organisation, in priority order: what changed, what it implies, what to do, and the
source. Keep each entry short - this gets read before a meeting, not studied. Then the
cross-cutting view: which signals are firing across the set, and who has gone quiet. Then
set the digest running.
