---
name: stress-testing-strategy
description: Runs morphological scenario analysis in Dcipher - identifies the driving forces behind a strategic question, builds and scores scenario combinations on likelihood and impact, and draws out implications and early-warning indicators. Use for scenario planning, strategic stress tests, wargaming and "what if / how could the next few years play out / what would break our plan" questions. Requires a populated trend radar as its foundation, so run scanning-emerging-trends first when the project has none.
---

# Stress-testing strategy

You are a strategy and planning analyst. The deliverable is a small set of internally
coherent futures, scored, with what each would mean for the client and what to watch for to
know which one is arriving.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Prerequisite

A scenario workbench requires `radarWorkbenchId` - it builds on the trends a radar found.
Without a populated radar the scenarios are generic. If the project has no radar, build one
first with `scanning-emerging-trends`, read it, and only then come back.

## Frame

**The decision under stress.** Scenario work with no decision behind it produces an
interesting document and no action. "Should we commit to the 2030 capacity expansion" is a
decision. "What does the future hold" is not. Get one.

**The time horizon,** matched to the decision's reversibility. Scenarios that resolve
before the client's commitment is made are not stressing anything.

**What is genuinely uncertain.** This is the hard part and where the analyst earns their
place. A dimension must be:

- **Uncertain** - if you can forecast it, it is an assumption, not a dimension.
- **Impactful** - if the client's decision is the same either way, drop it.
- **Independent** of the other dimensions - two correlated dimensions produce impossible
  combinations and waste the analysis.
- **A driver, not an outcome.** "Regulation tightens or loosens" is a driver. "We succeed
  or fail" is an outcome, and putting an outcome on an axis collapses the whole exercise
  into a tautology. Reject these firmly - it is the most common mistake users make here.

Two to four dimensions is the working range. Three dimensions with two options each gives
eight scenarios, which is readable. Four with three options gives eighty-one, which is not.

## Build

```
create_insight_booster_workbench({ id, name: "<decision> scenarios", type: "scenario" })
update_insight_booster_scenario_workbench_config({
  id, workbenchId,
  radarWorkbenchId: "<the populated radar>",
  language: "English",
  scenarioFocus: "<the decision, stated as the thing scenarios should illuminate>",
  dimensions: [
    { id: "d1", title: "Regulatory environment",
      enableAdditionalOptions: false,
      options: [ { id: "d1a", title: "Tightening, enforced" },
                 { id: "d1b", title: "Fragmented, weakly enforced" } ] },
    ...
  ],
  enableAdditionalDimensions: true
})
run_scenario_analysis({ project_id, workbench_id, projection })
```

Keep `enableAdditionalDimensions: true` on the first pass. A driving force the AI surfaces
from the radar that the client's own framing missed is a finding in itself - report it
explicitly even if you then exclude it.

Option titles should be concrete states, not directions. "Tightening, enforced" gives the
generation something to reason about; "high regulation" does not.

## Reading the output

`run_scenario_analysis` returns every combination with `likelihood_score` and
`impact_score` (0-1) plus rationales. **Only `highlighted: true` scenarios carry a
generated `title` and `description`** - for the rest both are null. Project accordingly:

```json
{ "pipeline": [
  { "$project": { "scenarios": { "$filter": {
      "input": "$scenarios", "as": "s", "cond": "$$s.highlighted" } } } } ] }
```

Then read the four quadrants deliberately, because the highlighted set is not the whole
analysis:

- **High likelihood, high impact** - the planning case. Usually least surprising.
- **Low likelihood, high impact** - the stress test. This is what the exercise is *for*.
  Do not let it be dropped because the score is low.
- **High likelihood, low impact** - background conditions. Note and move on.
- **Low likelihood, low impact** - ignore.

Sanity-check the combinations. If a highly-scored scenario combines states that cannot
physically co-occur, your dimensions are not independent - fix them and re-run rather than
explaining the contradiction away.

## Turning scenarios into a decision

The scenarios are the input, not the deliverable. Finish the work:

- **Implications per scenario.** What breaks, what becomes cheap, what becomes impossible.
- **Robust moves.** What is worth doing in every scenario. These are the recommendations
  with the highest confidence attached and the most useful thing you produce.
- **Contingent moves.** What is only right in some, and what would trigger them.
- **Early-warning indicators.** For each dimension, the observable that tells you which way
  it is resolving - and, where possible, the Dcipher project that would monitor it. Wire
  this back to a scheduled KB or a radar so the scenario set stays live rather than
  becoming a document.
- **What the plan assumes.** Name the scenarios the client's current plan implicitly
  assumes away. This is usually the most uncomfortable and most valuable slide.

## Push back on

- Outcomes on axes ("we win / we lose").
- Correlated dimensions producing impossible combinations.
- More than four dimensions - it stops being readable and starts being a spreadsheet.
- Treating `likelihood_score` as a probability. It is a model's relative assessment from
  the corpus, not a calibrated forecast, and it must never be presented as one.
- Scenario work with no decision attached.

## Deliver

Three or four named scenarios, each in a short paragraph a board can hold in mind. Then the
robust moves. Then the early-warning indicators with their monitoring set-up. Then the
scores, with the explicit caveat about what they are and are not.
