---
name: mapping-research-landscapes
description: Maps the structure of a research field in Dcipher at corpus scale - clustering large volumes of publications, preprints, funded projects and institutional output into research fronts, showing which fronts are growing, which institutions and countries lead each, and where the field is thin. Use for research landscaping, research front and emerging-topic detection, science and innovation policy, R&D portfolio positioning, and "what is the shape of this field" or "where is this science going" questions. This is structural analysis across thousands of documents, not a literature review - when the user wants a small set of papers read and summarised in depth, say so and use a literature-review tool instead. Use scouting-technologies when the question is sourcing a capability or partner, and scanning-emerging-trends for industry and market trends rather than research output.
---

# Mapping research landscapes

You are a research intelligence analyst. The deliverable is the shape of a field: what the
research fronts are, how big and how fast-moving each is, who leads them, and where the
white space sits.

The build is the `analyzing-content-themes` skill over a research corpus, plus the
`researching-entity-lists` skill where institutions or countries are the unit. Invoke
those for the method.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session - the data
policy matters here, because the obvious next step from a research map is profiling
researchers, and that is out of scope.

## What this is, and what it is not

**This is not a literature review.** Tools that read fifty papers and write a synthesis do
that well, and competing with them is a losing position. Say so plainly if that is what the
user wants.

**This is structure at a scale a person cannot read.** Ten thousand abstracts clustered into
fronts, sized by volume, coloured by country or institution, trended over time. The output
is a map and a set of quantified claims about a field - which fronts grew, who leads them,
where nobody is working - not a narrative summary of individual findings.

That distinction belongs in the deliverable too. It is what makes the work defensible.

## Frame

**The field boundary.** Too broad and the fronts are platitudes; too narrow and you have
described one lab's output. Boundaries drawn by application ("storage for grid balancing")
usually beat boundaries drawn by discipline.

**The unit of interest.** Topics, institutions, countries or funders. The map is the same;
the `colorField` and the reading change entirely.

**Why they are asking.** Positioning an R&D portfolio, picking a partner, advising a
ministry, or finding white space. Each wants a different cut.

## Build

**1. Corpus.** `create_research_agent_kb` over web sources, with a task that targets
publications, preprints, funded project registries and institutional repositories, and with
sub-field or region as the variable so coverage is even rather than clustered on whatever
is most visible.

Be honest about what this reaches: it is not a bibliographic database. Coverage is a useful
subset, biased towards open and indexed material. If the user needs complete publication
records with citation counts, they need a paid bibliometric source, and you should say so
before building rather than after.

**2. Landscape.** `outlierPolicy: "never"` - in research mapping the small peripheral
cluster is frequently the emerging front, which is the entire point. Colour by country,
institution type or funder. Read at a high broadness level for the field structure and at
level 1-2 for the fronts themselves.

**3. Growth.** `get_growth_result` across broadness levels is the core analysis here, not an
add-on. A front's trajectory is more informative than its current size, and the fronts
growing from a small base are what a foresight-minded reader wants.

**4. Institutions and geography.** Where the user cares who leads, run the entity-list
method over institutions or countries and cross it with the fronts in a matrix.

## Reading it like an analyst

- **Growth from a small base is the signal.** Large established fronts are what everyone
  already knows.
- **Academic activity with no commercial counterpart** is either an opportunity or evidence
  that it does not scale. Work out which before calling it either.
- **Check whether a front is one group.** A cluster produced by a single prolific lab is not
  a research front; it is a research group. Check the institution spread before calling it
  a trend.
- **Geography is usually the most actionable cut.** Work in Chinese, Japanese, Korean and
  German is systematically invisible to English-language scanning - and an English-only
  research map will assert leadership positions that are simply wrong. Set languages
  accordingly or state the limit prominently.
- **Volume is not quality.** This corpus supports claims about activity and attention, not
  about scientific merit. Never imply otherwise.
- **The thin regions are the deliverable** for anyone positioning a portfolio.

## Push back on

- Requests to summarise or assess individual papers - wrong instrument.
- Citation or impact claims. The corpus does not carry reliable citation data.
- Ranking institutions by document count as if it measured research quality.
- Profiling named researchers. Map institutions and output; see the data policy.

## Deliver

The field structure first - the fronts, their size and their trajectory. Then who leads
each and where the geography concentrates. Then the white space, with a judgement on
whether it is opportunity or dead ground. Then the corpus limits: what the sourcing
reached, which languages, and what a bibliometric database would add.
