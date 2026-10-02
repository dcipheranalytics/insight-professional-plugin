---
name: screening-entities-against-criteria
description: The shared method for screening a set of entities against explicit criteria in Dcipher - defining hard filters and scoring dimensions, assembling the candidate universe, researching each candidate in parallel, scoring them on a criteria matrix with an explicit Unknown level, and grouping the result into tiers. Use directly when the user wants a filtered and scored shortlist and none of the specific framings fits. Use screening-acquisition-targets when the entities are acquisition or investment candidates, screening-counterparty-risk when the criteria are risk and controversy dimensions, running-due-diligence when one entity needs investigating in depth rather than many compared, and researching-entity-lists when the user wants a research dataset rather than a scored shortlist.
---

# Screening entities against criteria

The shared machine behind every "find and filter these for me" deliverable: targets,
suppliers, partners, grantees, counterparties. The specific skills add the criteria and the
reading; the build below is common.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

Screening is worthless without criteria, and users rarely arrive with them. Get four
things:

- **The purpose.** Why screen at all - acquire, source, fund, de-risk, partner. It decides
  which criteria matter and is often the part the user has not articulated.
- **Hard filters.** Geography, size, sector, ownership, status. These narrow the universe
  before any research runs, and they are cheap.
- **Scoring dimensions.** The soft criteria the shortlist is ranked on.
- **Disqualifiers.** What rules a candidate out regardless of fit. Applying these early
  saves researching candidates that were never viable.

Say plainly what public sources cannot establish - private financials, ownership intent,
valuation, anything behind a paywall the platform does not carry. A screen produces a
shortlist to investigate, never a verdict.

## Build

**1. Assemble the universe.** Rarely handed over complete. Either derive it from a
landscape over the sector, or run an enumeration task:

```json
{
  "task": "Identify entities operating in <scope> in $geography meeting <hard filters>. For each, give the name, country, approximate size if disclosed, ownership or legal status, and a one-line description, each with a source URL. Aim for comprehensive coverage including small and privately held entities, not only the well-known names. Research in the local language of $geography.",
  "variables": [{ "label": "geography", "values": ["..."] }]
}
```

Set `excludeEmptyResults: false` on this and the profiling run, otherwise a candidate the
agent found nothing for vanishes instead of appearing as Unknown.

Then de-duplicate and show the user the list before researching it. Be explicit that a
derived universe skews towards entities with a public footprint - genuinely quiet ones are
missing, and in fragmented sectors that can be most of them.

**2. Profile each candidate against the criteria**, with the criteria written into the task
so outputs are comparable rather than merely rich:

```json
{
  "task": "Profile $entity against the following criteria: <criteria, each defined>. For each, give a specific, dated, sourced statement. Where a fact is not publicly available, write 'not disclosed' - do not estimate and do not substitute a related fact. Research in the local language where applicable.",
  "variables": [{ "label": "entity", "values": ["..."] }]
}
```

**3. Scoring matrix.** Candidates as rows, criteria as columns.

```
instruction: "Assess $row against the criterion '$column' using only the attached sources.
Give a one-line evidence-based assessment, then a rating of Strong, Partial, Weak or
Unknown. Use Unknown when the sources do not support a judgement - do not infer from
adjacent facts. Cite the source for each assessment."
summarizeRows: true
```

The four-level scale with an explicit **Unknown** is deliberate. A three-point scale forces
the model to guess, and a matrix of confident guesses is worse than no matrix. Count the
Unknowns per candidate and report that count - a candidate that is 60% Unknown is not weak,
it is un-researched, and those are different conclusions.

**4. Tier, do not rank.** Group into tiers with a stated reason per tier. A numeric
composite score is false precision: the weighting is the user's judgement, not yours. If
they want one, make the weights explicit and theirs.

## Reading it like an analyst

- **Unknowns are a finding**, not a gap to fill by inference.
- **The purpose is the tiebreaker.** Two similar scores rank differently depending on why
  you are screening.
- **Screen for accessibility, not just fit.** The best-fitting candidate that is
  unavailable, exclusively committed or unwilling is worse than the good-fitting one that
  is reachable.
- **Check the adjacent set.** Screens built from a sector definition miss entities that
  solve the same problem differently.
- **Small and quiet is not the same as weak.** Say which candidates are thin on evidence
  because of their size rather than their quality.

## Push back on

- A screen with no disqualifiers.
- A numeric composite presented as objective.
- Presenting a derived universe as complete.
- Valuations, private financials, or anything the source base cannot support.

## Deliver

Tiers, not a ranking. Per candidate: what they are, how they score, the evidence, the
Unknown count, and the single reason they are in or out. Then the universe caveat. Then the
recommended next step per top-tier candidate - usually "investigate properly", which means invoking the
`running-due-diligence` skill.
