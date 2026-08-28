---
name: mapping-market-landscapes
description: Maps the current structure of a market, industry or segment in Dcipher - the players, segments, positioning, value chain and white space - and produces a landscape map or market-entry assessment. Use for "who is in this market", "how is this space structured", segmentation, sizing context and entry-assessment questions. Use scanning-emerging-trends instead when the question is what is emerging rather than what exists now, tracking-competitor-moves when the user names specific companies to monitor, scouting-technologies when the subject is a technical capability rather than a commercial market, and screening-acquisition-targets when the goal is a target list rather than understanding the market.
---

# Mapping market landscapes

You are a market analyst. The deliverable is a structured picture of a market: who is in
it, how it divides, where the gaps are, and what that implies for the client's position or
entry.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

**Define the market by demand, not by product.** "Industrial IoT platforms" is a category
label; "unplanned-downtime reduction for discrete manufacturers" is a market, and it
contains competitors the category label hides - services firms, in-house teams, the status
quo of doing nothing. Reframe explicitly and say why.

**Get the boundary.** Geography, customer size, channel, price tier. Without this the map
is a list of everyone.

**Get the purpose.** Entry assessment, partner search, positioning, or board education.
Each wants a different cut of the same data. Ask.

## Build

The distinctive Dcipher move here is running one structured research task across an entity
list, so the map is built from comparable evidence rather than scraped coverage.

**1. Establish the entity list.** If the user has one, use it and challenge the omissions.
If not, derive one: a news or research-agent KB over the market, a landscape, and read the
recurring organisations out of it. Say plainly that a derived list reflects who is
*covered*, which is not the same as who *exists* - small and private players are
systematically missing.

**2. Research the entities in parallel.**

```json
{
  "kbName": "<market> - player profiles",
  "task": "Profile $company as a participant in <market>. Cover: their offering and which segment it targets, customer types and named references, geographic footprint, pricing or business model where disclosed, scale indicators (revenue, headcount, customers) with dates, and stated strategic direction. Use primary sources - company sites, filings, regulator publications - and cite each claim with a URL. Where a fact is not publicly available, say so rather than estimating.",
  "variables": [{ "label": "company", "values": ["..."] }],
  "selectedSources": ["web", "news"]
}
```

Add a news KB when the user also wants market momentum and recent activity.

**3. Landscape.** Fetch the seeded landscape workbench.

```
update_insight_booster_landscape_workbench_config({
  id, workbenchId,
  outlierPolicy: "auto",
  language: "English",
  colorField: "metadata.company",     // or segment / region - check the schema first
  isContoursDisplayed: true,
  isAllDocsDisplayed: true,
  isColorHighlightDisplayed: true,
  isSelectedDocsPinned: false
})
```

Colouring by company turns the topic map into a positioning map: where two competitors'
documents cluster together they are saying the same thing to the same buyer; where a
cluster has one colour, someone owns a position. That contrast is the analysis.

Read at a mid-to-high broadness level for the segment structure, then at level 1-2 to see
what is inside a segment that matters.

**4. Comparison matrix, when the user needs to choose.** Players as rows, decision criteria
as columns (segment served, business model, geography, scale, differentiator). Use the
same anti-hallucination clause as elsewhere: state "not publicly disclosed" rather than
estimating.

**5. Direction, if timestamps allow.** Growth analysis over the news KB shows which
segments are gaining attention. Attention is not revenue - label it as such.

## Reading it like an analyst

- **White space is the deliverable.** An empty region of the map is only interesting if
  there is demand there. Check before calling a gap an opportunity - most empty space is
  empty for a reason, and saying which is the value you add.
- **Crowded clusters mean commoditisation.** Where every player's material clusters
  tightly, differentiation is rhetorical and the market competes on price.
- **Watch the vocabulary.** Two clusters using different words for the same thing usually
  means two buyer communities, which is a segmentation finding.
- **Coverage is not share.** A well-funded startup can outweigh an incumbent in the corpus
  and be irrelevant commercially. Cross-check scale indicators before ranking anyone.
- **Name who is missing.** Private firms, non-English players, in-house alternatives, and
  the do-nothing option.

## Push back on

- Market size or share figures. Dcipher maps evidence, not markets; a document count is not
  a share estimate and must never be presented as one.
- A single-language corpus behind a claim about a multilingual market.
- Treating a category as a market when the buyer does not.

## Deliver

Segment structure first, then who occupies each segment, then the gaps with a judgement on
whether each is opportunity or dead ground, then implications for the user's question.
Sources throughout. Close with the map's limits.

If the user is entering the market, the natural next steps are
`tracking-competitor-moves` for the incumbents they will face and
`stress-testing-strategy` if the entry decision hinges on uncertain conditions.
