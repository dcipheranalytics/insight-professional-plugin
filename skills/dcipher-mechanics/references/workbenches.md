# Workbenches

A workbench holds one visualisation, its config, and any AI-generated report. Six types
plus `report`.

## The pattern

```
create_insight_booster_workbench({ id: projectId, name, type })
   type: landscape | radar | bump | matrix | growth | scenario | report
update_insight_booster_<type>_workbench_config({ id, workbenchId, ... })
get_<type>_result({ project_id, workbench_id, projection })
```

Notes that save round trips:

- `create_insight_booster` already seeded **one landscape workbench**. Fetch it rather
  than creating a second one.
- If you did not create the workbench in this session, call
  `get_insight_booster_workbench({ id, workbenchId })` to confirm its type. The config
  tools reject a type mismatch.
- `growth` has no data-config tool, but its `period` and `broadnessLevel` live in the
  workbench's UI config, and `get_growth_result` also takes a `broadness_level`.
- `update_insight_booster_workbench_ui_config` takes one of three shapes, matched to the
  workbench type: radar `{ period, showHiddenBubbles }`, bump `{ period, normalize }`, growth
  `{ period, broadnessLevel }`. **Some of these change the numbers, not only the display** -
  the radar `period` sets the time bucket behind momentum and size. Landscape, matrix and
  report workbenches accept no UI config.

## Choosing the workbench

This is the judgement the use-case skills lean on. Match it to the question, not to the
data.

| The user is asking | Workbench |
|---|---|
| What themes are in this corpus? | **landscape** |
| What is emerging, how fast, how far out? | **radar** |
| How did known themes move in rank over time? | **bump** |
| Which themes are accelerating or decaying? | **growth** |
| Entities x criteria, one cell each | **matrix** |
| What futures follow from these driving forces? | **scenario** |
| Written deliverable with citations | **report** |

A landscape asks "what is here". A radar asks "what is coming". Users who say "trends"
often want the landscape. Users who say "themes" sometimes want the radar. Ask if the
distinction changes the build.

## Landscape

Thematic clustering over the corpus. Required: `outlierPolicy`, `language`,
`isSelectedDocsPinned`, `isContoursDisplayed`, `isAllDocsDisplayed`,
`isColorHighlightDisplayed`, `id`, `workbenchId`.

```
colorField    categorical metadata field for bubble colour  (verify in the schema first)
sizeField     numeric metadata field for bubble size
outlierPolicy auto | always | never
language      FULL ENGLISH NAME - "English", "German", "Turkish". NOT an ISO code.
```

`language` is injected verbatim into the generation prompt; an ISO code renders the UI
language selector empty. This applies to the landscape, radar and scenario configs alike.

`colorField` is where a landscape becomes an argument rather than a picture. Colour by
source, region, or sentiment and the map answers "who is talking about what", not just
"what is being talked about".

Semantic outlier removal is on by default and drops small clusters, which are exactly where
weak signals sit. `outlierPolicy: "always"` for a noisy news corpus, `"never"` when the periphery is the
interesting part (weak signals, early-stage technologies).

**Broadness** is a read-time parameter, not a config field. Call
`get_landscape_max_broadness({ project_id, workbench_id })` for the ceiling. Level 1 is the
most granular; higher levels group into broader themes. Start one or two levels below the
maximum for an executive read, level 1-2 when hunting for specifics.

## Radar

Required: `mode`, `areaOfInterest`, `language`, `approach`, `predefinedSegments`,
`enableAdditionalSegments`, `angularScaleParam`, `radialScaleParam`, `sizeScaleParam`,
`id`, `workbenchId`.

```
mode      trendDetection   emerging directions          <- the default for foresight
          contentAnalysis  key themes and sub-themes
          newsAnalysis     recent developments, events
          narrativeAnalysis underlying stories, framings

approach  bottom-up   AI discovers the sectors          <- use when the taxonomy is unknown
          top-down    predefined sectors only           <- use when the user has a taxonomy
```

With `top-down`, supply `predefinedSegments: [{ name, predefinedTrends[],
enableAdditionalTrends }]`. Set `enableAdditionalSegments: true` to let the AI add sectors
the user's taxonomy missed - usually worth it, and the additions are themselves a finding.

The two axes take either an AI-derived definition or a metadata aggregation:

