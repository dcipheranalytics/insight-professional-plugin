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
- `growth` has no config tool - it reads the landscape and takes `broadness_level` at
  result time.
- `update_insight_booster_workbench_ui_config` handles display-only settings; it never
  changes the analysis.

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

`outlierPolicy: "always"` for a noisy news corpus, `"never"` when the periphery is the
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
angularScaleParam: { openEndedDefinition: "Impact on the client's core business" }
radialScaleParam:  { openEndedDefinition: "Time until widespread commercial impact" }
sizeScaleParam:    { predefinedMetric: "momentum" }   # or "volume"
```

Higher angular values sit more clockwise within the sector; higher radial values sit
further from the centre. `momentum` is rate of acceleration over the period, `volume` is
raw data quantity in the period. **Momentum needs timestamps and enough history** - on a
three-week corpus it is noise. Use `volume` or fix the corpus.

Alternatively `aggregateMetadataDefinition: { field, aggregateFunction }` (`sum`, `mean`,
`max`) to drive an axis off a real numeric field such as funding amount.

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

No config tool. `get_growth_result({ project_id, workbench_id, broadness_level })` returns
topics with `growths` and `volumes` keyed by time bucket.

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
documents that row or column may draw on.

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

`engine` must be one of the enumerated identifiers - see `reports.md`.

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

## Reading results: always project

Every result tool accepts `projection: { pipeline: [...] }` in MongoDB aggregation syntax,
applied to the value inside `result` - do not project `result.*` paths. Without a
projection these payloads are large enough to crowd out the analysis.

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
