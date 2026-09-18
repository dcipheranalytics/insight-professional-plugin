# dcipher-insight-professional-plugin

Claude plugin packaging Dcipher Analytics' Insight Booster MCP connector as a set of
business-intelligence skills. The connector supplies the tools; these skills supply the
analyst judgement about which tools to chain, with what defaults, and what to do with the
results.

**Status: early draft.** The skills are written against the connector's tool schemas but
have not yet been evaluated against real prompts, so treat the tool chains and defaults as
proposals rather than documentation. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the
authoring workflow and [`docs/eval-prompts.md`](docs/eval-prompts.md) for the test set.

## Design

Skills are named after **the recurring deliverable a BI subrole owns**, not after the
subrole and not after the underlying workbench. A user arrives with a task ("what has
Siemens been up to"), not an identity and not a visualisation preference, so descriptions
key on the observable task. The role positioning lives in the SKILL.md body, where it
shapes the output voice without hurting trigger accuracy.

Shared machinery - project setup, KB construction, filters, workbench configuration,
report generation - lives once in `skills/dcipher-mechanics/references/` rather than being
restated in every skill. Use-case skills point at it with a relative path.

## Skills

### Role-mapped deliverables

| Skill | BI subrole | Deliverable | Machinery |
|---|---|---|---|
| `tracking-competitor-moves` | Competitive intelligence | Competitor x trigger matrix, event digest | research agent KB, matrix |
| `scanning-emerging-trends` | Strategic foresight | Trend radar with momentum and impact | news / agent KB, radar, bump |
| `mapping-market-landscapes` | Market analyst | Segment map, player landscape, entry assessment | agent KB over entity lists, landscape |
| `scouting-technologies` | Innovation / R&D | Scouting brief, partner shortlist | agent KB, landscape, radar, matrix |
| `analyzing-customer-voice` | CX insight | Theme and complaint map, issue trends | file / social KB, landscape, growth |
| `monitoring-narratives` | Comms / public affairs | Narrative map, share of voice, reputation report | news + social KB, landscape, bump |
| `screening-acquisition-targets` | Corporate development | Screened target longlist with criteria matrix | agent KB, matrix |
| `tracking-regulatory-change` | Policy / regulatory affairs | Regulatory horizon brief by jurisdiction | agent KB over jurisdictions, matrix, radar |
| `stress-testing-strategy` | Strategy / planning | Scored scenario set with implications | scenario workbench on a radar |
| `researching-entity-lists` | Research operations | Comparable cited dataset across a list, plus synthesis | agent KB over entity lists, landscape, matrix |
| `running-due-diligence` | Diligence / corp dev | Evidence pack on one target, red-flag register | agent KB, news KB, matrix, report |
| `mapping-stakeholder-ecosystems` | Public affairs / ecosystem | Actor map, position matrix, recurring stakeholder digest | agent KB, news KB, landscape, matrix, bump |
| `tracking-customer-accounts` | Account intelligence / KAM | Account x signal matrix, pre-meeting brief, account digest | agent KB, scheduled news KB, matrix |

### Role-neutral

| Skill | Purpose |
|---|---|
| `querying-knowledge-bases` | Answer a question from an existing project. Highest expected trigger volume. |
| `managing-insight-projects` | Inventory, clone, rename, archive, tidy. |

### Internal

| Skill | Purpose |
|---|---|
| `dcipher-mechanics` | Shared reference layer. Not user-facing; loaded when another skill points at it. |

## Layout

```
.claude-plugin/plugin.json
skills/
  dcipher-mechanics/
    SKILL.md
    references/
      knowledge-bases.md        four KB constructors, query building, polling
      projects-and-filters.md   project creation, attachment, the three filter buckets
      workbenches.md            all six workbench configs + result projections
      reports.md                section schema, engines, templates, recurrence
      analyst-standards.md      the quality bar - source adequacy, caveats, delivery
  <one directory per skill>/SKILL.md
docs/
  eval-prompts.md               trigger and quality test prompts
CONTRIBUTING.md
```

## Reading this repository

Start here:

1. `skills/dcipher-mechanics/references/analyst-standards.md` - the quality bar every skill
   inherits: source adequacy, when to push back, how to caveat, how to deliver.
2. The description block of every SKILL.md, **as a set**. Trigger collisions are the main
   design risk and they are only visible when the descriptions are read together.
3. Any single skill body, for how a use case is actually built.