```
angularScaleParam: { openEndedDefinition: "Impact on the client's core business (1 = negligible, 5 = threatens it)" }
radialScaleParam:  { openEndedDefinition: "Time until widespread commercial impact (1 = already happening, 5 = more than five years away)" }
sizeScaleParam:    { predefinedMetric: "momentum" }   # or "volume"
```

Higher angular values sit more clockwise within the sector; higher radial values sit
further from the centre. `momentum` is rate of acceleration over the period, `volume` is
raw data quantity in the period. **Momentum needs timestamps and enough history** - on a
three-week corpus it is noise. Use `volume` or fix the corpus.

Alternatively `aggregateMetadataDefinition: { field, aggregateFunction }` (`sum`, `mean`,
`max`) to drive an axis off a real numeric field such as funding amount.

**What comes back.** A list of sectors, each holding bubbles. A risk-themed project on the
platform returned 7 sectors and 27 bubbles. Sector titles are generated sentences ("Agentic AI
Expands the Cybersecurity Attack Surface"). Each bubble has a title, a `summary`, a document
`count`, an `angularScale` and `radialScale` on a **1-5 scale**, a `sizeScale` holding the
volume for each period bucket (`daily`, `weekly`, `monthly`, and so on) and around ten source
references. Positions are the model's qualitative judgement against your two definitions, not
measurements, so report them as placement and never as scores.

**Anchor both ends of every axis.** Naming the quantity is not enough. In that project the
radial axis was defined as "Time until this risk or trend materially affects mainstream
organizations and society". The model gave 5.0 to shadow AI and 4.9 to prompt injection, both
already widespread, and 1.4 to kill-switch proposals, which are speculative future policy. It
had read the axis as imminence, the reverse of what the wording says. Write the scale into the
definition, as in the example above, then check two bubbles whose timing you know before
reading the chart.

**Set the bucket.** The radar `period` in the UI config (`daily` to `biennial`) decides the
time bucket behind `momentum` and `volume`. Weekly suits a news corpus watched over months;
quarterly suits a slower research corpus.

With `approach` set to predefined sectors, name each in under ten words; longer names clutter
the chart. `enableAdditionalSegments` decides whether AI-found sectors appear beside yours.

Phrase axis definitions as the client's decision criterion, not as a generic metric.
"Impact on society" is a template; "Threat to our aftermarket service revenue" is an
analysis.

## Bump

Rank evolution over time. Required: `sourceType`, `id`, `workbenchId`.

```
sourceType: "topic"     -> uses the landscape; set broadnessLevel (1 = narrowest)
            "document"  -> set metadataField (a categorical field from the KBs)
            "radar"     -> set sourceWorkbenchId + radarSourceType: category|subcategory
```

Requires timestamped documents. Check `check_kb_has_timestamp` and
`get_project_time_range` first, and refuse politely if the window is too short to show
movement - a bump chart over three weeks of news measures the news cycle, not the trend.

`get_bump_result` takes `max_series` (default 25) and returns pre-bucketed series under
`day`, `week`, `month`, `quarter`, `semiyear`, `year`. Project only the granularity you
need.

## Growth

Configured through the UI config: `period` (`daily` up to `biennial`) and `broadnessLevel`.
`get_growth_result({ project_id, workbench_id, broadness_level })` returns topics with
`growths` and `volumes` keyed by time bucket; pass `broadness_level` explicitly rather than
relying on the default of 1.

Growth compares only the **last two periods**: "annual" sets the two most recent 12-month
periods against each other, "monthly" the last two months. Say which pair when you report a
rate. Topics above the breakout level are new, with no earlier period to compare against,
so their growth is undefined and not large. Needs a date field. The available periods
depend on how long the corpus runs.

Read growth and volume together. A topic can double from two documents to four; that is
not a trend. Filter on `size` or `volumes` before reporting a growth rate.

## Matrix

Entities x criteria, one AI-generated cell each. Required: `instruction`, `engine`, `rows`,
`columns`, `rowVariable`, `columnVariable`, `id`, `workbenchId`.

```
rowVariable    "$row"     (default)
columnVariable "$column"  (default)
instruction    must contain BOTH tokens verbatim
rows/columns   [ { value, instruction?, inputs?, viewInputs? } ]
```

`value` is the header label and the substitution value. Per-row/column `instruction`
overrides the matrix-level one; `inputs` (KB ids) and `viewInputs` (view ids) scope which
documents that row or column may draw on. Use this to cross sources in one matrix, for
example rows fed by the research-agent KB and columns by the news KB. Per-row/column
overrides go on one axis only. An empty cell means the sources held nothing relevant, which
is a finding, not a failure.

`contentDistributionOptimization: "rows" | "columns"` prioritises content along that axis.
`summarizeRows` / `summarizeColumns` generate footer summaries - cheap, and usually the
part a reader actually quotes.

The cell instruction determines the entire quality of the output. A weak one:

> Describe $row's activity in $column.

A working one:

> For $row, report their activity in $column over the last 12 months. Give at most three
> concrete items, each with a date and a source. Prefer primary sources (filings, press
> releases, regulator publications) over commentary. State "No significant activity
> identified" if the evidence does not support a claim - do not infer from adjacent
> activity.

The "say nothing when there is nothing" clause is what stops a matrix from filling every
cell with plausible fiction. Never omit it.

`engine` must be a live identifier from <https://api.dciphernext.com/llm-engines> carrying
**`aiGeneratedReportSupported: true`** - matrix cells draw on the same engine set as
AI-generated report sections. Fetch the list rather than guessing; see the Engines section
of `reports.md` for the full selection procedure.

Matrix cells are generated one per cell, so engine cost scales with rows x columns. Check
`costTier` before running a 20 x 8 matrix on a high-tier engine - that is 160 generations,
not one.

## Scenario

Morphological scenario analysis built on a radar. Required: `dimensions`,
`enableAdditionalDimensions`, `radarWorkbenchId`, `id`, `workbenchId`.

```
dimensions: [ { id, title, enableAdditionalOptions,
                options: [ { id, title } ] } ]
scenarioFocus: freeform steer injected into the generation prompt
language: full English name
radarWorkbenchId: the radar the scenarios are grounded in   <- required
```

A scenario workbench without a populated radar produces generic futures. Build and read
the radar first.

Dimensions should be genuinely uncertain and genuinely independent. "Regulation strict vs
permissive" is a dimension; "our strategy succeeds vs fails" is an outcome, and putting an
outcome on an axis collapses the analysis.

`run_scenario_analysis({ project_id, workbench_id, projection })` returns every
combination, each with `likelihood_score` and `impact_score` (0-1) plus rationales.
**`title` and `description` are only generated for `highlighted: true` scenarios** - the
rest are scored combinations with null titles. Project accordingly.

## Reading results: ask for a projection, then check it

Every result tool accepts `projection: { pipeline: [...] }` in MongoDB aggregation syntax,
applied to the value inside `result` - do not project `result.*` paths. Without a
projection these payloads are large enough to crowd out the analysis: a radar of 27 bubbles
came to 339,571 characters, mostly `references`, `examples` and the per-period `sizeScale`.

**Projection did not take effect when tested.** Three pipelines against that radar - a
`$limit` plus `$map`, a `$slice` inside `$map`, and the bare `[{ $project: { title: 1 } }]` -
each returned the identical full payload. Whether that is the server or the connector is not
established. So the pipelines below are the documented shape and are worth sending, but treat
them as unverified: check the size and fields of what comes back, and if it is the full
payload, work with it rather than resending variants. Where the client saves an oversized
result to a file, read the file with a script (`jq` or Python) and pull out only the fields
you need; never paste the whole result into the analysis.

Radar, trimmed to what a briefing needs:

```json
{ "pipeline": [
  { "$project": {
      "title": 1,
      "bubbles": { "$map": {
        "input": { "$slice": ["$bubbles", 8] },
        "as": "b",
        "in": { "title": "$$b.title", "summary": "$$b.summary",
                "count": "$$b.count", "angularScale": "$$b.angularScale",
                "radialScale": "$$b.radialScale",
                "url": { "$arrayElemAt": ["$$b.references.url", 0] } } } } } } ] }
```

Matrix, only the cells with substance:

```json
{ "pipeline": [
  { "$match": { "size": { "$gt": 0 } } },
  { "$project": { "row": 1, "column": 1, "output": 1,
                  "sources": { "$slice": ["$references.url", 3] } } } ] }
```

Landscape, topics at one level only:

```json
{ "pipeline": [
  { "$project": { "topics": { "$filter": {
      "input": "$topics", "as": "t", "cond": { "$eq": ["$$t.level", 3] } } } } } ] }
```

Scenario, highlighted only:

```json
{ "pipeline": [
  { "$project": { "scenarios": { "$filter": {
      "input": "$scenarios", "as": "s", "cond": "$$s.highlighted" } } } } ] }
```
