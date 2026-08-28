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
sheetInputs             ids of attached xls/xlsx sheets ([] if none)
viewInputs              view ids scoping which documents are eligible ([] if none)
instruction             the prompt for this section
exampleOutput           format/tone exemplar ("" if none)
style                   per-section style, overrides report defaultStyle ("" if none)
includeReferences       bool
queryOptimization       bool - beta, optimises retrieval
longContextMode         bool - more source material per section
agenticRag              bool - beta; an agent decides retrieval. OVERRIDES queryOptimization,
                        orderSensitive, mode and outputMode; needs a RAG-capable engine;
                        incompatible with sheetInputs
engine                  see the enum below
mode                    Creative | Balanced | Precise
orderSensitive          bool - prioritise `inputs` in listed order
outputMode              regular | comparison
```

Report-level: `defaultStyle`, `executiveSummary`, `inlineCitations`,
`contentDistributionOptimization`, `generatePodcast` (beta), `variables`, `sheets`.

`variables` are `$`-prefixed placeholders substituted into instructions and styles - the
mechanism that makes a template reusable across clients, periods or segments. Define
`$client`, `$period`, `$market` once rather than editing ten instructions.

### Engines

`gpt-5.6-sol`, `gpt-5.6-terra`, `gpt-5.6-luna`, `gpt-5.5`, `gpt-5.4`, `gpt-5.2`,
`gpt-5.1`, `gpt-5`, `gpt-5.4-mini`, `gpt-5.4-nano`, `gpt-5-mini`, `gpt-5-nano`, `gpt-4.1`,
`gpt-4.1-mini`, `gpt-4.1-nano`, `gpt4-128k-omni`, `gpt4-128k-omni-mini`,
`cohere-command-r-plus`, `cohere-command-r`, `gemini-pro`, `gemini-flash-lite`,
`gemini-flash`, `claude-fable`, `claude-opus`, `claude-sonnet`, `claude-haiku`,
`google/gemma-4-31B-it`, `meta-llama/Llama-3.1-8B-Instruct`, `Qwen/Qwen3.5-9B`,
`Qwen/Qwen3.5-27B`.

Pick from this list verbatim; a free-typed model name is rejected. Use a frontier engine
for analytical sections and a small one for mechanical extraction if cost matters. Do not
mix engines within a report without reason - the voice shifts noticeably between sections.

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
