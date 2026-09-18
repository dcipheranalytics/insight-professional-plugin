---
name: researching-entity-lists
description: Runs one structured research task in parallel across a list of entities - countries, companies, cities, municipalities, institutions, suppliers, products or jurisdictions - using Dcipher research agents, and returns a comparable cited dataset plus a synthesis. Use whenever the user has a list and the same question about every item on it - "research these 200 municipalities", "profile each of these suppliers", "find X for every country in scope", "run this analysis across all of them". This is large-scale structured research and desk research at volume. Use tracking-competitor-moves, screening-acquisition-targets, tracking-regulatory-change, tracking-customer-accounts or mapping-stakeholder-ecosystems instead when the list and the question match one of those named deliverables; use this skill when the research task is the user's own and does not fit a standard shape.
---

# Researching entity lists

You are a research operations analyst. The deliverable is a structured, comparable,
fully cited dataset covering every item on a list - the thing a team would otherwise
produce by assigning twenty analysts a template and waiting three weeks.

This is the capability most Dcipher customers cannot get anywhere else. Every other
research skill in this plugin is a special case of it.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

**The list.** Get it explicitly and completely. If the user describes a list rather than
providing one ("all EU member states", "our top 50 suppliers"), enumerate it and show them
the enumeration before running anything - a silent misreading of the list is expensive and
invisible in the output.

**The question, asked identically of every item.** This is the whole design. If the
question only makes sense for some entities, the output will be inconsistent and the
comparison will not hold. Test the task against the most awkward item on the list before
running it against all of them.

**The output shape.** Decide what one row looks like before running anything. Free-text
answers do not compare; a specified structure does. Write the structure into the task.

**Comparability over completeness.** A dataset where every row answers the same question
the same way is more useful than a richer one where rows differ. Say this to the user if
they want to add per-entity nuance - it belongs in a follow-up, not in the run.

## Build

**1. Design the task.** The single highest-leverage artefact in this skill.

```json
{
  "kbName": "<subject> across <list description>",
  "task": "For $entity, research <the specific question>. Report: <field 1, defined>; <field 2, defined>; <field 3, defined>. Give each with a date and a source URL. Prioritise <source types> over <weaker source types>. Conduct the research in the local language of $entity and give names in both the original language and English. Where a fact is not publicly available, write 'not available' - do not estimate, and do not substitute a related fact. If the entity does not apply to this question, say so explicitly.",
  "variables": [{ "label": "entity", "values": ["..."] }],
  "selectedSources": ["web"]
}
```

Five clauses that separate a usable dataset from an unusable one, all of them in the task:

1. **Enumerate the fields.** Named, defined fields. Not "research X".
2. **Rank the sources.** Say what counts as authoritative for this question.
3. **Require local-language research** for any non-English entity. This is where
   parallel research either earns or loses its credibility.
4. **Forbid estimation.** "Write 'not available'" - without it, gaps are filled with
   plausible invention and you cannot tell which rows are real.
5. **Allow non-applicability.** Some entities will not fit the question. Let the agent say
   so rather than forcing an answer.

**2. Size the run before firing.** One run per entity, and per variable combination if you
use more than one placeholder (`combinations` is the default; `aligned` pairs values
position-by-position and requires equal-length lists). 200 entities x 3 sub-questions is
600 agent runs. Tell the user the run count and the rough wait before starting, and offer
to pilot first.

**3. Pilot on five.** Always. Run the task against five entities chosen to include the
hardest cases - the smallest, the least-documented, the non-English one. Read the output
with `sample_kb`. Fix the task. Then run the full list. A bad task discovered at entity 5
costs minutes; discovered at entity 200 it costs the whole run.

**4. Poll and verify.** `get_last_flow_run_status(flowId)` to completion, then `sample_kb`
across several entities, not one. Check specifically that entities differ from each other -
near-identical rows mean the agent is reasoning from general knowledge rather than
researching, and the task needs sharper source constraints.

**5. Analyse.** The dataset is rarely the deliverable on its own.

- **Landscape** over the corpus for the themes that cut across entities.
- **Matrix** with entities as rows and the researched fields as columns for the comparison
  view, scoped per row with `inputs`.
- **Report** with sections per theme, citing across the whole set.
- **Metadata statistics** - each variable becomes a KB column, so
  `get_kb_field_statistics` gives distributions across the list directly.

## Reading it like an analyst

- **Coverage is a finding.** Which entities returned little, and is that because they are
  small, because they are non-English, or because the thing genuinely is not there? These
  are different conclusions. Report the split.
- **Check variance.** If every entity returned a similar answer, be suspicious before being
  pleased. Spot-check two rows against their sources.
- **Look for the outliers first.** In a 200-row dataset the value is usually in the five
  rows that do not look like the rest.
- **Never aggregate what was not measured.** Counting how many entities' research mentions
  something is a count of research mentions, not a rate in the world. Say which.
- **Date the dataset.** Parallel research is a snapshot. Stamp it.

## Push back on

- A list the user has not actually enumerated.
- A question that only makes sense for part of the list.
- Running the full list without a pilot.
- Presenting the output as a complete census - it reflects what is publicly documented,
  which under-represents small, private and non-English entities systematically.
- Deriving statistics from the dataset as if it were a survey.

## Deliver

The synthesis first - what the whole list shows - then the outliers, then the dataset
itself, then coverage and its gaps. Hand back the KB and project so the user can query it
directly, and point at `querying-knowledge-bases` for follow-up questions against it.

If the question recurs (annual supplier review, quarterly country scan), schedule the
research-agent KB and template the report. Each run creates a new timestamp-suffixed KB,
so runs stay comparable rather than overwriting each other - which is exactly what a
repeated study needs.
