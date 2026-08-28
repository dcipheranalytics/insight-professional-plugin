---
name: tracking-competitor-moves
description: Tracks a named set of competitors against event triggers - funding, product launches, leadership changes, M&A, partnerships, patents, regulatory action - and produces a competitor-by-trigger matrix or event digest in Dcipher Insight Booster. Use when the user names specific companies and wants to know what they have been doing, or wants ongoing monitoring of known rivals. Use screening-acquisition-targets instead when the companies are unknown and must be found, mapping-market-landscapes when the subject is a market or segment rather than named firms, and monitoring-narratives when the user wants how competitors are being covered rather than what they did.
---

# Tracking competitor moves

You are a competitive intelligence analyst. The deliverable is a competitor-by-trigger
matrix that a strategy team can read in five minutes and act on, refreshed on a cadence.

Read `../dcipher-mechanics/references/analyst-standards.md` once before your first
delivery in a session. Mechanics live in `../dcipher-mechanics/references/`.

## Frame the scan

Two things must be explicit before you build: **who** and **what counts as a move**.

**Who.** Take the user's list, then say what it is missing. A competitor set drawn from
the incumbent field usually omits new entrants, adjacent-industry attackers, and the
suppliers or channel partners who could integrate forward. Propose the additions and let
the user cut them.

**What counts.** Users typically name one trigger ("what have they launched"). Cover the
standard set unless they rule some out - the ones they did not ask about are where the
surprises are:

| Trigger | Why it matters | Where the signal is |
|---|---|---|
| Funding and investment | capacity and intent | filings, press releases, trade press |
| Product and service launches | direct market overlap | company sites, trade press |
| M&A and divestments | strategic direction | filings, regulatory notices |
| Partnerships and alliances | capability gaps they are filling | joint press releases |
| Leadership and org changes | strategy shifts precede them | company sites, LinkedIn-adjacent coverage |
| Patents and R&D | 18-36 month leading indicator | patent offices, publications |
| Regulatory and litigation | constraints and exposure | regulator publications, court records |
| Hiring and footprint | quiet expansion, no announcement | careers pages, local press |

Also settle the window (12 months is the usual default) and whether this is a one-off or a
standing watch. If standing, everything below gets a `schedule`.

## Build

**1. Source with research agents, not news.** News coverage measures attention, not
activity. A competitor can be highly active and quiet in the press, and a news KB will
under-report exactly the disciplined competitor the user should worry about. Use
`create_research_agent_kb` with companies and triggers as variables:

```json
{
  "kbName": "<sector> competitor watch",
  "task": "Research $trigger by $company over the last 12 months. Prioritise primary sources - company announcements, filings, regulator publications - over secondary commentary. For each item give the date, a one-line factual description, the figure if one was disclosed, and the source URL. Report at most five items. If there was no significant activity of this type, state 'No significant activity identified' rather than reporting minor or unrelated news. Research in the local language where the company is headquartered.",
  "variables": [
    { "label": "company", "values": ["..."] },
    { "label": "trigger", "values": ["funding and investment", "product launches", "M&A", "partnerships", "leadership changes"] }
  ],
  "multivariablePolicy": "combinations",
  "selectedSources": ["web", "news"]
}
```

Check the run count before firing: companies x triggers. Above roughly 60, confirm with
the user or trim the trigger set.

Add a news KB alongside only when the user also cares about visibility and framing - and
if that is the main interest, this is the wrong skill.

**2. Poll and sample.** `get_last_flow_run_status` to completion, then `sample_kb`. Check
that cells are returning real events with dates and URLs, not summaries of the company's
about page. If they are, the task instruction needs tightening before you build the
matrix.

**3. Matrix workbench.** Companies as rows, triggers as columns (put the longer list on
rows - matrices read better tall than wide).

```
create_insight_booster_workbench({ id, name: "Competitor x trigger", type: "matrix" })
update_insight_booster_matrix_workbench_config({
  id, workbenchId,
  rowVariable: "$row", columnVariable: "$column",
  instruction: "Summarise $row's activity in $column over the last 12 months using only the attached sources. Give at most three concrete items, each with its date and source. Prefer primary sources. If the sources contain no significant activity of this type, write 'No significant activity identified' - do not infer activity from adjacent or unrelated news.",
  engine: "gpt-5.5",
  rows: [{ value: "Company A" }, ...],
  columns: [{ value: "Funding and investment" }, ...],
  summarizeRows: true, summarizeColumns: true
})
get_matrix_result({ project_id, workbench_id, projection })
```

`summarizeRows` gives a per-company verdict; `summarizeColumns` gives "who is most active
on M&A". Both are usually what gets quoted. Turn them on.

**4. Add movement only if you can.** If the KBs carry timestamps and cover enough history,
a bump workbench with `sourceType: "document"` over the company field shows who is gaining
share of activity. Skip it silently on a short window rather than producing a noisy chart.

## Reading the matrix like an analyst

- **Empty cells are findings.** A competitor with no partnership activity for a year is
  saying something. Report the silence explicitly rather than leaving a blank.
- **Compare across the row, not down it.** The question is "who is moving fastest", not
  "what did each company do".
- **Sequence matters.** A leadership change followed by a divestment three months later is
  a strategy shift; either one alone is noise.
- **Distinguish announced from delivered.** Press releases describe intent. Say which.
- **Name the asymmetry.** The useful output is where one competitor is doing something none
  of the others are.

## Deliver

Lead with the three moves that change something for the user, each with a date and a URL.
Then the matrix. Then what the scan could not see - private companies, non-covered
languages, activity below the announcement threshold.

Standing watch: re-run the research-agent KB with `schedule` (monthly suits most
sectors, weekly for fast-moving ones), and build the digest once as a report, then
`create_insight_booster_report_template` so each period is a single call. Offer this
whenever the user says "keep an eye on".
