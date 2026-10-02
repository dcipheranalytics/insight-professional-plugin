# Reports

A report is an ordered set of sections, each generated independently against a chosen set
of knowledge bases, with citations.

## Two entry points

**From scratch, when the structure is known:**

```
create_insight_booster_workbench({ id, name, type: "report" })
update_insight_booster_report({ id, workbenchId, sections: [...] })
get_report_result({ project_id, workbench_id, payload: {} })
```

**From the data, when it is not:**

```
generate_report_sections_from_landscape({ project_id, interest_area })
```

This proposes sections grounded in what the project actually contains. Use it when the
user has a corpus but no outline, then edit the proposal - it is a starting point, not a
deliverable.

## Section fields

`update_insight_booster_report` requires **every** field on each section object. There are
no defaults; omitting one is a validation error.

```
id                      stable, preserved across updates
title                   heading in the output
inputs                  KB ids this section retrieves from
sheetInputs             ids of attached xls/xlsx sheets - pass [], see below
viewInputs              view ids scoping which documents are eligible ([] if none)
instruction             the prompt for this section
exampleOutput           format/tone exemplar ("" if none)
style                   per-section style, overrides report defaultStyle ("" if none)
includeReferences       bool
queryOptimization       bool - beta, optimises retrieval
longContextMode         bool - more source material per section
agenticRag              bool - beta; an agent decides retrieval. OVERRIDES queryOptimization,
                        orderSensitive, mode and outputMode; needs an engine with
                        ragAgentSupported: true; incompatible with sheetInputs
engine                  see Engines below - fetch the live list, do not guess
mode                    Creative | Balanced | Precise
orderSensitive          bool - prioritise `inputs` in listed order
outputMode              regular | comparison
```

Report-level: `defaultStyle`, `executiveSummary`, `inlineCitations`,
`contentDistributionOptimization`, `generatePodcast` (beta), `variables`, `sheets`.

`sheets` and `sheetInputs` attach uploaded spreadsheet files, which cannot be uploaded
through these tools - pass `sheets: []` and `sheetInputs: []` and source every section from
knowledge-base `inputs` instead.

`variables` are `$`-prefixed placeholders substituted into instructions and styles - the
mechanism that makes a template reusable across clients, periods or segments. Define
`$client`, `$period`, `$market` once rather than editing ten instructions.

### Engines

**The authoritative list is <https://api.dciphernext.com/llm-engines>.** Fetch it rather
than working from memory or from any list written down in this repository - engines are
added, deprecated and renamed, and a stale identifier is rejected outright.

The response has an `engines` array; each entry carries the identifier in `engine` plus
capability flags. The ones that matter:

| Field | Use it for |
|---|---|
| `aiGeneratedReportSupported` | **Report sections and matrix cells both require `true`.** A hard filter - not every accepted engine can generate them. |
| `ragAgentSupported` | Required when a section sets `agenticRag: true`. A much smaller set than the report-capable one. |
| `costTier` | `high` or `low` - the cost decision, without doing arithmetic on unit costs |
| `generatorMaxTokens` | Context size. Relevant when a section sets `longContextMode` or draws on a large corpus |
| `deprecationDate` | Present on engines being retired. Do not select one for a report that will be re-run on a schedule |
| `aliases` | Alternative identifiers the API also accepts |
| `label`, `provider` | What to call it when telling the user which engine you picked |

So the selection procedure is: fetch the list, filter to
`aiGeneratedReportSupported: true`, filter again to `ragAgentSupported: true` if the
section uses agentic RAG, drop anything carrying a `deprecationDate`, then choose on
`costTier` and `generatorMaxTokens`.

**The same procedure governs the matrix `engine` field** - matrix cells use the
report-capable engine set. Agentic RAG does not apply there, so the second filter is only
ever a report concern.

**If you cannot reach the endpoint**, do not guess an identifier. Read the engine already
configured on the workbench or template with `get_insight_booster_workbench` and reuse it,
or ask the user. A rejected engine fails the whole section.

Judgement, once the list is filtered: a high-tier engine for analytical and synthesis
sections, a low-tier one for mechanical extraction where cost matters. Do not mix engines
within a report without a reason - the voice shifts noticeably between sections, and
readers notice it before they notice the analysis.

### Writing section instructions

`mode: "Precise"` for anything factual - competitor activity, regulatory summaries,
financials. `"Balanced"` for synthesis. `"Creative"` almost never in a BI deliverable.

Set `includeReferences: true` and `inlineCitations: true` on any section a client will act
on. An uncited BI claim is unusable regardless of whether it is correct.

Scope `inputs` per section rather than pointing every section at every KB. A "market
context" section drawing on the news KB and a "company positions" section drawing on the
research-agent KB produce a far better report than both sections reading everything.

Instructions carry the same anti-hallucination clause as matrix cells: say what to do when
the sources are silent.

Keep style and tone out of `instruction`; put them in `style` or `defaultStyle`, and use
`exampleOutput` to show the shape wanted. Put a "/" in a `title` to make a subsection under
the part before it. `outputMode: "comparison"` needs at least two KBs in `inputs`. With
`orderSensitive`, list the KB that should dominate first. Generate one section on its own
and read it before firing the full report.

## Templates and recurrence

```
create_insight_booster_report_template({ name, reportId })   # derive from a good report
generate_insight_booster_workbench_report_from_template({ id, workbenchId, templateId })
update_insight_booster_report({ templateId, ... })           # edit the template itself
list_insight_booster_report_templates / list_owned_insight_booster_report_templates
delete_insight_booster_report_template
validate_insight_booster_report_name
```

Pass **either** `templateId` (edit a template) **or** `id` + `workbenchId` (edit a
workbench's report). Never both. `name` applies to templates only.

This is where the recurring deliverable lives - the monthly CI update, the quarterly
investment memo. Build the report once, get it right with the user, template it, and every
subsequent period is one call against a refreshed KB. When a user asks for anything they
will want again, template it and tell them you did.

## Reading

`get_report_result` returns an array of section bodies: `output` (text), `references`
(`source`, `url`), `match_score` (reference match count; 0 for agentic sections). Pass
`payload: { sectionId }` to preview a single section - do that while iterating rather than
regenerating the whole report.
