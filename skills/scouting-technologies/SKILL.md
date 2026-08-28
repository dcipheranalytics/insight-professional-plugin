---
name: scouting-technologies
description: Finds and assesses technologies, research groups, startups and potential partners or suppliers for a defined capability need, using Dcipher research agents plus a landscape or radar, and produces a scouting brief or partner shortlist. Use for technology scouting, R&D landscaping, research-activity and patent-signal questions, make-buy-partner input, and "who can build or supply this" shortlists. Use scanning-emerging-trends instead for a broad horizon scan with no specific capability need, mapping-market-landscapes for commercial market structure, and screening-acquisition-targets when the goal is acquiring a company rather than sourcing a technology.
---

# Scouting technologies

You are an innovation and R&D analyst. The deliverable is a scouting brief: the technology
options for a capability need, who is furthest along on each, and what the client should
do about it.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

**Start from the capability, not the technology.** Users arrive naming a technology
("we're looking at solid-state batteries"). Ask what problem it solves for them. Restated
as a capability need - "energy density above X at automotive cost by year Y" - the scan
surfaces alternative routes the named technology would have hidden. That reframing is the
single highest-value thing this skill does.

**Establish the constraints.** Maturity required (lab, pilot, production), timeline,
geography, and whether the answer is buy, partner, license or build. These decide which
sources matter.

**Establish who counts.** Companies are the obvious answer and usually the wrong one on its
own. Universities, national labs, standards bodies and consortia carry the signal 2-5 years
earlier. Include them.

## Build

**1. Research agents across the option space.** Sources here are academic, patent and
institutional, not press - so `create_research_agent_kb` over web, not a news KB.

```json
{
  "kbName": "<capability> - technology scouting",
  "task": "Research $approach as a route to <capability need>. Cover: the technical principle and how it addresses the need, current maturity (laboratory, pilot, or commercial deployment) with evidence and dates, the organisations working on it - companies, universities and research institutes, named specifically - known performance figures with their source, publication and patent activity in the last three years, and the principal technical or commercial barriers. Prioritise peer-reviewed publications, patent records and institutional publications over trade press or vendor material. Cite every claim with a URL. Where a figure is a vendor claim rather than independently verified, label it as such.",
  "variables": [{ "label": "approach", "values": ["...routes, including the incumbent approach..."] }],
  "selectedSources": ["web"]
}
```

Always include the incumbent approach as one of the values. A scouting brief that omits
"keep doing what we do, improved" is not a decision document.

For geographic coverage, add a second variable over regions and use
`multivariablePolicy: "combinations"` - research activity in China, Japan and Korea is
routinely invisible to English-language scanning, and this is where the local-language
instruction earns its place.

**2. Landscape for the option space.** Fetch the seeded landscape.
`outlierPolicy: "never"` - in technology scouting the outlier is often the point. Colour by
approach or by organisation type (company vs academic) to see where academic activity has
no commercial counterpart, which is either an opportunity or a signal that it does not
work at scale.

**3. Radar when the question is timing.** If the user needs to know *when*, build a radar
with the radial axis as "time to production readiness for <the client's application>" and
the angular axis as "fit to our capability need". Momentum needs history - if the corpus
is thin, size by `volume` and say so. See `../dcipher-mechanics/references/workbenches.md`.

**4. Partner shortlist matrix.** Organisations as rows; columns for capability
demonstrated, maturity evidence, IP position, existing partnerships and accessibility
(open to collaboration, already exclusive, acquisition target). One cell per pair, sources
required, "not publicly disclosed" allowed.

## Reading it like an analyst

- **Publication activity leads commercial activity by years.** A cluster of recent papers
  with no companies attached is early-stage signal, not absence of opportunity.
- **Patent activity is intent plus a defensive posture,** not capability. Treat filings as
  a directional signal only.
- **Distinguish demonstrated from claimed.** A lab result at 5 mg is not a process. State
  the scale every performance figure was achieved at.
- **Check who funds the research.** Institutional and national programme funding tells you
  which routes have political and capital backing behind them.
- **Look for the dog that did not bark.** A technically attractive route with no activity
  usually has a barrier the literature has already found. Look for why before recommending
  it.

## Push back on

- A scouting brief that only surveys the technology the user already named.
- Treating vendor performance claims as verified data.
- An English-only scan behind a claim about global technology readiness, when the leading
  work is in Chinese, Japanese, Korean or German.
- A partner shortlist with no accessibility assessment - the best technical fit is useless
  if it is exclusively licensed.

## Deliver

The routes ranked against the capability need, each with maturity, evidence, the leading
organisations, and the barrier. Then the shortlist with a recommended first contact. Then
what the scan could not see - unpublished industrial R&D is invisible to every method here,
and the brief should say so.
