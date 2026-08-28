---
name: managing-insight-projects
description: Lists, inspects, clones, renames, archives, restores and tidies Dcipher Insight Booster projects, workbenches, views, filters and report templates. Use for Dcipher housekeeping and inventory requests - "what projects do we have", "what's in this project", "clone this for Q3", "archive the old ones", "reorder these workbenches". Use querying-knowledge-bases instead when the user wants an answer from a project's content rather than to manage the project itself.
---

# Managing insight projects

Housekeeping. Low glamour, high value: it is what stops a sixth near-duplicate project
being created because nobody checked.

## Inventory

```
list_insight_boosters                      # start here, always
get_insight_booster(id)                    # detail, workbenches, config
list_insight_booster_knowledge_bases(id)   # what data is attached
list_archived_insight_boosters
list_knowledge_bases({ nameContains })     # org-wide KBs, and which projects use them
list_files
list_insight_booster_report_templates(...) / list_owned_insight_booster_report_templates
```

When asked "what do we have", do not just dump the list. Group it: active vs stale (check
update timestamps), which projects share knowledge bases, which are fed by scheduled flows
and are therefore still accumulating cost and data, and which look like duplicates of each
other. That reading is the actual deliverable.

## Reuse before creating

Cloning a configured project is nearly always better than rebuilding one - filters,
workbench configuration and report templates come with it.

```
clone_insight_booster        # new period, new segment, new client, same analysis
rename_insight_booster       # validate_insight_booster_name first
```

Naming convention worth proposing if a team has none: `<subject> - <analysis type> - <period>`.
Duplicate projects are almost always a naming failure.

## Structure

```
create_insight_booster_workbench / rename_insight_booster_workbench
delete_insight_booster_workbench
reorder_insight_booster_workbenches         # order = the reading order of the story
get_insight_booster_workbench({ id, workbenchId })
update_insight_booster_workbench_ui_config  # display only, never the analysis
```

Workbench order is not cosmetic - it is the narrative sequence a colleague opening the
project will read. Context, then finding, then implication.

Views (saved filter states) let one project serve several audiences without cloning:

```
validate_insight_booster_view_name -> create_insight_booster_view
update_insight_booster_view / delete_insight_booster_view
```

Attachments and filters:

```
attach_knowledge_base_to_insight_booster / detach_knowledge_base_from_insight_booster
detach_all_knowledge_bases_from_insight_booster
set_insight_booster_knowledge_base_filters
delete_insight_booster_filter
```

## Archiving

```
archive_insight_booster / restore_insight_booster
```

Archiving is reversible; deleting workbenches, views, templates and filters is not.

**Confirm before anything destructive, every time.** Before deleting or detaching, show the
user what it contains and what depends on it. Specifically:

- Detaching a KB changes every workbench in the project, not just the one in view.
- A scheduled flow keeps producing new timestamp-suffixed KBs whether or not anyone is
  reading them. When tidying, check for scheduled flows behind the clutter - otherwise the
  same mess regenerates next week.
- Deleting a report template breaks the recurring deliverable built on it.

Never batch-delete on a vague instruction. List what would go, get confirmation, then act.
