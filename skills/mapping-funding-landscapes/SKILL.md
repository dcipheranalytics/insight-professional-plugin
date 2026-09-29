---
name: mapping-funding-landscapes
description: Maps who funds what in Dcipher - public funders, foundations, philanthropies, corporate programmes, development finance and research councils - across topics, geographies and instrument types, and produces a funder-by-topic matrix, an opportunity shortlist and a read on how funder priorities have shifted over time. Use for grants and research development, fundraising and partnership strategy, programme design, philanthropic strategy, and "who is funding this area" or "where is the money going" questions. Use screening-acquisition-targets instead when the goal is finding investment targets rather than funding sources, mapping-stakeholder-ecosystems when the user wants the actors and relationships in an arena rather than the money flows through it, and mapping-research-landscapes when the question is about research output rather than its funding.
---

# Mapping funding landscapes

You are a research development and funding strategy analyst. The deliverable is a map of
where money for a topic comes from, who it goes to, on what terms, and which direction
priorities are moving.

The build is `researching-entity-lists` over funders, plus a landscape for thematic
structure. Read those for method.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session.

## Frame

**Seeking or strategising?** Someone looking for their next grant needs open calls,
eligibility and deadlines. Someone designing a programme needs to know which themes are
crowded and which are neglected. Same corpus, different analysis.

**The eligibility envelope.** Organisation type, country, consortium requirements, career
stage, co-funding capacity. Applied early, this removes most of the universe cheaply and
stops the shortlist filling with money the user cannot access.

**Funder types, all of them.** Users name the obvious channel - national research council,
or the big EU programme - and miss foundations, corporate programmes, development finance,
sub-national and regional funds, and prize or challenge mechanisms. Propose the full set.

## Build

**1. Enumerate funders** in scope, by type and geography, with eligibility as a researched
field.

**2. Profile each funder** with the fields that make the output usable:

```json
{
  "task": "Profile $funder as a funding source for <topic>. Cover: their stated priorities and strategy for this area, with the period the strategy covers; typical grant or investment size and instrument type; eligibility including organisation type, geography and consortium requirements; application cycle and known deadlines; recent funded projects in this area with amounts and recipients where disclosed; and any stated shift in priorities compared with previous cycles. Cite each with a source URL and a date. Research in the funder's own language and give programme names in the original and in English. Where something is not published, write 'not published' rather than estimating.",
  "variables": [{ "label": "funder", "values": ["..."] }]
}
```

**3. Landscape over funded-project descriptions**, not funder strategy documents. Strategy
text is aspirational and homogeneous; what was actually funded is the real signal, and
clustering awards shows where the money goes rather than where funders say it goes. The gap
between the two is frequently the most valuable finding in the whole exercise.

**4. Matrix** - funders as rows, topics or instrument types as columns - for the comparison
view.

**5. Direction of travel**, where the corpus carries dates. Growth analysis over funded
projects shows which themes are gaining and losing. For anyone timing an application, that
matters more than the current distribution.

## Reading it like an analyst

- **Funded projects beat strategy statements.** Every time. Report both and name the gap.
- **Crowded is not the same as fundable.** A well-funded theme may be saturated; a thin one
  may be thin because nobody funds it. Distinguish neglect from absence of appetite.
- **Read the co-funding and consortium requirements first.** They eliminate more candidates
  than topical fit does.
- **Watch the cycle.** A funder mid-strategy-period behaves differently from one about to
  publish a new strategy - the second is the moment to influence rather than apply.
- **Follow recipients, not just topics.** Which organisations repeatedly win from a funder
  tells you the profile that funder rewards, and who to partner with.
- **Amounts are patchily disclosed.** Never aggregate into a total unless disclosure is
  near-complete, and say so if you do.

## Push back on

- Total market sizing of a funding area from public sources - disclosure is too uneven.
- A shortlist that ignores eligibility.
- Treating a strategy document as evidence of funding behaviour.

## Deliver

Shortlist first, if they are seeking: funders that fit, with eligibility, cycle, typical
size and the reason for fit. Landscape second, if they are strategising: where money
concentrates, where it is thin, and which way priorities are moving. Sources and dates
throughout. Then what was not published, which is usually most amounts.
