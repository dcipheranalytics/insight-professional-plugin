# Projects, attachments and filters

An Insight Booster project ("project", "IB") is the scope every analysis runs inside: a
set of attached knowledge bases, a set of filters, and one or more workbenches.

## Before creating anything

```
list_insight_boosters                    # is there already a project for this topic?
get_insight_booster(id)                  # what is in it?
list_insight_booster_knowledge_bases     # what is attached?
```

If the user names a project, work inside it. If they do not, list first and check for a
relevant existing one. Confirm before creating a new project unless they explicitly asked
for one. Creating a duplicate project is the single most common failure mode.

## Creating

```
validate_insight_booster_name(name)      # availability check, pass id when renaming
create_insight_booster({ name, tags? })  # name >= 3 chars
```

`create_insight_booster` seeds the project with **one landscape workbench**. Every other
workbench you add yourself.

Name projects for the question, not the tool: "EU battery supply chain - competitor watch"
rather than "Matrix 3". Use `tags` for the team or programme so the project list stays
navigable.

## Attaching knowledge bases

```
attach_knowledge_base_to_insight_booster({
  id: projectId,
  knowledgeBases: [ { knowledgeBaseId, filters?: [...] } ]
})
```

Existing attachments are preserved. Per-KB `filters` are pre-filters scoped to that KB
only - use them when one source needs narrowing and the others do not.

Also available: `set_insight_booster_knowledge_base_filters`,
`detach_knowledge_base_from_insight_booster`,
`detach_all_knowledge_bases_from_insight_booster`.

## Understanding the data before filtering

Run these three, in this order, before any filter or workbench config:

```
fetch_project_metadata_schema(project_id)   # the real field names, union across KBs
get_project_time_range(project_id)          # earliest and latest timestamps
check_kb_has_timestamp(kb_ids)              # which KBs support time analysis at all
```

Custom metadata fields are addressed as `metadata.<label>`. Never reference a field you
have not seen in the schema.

## The three filter buckets

Every filter setter takes a `type`, and the choice changes what the filter does:

| `type` | Effect |
|---|---|
| `pre` | KB-scoped pre-filter. Applies to **all** workbenches. This is the usual choice. |
| `deleted` | Documents ignored by all workbenches. Use for junk you never want back. |
| `excluded` | Documents removed from the landscape but **retained for other workbenches**. Use to declutter a topic map without losing the documents from counts and trends. |

Setters:

```
set_insight_booster_timestamp_filter({ id, type, filter })
set_insight_booster_metadata_filter({ id, type, filters: [...] })   # replaces the bucket
set_insight_booster_document_filter({ id, type, filter })
delete_insight_booster_filter(...)
```

`set_insight_booster_metadata_filter` **replaces** the metadata filters in that bucket -
send the full set you want, not just the new one.

### Filter value shapes

The `values` field is polymorphic. Pick the variant that matches the field:

```
categorical  ["Reuters", "Bloomberg"]
numeric      { min: 10, max: 100 }
semantic     { text: "battery recycling", top_k: 500, threshold: 0.35 }
absolute date{ start_date: "2026-01-01", end_date: "2026-06-30" }
relative date{ period: "month", duration: 6 }
```

Set `revert: true` to invert a filter (keep documents that do *not* match). That is the
clean way to exclude a noisy source or a dominant entity that is drowning everything else.

**Prefer relative date filters** (`{ period, duration }`) on any project that will be
re-run or scheduled - an absolute range silently goes stale.

The semantic filter is underused and often the right tool: a broad KB plus
`{ text: <the actual question>, top_k: N }` beats a narrow keyword query, because the KB
stays reusable for the next question.

## Views

Views are saved filter states, useful when one project serves several audiences or
segments (per region, per business unit).

```
validate_insight_booster_view_name -> create_insight_booster_view
update_insight_booster_view / delete_insight_booster_view
```

Matrix rows/columns and report sections can be scoped to a view via `viewInputs`, which is
how you get "the same analysis, per region" without cloning the project.

## Housekeeping

```
clone_insight_booster      # reuse a configured project for a new period or segment
rename_insight_booster
archive_insight_booster / restore_insight_booster / list_archived_insight_boosters
reorder_insight_booster_workbenches / rename_insight_booster_workbench
delete_insight_booster_workbench
```

Cloning a configured project is almost always better than rebuilding one: the filters,
workbench configs and report templates come with it.
