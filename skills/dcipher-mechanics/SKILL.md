---
name: dcipher-mechanics
description: Internal reference for how Dcipher Insight Booster projects, knowledge bases and workbenches are actually built - tool call order, required parameters, polling, filter buckets and result projections. Not a user-facing skill. Load this when another dcipher-insight skill points here, or when a Dcipher tool call has failed and you need the correct parameter shape.
---

# Dcipher mechanics

Shared machinery for every Dcipher business-intelligence skill. The use-case skills
decide *what* to build and *why*; this file and its references cover *how*.

## The universal shape of a Dcipher analysis

Every deliverable in this plugin is the same five steps. Only steps 1 and 4 differ
between use cases.

1. **Frame** - turn the request into an interest area, an entity list, or a set of
   triggers. This is the analyst work, and it lives in the use-case skill.
2. **Source** - build or find a knowledge base. See `references/knowledge-bases.md`.
3. **Scope** - create the project, attach the KBs, set filters. See `references/projects-and-filters.md`.
4. **Analyse** - create a workbench, configure it, read the result. See
   `references/workbenches.md` and `references/reports.md`.
5. **Deliver** - answer in prose with source URLs, and hand back the project link so
   the user can keep working in the Studio UI.

## Rules that apply everywhere

**Never guess an id.** `list_insight_boosters`, `list_knowledge_bases`,
`get_insight_booster` and `list_insight_booster_knowledge_bases` exist precisely so you
do not have to. Project ids are 24-character hex.

**Check before you create.** Before creating a project, call `list_insight_boosters` and
check whether an existing one already covers the topic. Confirm with the user before
creating a new project unless they explicitly asked for one. The same goes for KBs -
`list_knowledge_bases` with `nameContains` first.

**Everything that fetches data is asynchronous.** All four `create_*_kb` tools return a
`flowId` immediately, not a knowledge base. Poll `get_last_flow_run_status(flowId)` until
it completes; only then do you have a `knowledgeBaseId`. Never assume a KB is ready.
Research-agent runs over a long entity list can take a while - tell the user what you are
waiting on rather than polling silently.

**Timestamps gate half the analyses.** Bump charts, growth analysis, radar momentum and
any "how has this changed" question need timestamped documents. Call
`check_kb_has_timestamp` before promising a time-based analysis, and
`get_project_time_range` before choosing a window.

**Field names come from the schema, not from memory.** Call
`fetch_project_metadata_schema(project_id)` before referencing any metadata field in a
filter, a landscape `colorField`/`sizeField`, or a bump `metadataField`. Custom fields are
addressed as `metadata.<label>`.

**Always project results.** `get_radar_result`, `get_landscape_result`,
`get_matrix_result`, `get_bump_result`, `get_growth_result`, `get_report_result` and
`run_scenario_analysis` all accept a MongoDB aggregation `projection` and all return large
payloads without one. Ask for the smallest set of fields that answers the question. See
`references/workbenches.md` for worked pipelines.

## References

- `references/knowledge-bases.md` - the four KB constructors, their parameter shapes, query
  building, and the polling loop.
- `references/projects-and-filters.md` - project creation, KB attachment, the three filter
  buckets, metadata schema.
- `references/workbenches.md` - landscape, radar, bump, matrix, growth and scenario configs,
  plus result projections.
- `references/reports.md` - report sections, templates, and recurring report generation.
- `references/analyst-standards.md` - the quality bar: source adequacy, language coverage,
  time-window sanity, how to caveat. Read this once; it shapes the output of every skill.
