# Review notes - what we need decided

This draft was written from the MCP tool schemas and the public supported-tools page. The
mechanics are transcribed from real schemas; the **analytical defaults are proposals**, and
those are what the team needs to correct.

## 1. Verify the sequencing claims

Each of these is asserted in a skill and none has been run end to end:

- A workbench must exist and be configured before `get_*_result` returns anything useful.
- `get_last_flow_run_status` is the correct and only polling call for all four KB
  constructors.
- A scenario workbench needs a radar that has actually been *read* (result generated), not
  merely configured.
- `growth` workbenches need no config call - `broadness_level` at read time is sufficient.
- Attaching a KB after a workbench is configured does not require reconfiguring it.

## 2. Analytical defaults to confirm or replace

These are the opinions in the skills. Each is a judgement call we made; replace them with
what the team actually recommends to customers.

| Where | Default proposed | Confirm? |
|---|---|---|
| competitor tracking | research agents over news KB, because news measures attention not activity | |
| competitor tracking | 12-month window; monthly schedule | |
| customer voice | `outlierPolicy: "never"` - small clusters are emerging issues | |
| market landscape | `colorField` = company, to turn a topic map into a positioning map | |
| foresight radar | `bottom-up` unless the client has a taxonomy; `enableAdditionalSegments: true` always | |
| foresight radar | `momentum` sizing needs "several periods" of history - what is the real minimum? | |
| landscape reads | start one or two broadness levels below max for exec, 1-2 for specifics | |
| scenarios | 2-4 dimensions; more than 4 is unreadable | |
| matrix cells | four-level scale with explicit `Unknown` for screening | |
| reports | `gpt-5.5` used in examples - which engine should we actually recommend per section type? | |
| news KB | `limit` 10000-20000 for a broad area - is this sane on cost? | |

## 3. Things we may have got wrong

- **`momentum` semantics.** Described as "relative rate of acceleration over the selected
  period". Skills tell Claude to refuse momentum sizing on short corpora. Is there a real
  minimum document count or time span?
- **Radar axis quality.** We push hard on client-specific axis definitions
  ("Threat to our aftermarket revenue") over generic ones. Does the generation actually
  handle that well, or does it prefer generic metrics?
- **Semantic filters.** `projects-and-filters.md` recommends broad KB + semantic filter over
  narrow keyword queries, so the KB stays reusable. Is that right operationally, or does it
  cost too much at query time?
- **Scheduled KBs.** Every run creates a new timestamp-suffixed KB. Do those auto-attach to
  the project when `insightBoosterId` was set at creation, and does the project then span
  all runs? Several skills assume yes.
- **File KBs have no timestamps.** Stated as a hard constraint that blocks bump and growth.
  Correct? It shapes the whole customer-voice skill.

## 4. Scope questions

- **Three skills have no Solutions page behind them** - `screening-acquisition-targets`,
  `tracking-regulatory-change`, `scouting-technologies`. The tools support them and the BI
  budget sits there, but check them against the actual customer base before we ship.
- **Should `stress-testing-strategy` ship at all in v1?** It has a hard prerequisite (a
  populated radar), which makes it the most likely skill to fail cold.
- **Do we want a report-generation skill?** Currently report generation is machinery
  referenced by every use-case skill rather than a user-facing skill. A user who says
  "write me a report on X" has to be routed by one of the others. This may be a gap.

## 5. Rollout

The recommendation is **not to ship all twelve at once**. Adding a near-neighbour skill
degrades its neighbours' triggering, and that is only visible if you test for it.

Proposed order:

1. `querying-knowledge-bases` + `managing-insight-projects` - role-neutral, no collisions,
   highest volume, cheapest to get right.
2. `tracking-competitor-moves` + `analyzing-customer-voice` - far apart in trigger space.
   Get the shared reference layer right against these two.
3. Then one at a time from the crowded middle: `scanning-emerging-trends`,
   `mapping-market-landscapes`, `scouting-technologies`, `monitoring-narratives` - checking
   after each that the earlier ones still fire.
4. `screening-acquisition-targets`, `tracking-regulatory-change`, `stress-testing-strategy`
   last.

## 6. Unresolved: the "report" gap in trigger space

"Write me a competitor report", "give me a market report" and "produce a briefing on X" are
likely real prompts. Right now they match several skills weakly rather than one strongly.
Options: (a) add a `writing-insight-reports` skill, (b) add report-request phrasing to each
use-case description, (c) leave it and see what the evals say. Needs a decision.
