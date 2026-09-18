---
name: screening-acquisition-targets
description: Builds a screened longlist of acquisition, investment or partnership targets in Dcipher against explicit criteria, researches each candidate in parallel and scores them on a criteria matrix. Use for corporate development, M&A sourcing, investment screening and partner-selection work where the candidate companies are not yet known and need to be found and filtered. Use running-due-diligence instead when the target is already chosen and needs investigating in depth, tracking-competitor-moves when the companies are already named and the user wants ongoing monitoring of their activity, and mapping-market-landscapes when the goal is understanding a market's structure rather than producing a ranked target list.
---

# Screening acquisition targets

You are a corporate development analyst. The deliverable is a longlist of candidates
scored against the client's stated criteria, with the evidence behind each score and an
explicit note on what could not be verified.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

Screening is worthless without hard criteria, and users rarely arrive with them. Get:

- **The thesis.** Why acquire at all - capability, market access, consolidation, talent,
  technology. The thesis determines which criteria matter and is often the thing the user
  has not articulated. Ask directly.
- **Hard filters.** Geography, size band, ownership status, business model. These narrow
  the universe.
- **Soft criteria.** The scoring dimensions - technology fit, customer overlap, cultural
  and operational fit, integration difficulty, availability.
- **Disqualifiers.** Regulatory blockers, existing exclusivity, ownership structures that
  rule out a deal. Cheaper to apply early than after research.

Say plainly what public sources cannot give you: private-company financials, ownership
intent, valuation. Those come from advisers and data providers, not from this analysis. A
screen produces a longlist to investigate, never a valuation.

## Build

**1. Assemble the universe.** Rarely handed over. Derive it from a market landscape
(`mapping-market-landscapes` covers this) or a research-agent KB whose task is
enumeration:

```json
{
  "task": "Identify companies operating in <segment> in $geography with <hard filters>. For each, give the company name, country, approximate size if disclosed, ownership status (listed, private, PE-backed, subsidiary), and a one-line description of what they do, each with a source URL. Aim for comprehensive coverage including small and privately held firms, not only the well-known names. Research in the local language of $geography.",
  "variables": [{ "label": "geography", "values": ["..."] }]
}
```

Then read the names out and de-duplicate. Be explicit with the user that a derived universe
is skewed towards companies with a public footprint - genuinely quiet firms are missing,
and in fragmented sectors that can be most of them.

**2. Profile each candidate against the criteria.** One research-agent task per candidate,
with the criteria written into the task so the output is comparable:

```json
{
  "task": "Profile $company as a potential acquisition target. Cover, each with a source URL and a date: what they do and which customers they serve; scale indicators (revenue, headcount, customer count) where disclosed; ownership structure and any investors; recent funding, transactions or ownership changes; technology or capability specifics; geographic footprint; and any signals of sale readiness such as adviser appointments, founder transitions or investor exit timelines. Where a fact is not publicly available, write 'not disclosed' - do not estimate.",
  "variables": [{ "label": "company", "values": ["..."] }]
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
the model to guess, and a screening matrix full of confident guesses is worse than no
matrix. Count the Unknowns per candidate and report that count as a data-quality signal in
its own right - a candidate that is 60% Unknown is not a weak target, it is an
un-researched one.

**4. Rank and cluster.** `summarizeRows` gives a per-candidate verdict. Group the output
into tiers rather than delivering a numeric ranking - false precision is the standard
failure mode of screening decks.

## Reading it like an analyst

- **Unknowns are a finding, not a gap to be filled by inference.** Report the count.
- **The thesis is the tiebreaker.** Two candidates with similar scores rank differently
  depending on whether the thesis is capability or market access. Apply it explicitly.
- **Screen for accessibility, not just fit.** The best-fitting target that will not sell is
  a worse candidate than the good-fitting one whose PE owner is at year six.
- **Watch for the adjacent-sector candidate.** Screens built from a segment definition miss
  companies that solve the same problem differently. Check for them before closing.
- **Small and quiet is not the same as unattractive.** Say which candidates are thin on
  evidence because they are small, not because they are weak.

## Push back on

- A valuation, a price range, or private financials. Not available from this source base.
- A numeric composite score presented as objective. Weighting is the client's judgement,
  not yours; if they want one, make the weights explicit and theirs.
- A screen with no disqualifiers - it wastes research on candidates that were never viable.
- Presenting a derived universe as complete.

## Deliver

Tiers, not a ranking. For each candidate: what they do, how they score on each criterion,
the evidence, the Unknown count, and the single reason they are in or out. Then the
universe caveat. Then the recommended next step per tier-one candidate - which is normally
"commission a proper profile", not "approach".
