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

The plugin is three layers. **Tool mechanics** - project setup, KB construction, filters,
workbench configuration, report generation - live once in
`skills/dcipher-mechanics/references/`. **Analytical method** - the recurring patterns
behind whole families of deliverables - lives in three generic skills. **Role-mapped
skills** reference both and add the framing, the defaults, the judgement and the reading
that make a deliverable specific to a subrole. Nothing is restated twice, so nothing
drifts.

## Skills

### Generic method skills

Each holds one analytical pattern once. Usable directly when a request is generic;
referenced by the role-mapped skills for the build.

| Skill | Pattern | Referenced by |
|---|---|---|
| [`analyzing-content-themes`](skills/analyzing-content-themes/SKILL.md) | Any text corpus into a theme map, trended | [customer voice](skills/analyzing-customer-voice/SKILL.md), [narratives](skills/monitoring-narratives/SKILL.md), [research landscapes](skills/mapping-research-landscapes/SKILL.md) |
| [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md) | Named organisations x signals, as a recurring digest | [competitors](skills/tracking-competitor-moves/SKILL.md), [customer accounts](skills/tracking-customer-accounts/SKILL.md), [portfolio](skills/monitoring-portfolio-companies/SKILL.md), [counterparty risk](skills/screening-counterparty-risk/SKILL.md), [supply chain](skills/monitoring-supply-chain-risk/SKILL.md) |
| [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md) | Entities scored against criteria, tiered | [acquisition targets](skills/screening-acquisition-targets/SKILL.md), [counterparty risk](skills/screening-counterparty-risk/SKILL.md), [supply chain](skills/monitoring-supply-chain-risk/SKILL.md), [country risk](skills/assessing-country-risk/SKILL.md) |

### Role-mapped deliverables

| Skill | BI subrole | Deliverable | Machinery |
|---|---|---|---|
| [`tracking-competitor-moves`](skills/tracking-competitor-moves/SKILL.md) | Competitive intelligence | Competitor x trigger matrix, event digest | agent KB, news KB, bump, matrix, report |
| [`scanning-emerging-trends`](skills/scanning-emerging-trends/SKILL.md) | Strategic foresight | Trend radar with momentum and impact | agent KB, news KB, radar, bump |
| [`mapping-market-landscapes`](skills/mapping-market-landscapes/SKILL.md) | Market analyst | Segment map, player landscape, entry assessment | agent KB over entity lists, news KB, landscape, growth, matrix |
| [`scouting-technologies`](skills/scouting-technologies/SKILL.md) | Innovation / R&D | Scouting brief, partner shortlist | agent KB, landscape, radar, matrix |
| [`analyzing-customer-voice`](skills/analyzing-customer-voice/SKILL.md) | CX insight | Theme and complaint map, prioritisation radar, issue trends | agent KB, news KB, social KB, landscape, radar, bump, growth, report |
| [`monitoring-narratives`](skills/monitoring-narratives/SKILL.md) | Comms / public affairs | Narrative map, share of voice, reputation report | news KB, social KB, landscape, radar, bump, growth, report |
| [`screening-acquisition-targets`](skills/screening-acquisition-targets/SKILL.md) | Corporate development | Screened target longlist with criteria matrix | agent KB, landscape, matrix |
| [`tracking-regulatory-change`](skills/tracking-regulatory-change/SKILL.md) | Policy / regulatory affairs | Regulatory horizon brief by jurisdiction | agent KB over jurisdictions, news KB, radar, matrix, report |
| [`stress-testing-strategy`](skills/stress-testing-strategy/SKILL.md) | Strategy / planning | Scored scenario set with implications | agent KB, news KB, scenario workbench on a radar |
| [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | Research operations | Comparable cited dataset across a list, plus synthesis | agent KB over entity lists, landscape, matrix, report |
| [`running-due-diligence`](skills/running-due-diligence/SKILL.md) | Diligence / corp dev | Evidence pack on one target, red-flag register | agent KB, news KB, social KB, matrix, report |
| [`mapping-stakeholder-ecosystems`](skills/mapping-stakeholder-ecosystems/SKILL.md) | Public affairs / ecosystem | Actor register, who-talks-about-what landscape, position and relationship matrices, recurring digest | agent KB, news KB, social KB, landscape, bump, matrix, report |
| [`tracking-customer-accounts`](skills/tracking-customer-accounts/SKILL.md) | Account intelligence / KAM | Account x signal matrix, pre-meeting brief, account digest | agent KB, scheduled news KB, matrix, report |
| [`monitoring-portfolio-companies`](skills/monitoring-portfolio-companies/SKILL.md) | PE / VC / DFI portfolio ops | Portfolio x signal matrix, flag list, portfolio digest | agent KB, scheduled news KB, bump, matrix, report |
| [`screening-counterparty-risk`](skills/screening-counterparty-risk/SKILL.md) | Third-party risk / ESG | Controversy register with severity, escalation shortlist | agent KB, scheduled news KB, matrix, report |
| [`monitoring-supply-chain-risk`](skills/monitoring-supply-chain-risk/SKILL.md) | Procurement / supply chain | Supplier x risk matrix, concentration analysis, disruption digest | agent KB, scheduled news KB, landscape, matrix |
| [`assessing-country-risk`](skills/assessing-country-risk/SKILL.md) | Enterprise risk / strategy | Country risk brief, jurisdiction x dimension matrix, risk radar | agent KB over jurisdictions, scheduled news KB, radar, matrix |
| [`mapping-funding-landscapes`](skills/mapping-funding-landscapes/SKILL.md) | Grants / research development | Funder x topic matrix, opportunity shortlist, priority shift | agent KB over funders, landscape, growth, matrix |
| [`mapping-research-landscapes`](skills/mapping-research-landscapes/SKILL.md) | Research intelligence / science policy | Research front map, institution positions, white space | agent KB, landscape, growth, matrix |

Machinery lists the knowledge-base types exactly as the matrix below shows them, including
those a skill inherits from the skill it builds on, plus the workbenches its own definition
uses.

### Role-neutral

| Skill | Purpose |
|---|---|
| [`querying-knowledge-bases`](skills/querying-knowledge-bases/SKILL.md) | Answer a question from an existing project. Highest expected trigger volume. |
| [`managing-insight-projects`](skills/managing-insight-projects/SKILL.md) | Inventory, clone, rename, archive, tidy. |

### Internal

| Skill | Purpose |
|---|---|
| [`dcipher-mechanics`](skills/dcipher-mechanics/SKILL.md) | Shared reference layer. Not user-facing; loaded when another skill points at it. |

## Dependency graph

Each diagram shows one skill and the skills built on it. An arrow points from a skill to
the skill it builds on. Follow a heading to open that skill's file.

### [`analyzing-content-themes`](skills/analyzing-content-themes/SKILL.md)

```mermaid
flowchart LR
  D0["analyzing-customer-voice"] --> T["analyzing-content-themes"]
  D1["monitoring-narratives"] --> T["analyzing-content-themes"]
  D2["mapping-research-landscapes"] --> T["analyzing-content-themes"]
  classDef base stroke-width:3px
  class T base
```

### [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md)

```mermaid
flowchart LR
  D0["tracking-competitor-moves"] --> T["tracking-organisation-signals"]
  D1["tracking-customer-accounts"] --> T["tracking-organisation-signals"]
  D2["monitoring-portfolio-companies"] --> T["tracking-organisation-signals"]
  D3["screening-counterparty-risk"] --> T["tracking-organisation-signals"]
  D4["monitoring-supply-chain-risk"] --> T["tracking-organisation-signals"]
  classDef base stroke-width:3px
  class T base
```

### [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md)

```mermaid
flowchart LR
  D0["screening-acquisition-targets"] --> T["screening-entities-against-criteria"]
  D1["screening-counterparty-risk"] --> T["screening-entities-against-criteria"]
  D2["monitoring-supply-chain-risk"] --> T["screening-entities-against-criteria"]
  D3["assessing-country-risk"] --> T["screening-entities-against-criteria"]
  classDef base stroke-width:3px
  class T base
```

### [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md)

```mermaid
flowchart LR
  D0["mapping-stakeholder-ecosystems"] --> T["researching-entity-lists"]
  D1["assessing-country-risk"] --> T["researching-entity-lists"]
  D2["mapping-funding-landscapes"] --> T["researching-entity-lists"]
  D3["mapping-research-landscapes"] --> T["researching-entity-lists"]
  classDef base stroke-width:3px
  class T base
```

### [`scanning-emerging-trends`](skills/scanning-emerging-trends/SKILL.md)

```mermaid
flowchart LR
  D0["stress-testing-strategy"] -->|requires a populated radar| T["scanning-emerging-trends"]
  classDef base stroke-width:3px
  class T base
```

### All relationships

Hand-offs are natural next steps and inherit nothing; they are listed here rather than drawn.

| Skill | Builds on | Hands off to |
|---|---|---|
| [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md) | - | [`running-due-diligence`](skills/running-due-diligence/SKILL.md) |
| [`tracking-competitor-moves`](skills/tracking-competitor-moves/SKILL.md) | [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md) | - |
| [`mapping-market-landscapes`](skills/mapping-market-landscapes/SKILL.md) | - | [`tracking-competitor-moves`](skills/tracking-competitor-moves/SKILL.md), [`stress-testing-strategy`](skills/stress-testing-strategy/SKILL.md) |
| [`analyzing-customer-voice`](skills/analyzing-customer-voice/SKILL.md) | [`analyzing-content-themes`](skills/analyzing-content-themes/SKILL.md) | - |
| [`monitoring-narratives`](skills/monitoring-narratives/SKILL.md) | [`analyzing-content-themes`](skills/analyzing-content-themes/SKILL.md) | - |
| [`screening-acquisition-targets`](skills/screening-acquisition-targets/SKILL.md) | [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md) | [`mapping-market-landscapes`](skills/mapping-market-landscapes/SKILL.md) |
| [`tracking-regulatory-change`](skills/tracking-regulatory-change/SKILL.md) | - | [`mapping-stakeholder-ecosystems`](skills/mapping-stakeholder-ecosystems/SKILL.md) |
| [`stress-testing-strategy`](skills/stress-testing-strategy/SKILL.md) | [`scanning-emerging-trends`](skills/scanning-emerging-trends/SKILL.md) | - |
| [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | - | [`querying-knowledge-bases`](skills/querying-knowledge-bases/SKILL.md) |
| [`mapping-stakeholder-ecosystems`](skills/mapping-stakeholder-ecosystems/SKILL.md) | [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | - |
| [`tracking-customer-accounts`](skills/tracking-customer-accounts/SKILL.md) | [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md) | - |
| [`monitoring-portfolio-companies`](skills/monitoring-portfolio-companies/SKILL.md) | [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md) | - |
| [`screening-counterparty-risk`](skills/screening-counterparty-risk/SKILL.md) | [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md), [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md) | - |
| [`monitoring-supply-chain-risk`](skills/monitoring-supply-chain-risk/SKILL.md) | [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md), [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md) | [`screening-counterparty-risk`](skills/screening-counterparty-risk/SKILL.md) |
| [`assessing-country-risk`](skills/assessing-country-risk/SKILL.md) | [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md), [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | [`stress-testing-strategy`](skills/stress-testing-strategy/SKILL.md) |
| [`mapping-funding-landscapes`](skills/mapping-funding-landscapes/SKILL.md) | [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | - |
| [`mapping-research-landscapes`](skills/mapping-research-landscapes/SKILL.md) | [`analyzing-content-themes`](skills/analyzing-content-themes/SKILL.md), [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | - |

Every skill except [`querying-knowledge-bases`](skills/querying-knowledge-bases/SKILL.md) and [`managing-insight-projects`](skills/managing-insight-projects/SKILL.md)
also reads [`dcipher-mechanics`](skills/dcipher-mechanics/SKILL.md) for tool call order, parameters and projections.
[`scouting-technologies`](skills/scouting-technologies/SKILL.md), [`running-due-diligence`](skills/running-due-diligence/SKILL.md) and
[`tracking-regulatory-change`](skills/tracking-regulatory-change/SKILL.md) build on no other skill, and none builds on them.

## Skills by knowledge-base type

**X** means the skill's own definition builds that knowledge-base type, including where it
treats it as optional. **(X)** means it inherits the type from a skill it builds on, following
the solid arrows in the graph above. Dotted arrows are hand-offs and inherit nothing.

The three generic method skills are not listed as rows. They are the source of most
inherited marks: [`tracking-organisation-signals`](skills/tracking-organisation-signals/SKILL.md) builds news and research agent knowledge
bases, [`screening-entities-against-criteria`](skills/screening-entities-against-criteria/SKILL.md) builds research agent ones, and
[`analyzing-content-themes`](skills/analyzing-content-themes/SKILL.md) accepts any corpus, so the skills built on it show only the types
they name themselves.

| Skill | Layer | News | Social | Research agent |
|---|---|:-:|:-:|:-:|
| [`tracking-competitor-moves`](skills/tracking-competitor-moves/SKILL.md) | Role-mapped | X |  | X |
| [`scanning-emerging-trends`](skills/scanning-emerging-trends/SKILL.md) | Role-mapped | X |  | X |
| [`mapping-market-landscapes`](skills/mapping-market-landscapes/SKILL.md) | Role-mapped | X |  | X |
| [`scouting-technologies`](skills/scouting-technologies/SKILL.md) | Role-mapped |  |  | X |
| [`analyzing-customer-voice`](skills/analyzing-customer-voice/SKILL.md) | Role-mapped | X | X | X |
| [`monitoring-narratives`](skills/monitoring-narratives/SKILL.md) | Role-mapped | X | X |  |
| [`screening-acquisition-targets`](skills/screening-acquisition-targets/SKILL.md) | Role-mapped |  |  | X |
| [`tracking-regulatory-change`](skills/tracking-regulatory-change/SKILL.md) | Role-mapped | X |  | X |
| [`stress-testing-strategy`](skills/stress-testing-strategy/SKILL.md) | Role-mapped | (X) |  | (X) |
| [`researching-entity-lists`](skills/researching-entity-lists/SKILL.md) | Role-mapped |  |  | X |
| [`running-due-diligence`](skills/running-due-diligence/SKILL.md) | Role-mapped | X | X | X |
| [`mapping-stakeholder-ecosystems`](skills/mapping-stakeholder-ecosystems/SKILL.md) | Role-mapped | X | X | X |
| [`tracking-customer-accounts`](skills/tracking-customer-accounts/SKILL.md) | Role-mapped | X |  | X |
| [`monitoring-portfolio-companies`](skills/monitoring-portfolio-companies/SKILL.md) | Role-mapped | (X) |  | (X) |
| [`screening-counterparty-risk`](skills/screening-counterparty-risk/SKILL.md) | Role-mapped | (X) |  | (X) |
| [`monitoring-supply-chain-risk`](skills/monitoring-supply-chain-risk/SKILL.md) | Role-mapped | X |  | (X) |
| [`assessing-country-risk`](skills/assessing-country-risk/SKILL.md) | Role-mapped | X |  | (X) |
| [`mapping-funding-landscapes`](skills/mapping-funding-landscapes/SKILL.md) | Role-mapped |  |  | X |
| [`mapping-research-landscapes`](skills/mapping-research-landscapes/SKILL.md) | Role-mapped |  |  | X |
| [`querying-knowledge-bases`](skills/querying-knowledge-bases/SKILL.md) | Role-neutral |  |  |  |
| [`managing-insight-projects`](skills/managing-insight-projects/SKILL.md) | Role-neutral |  |  |  |
| [`dcipher-mechanics`](skills/dcipher-mechanics/SKILL.md) | Internal |  |  |  |
| **Skills using it** | | **14** | **4** | **18** |
| **of which build it directly** | | **11** | **4** | **13** |

Rows with no mark work on an existing project rather than building a knowledge base:
[`querying-knowledge-bases`](skills/querying-knowledge-bases/SKILL.md) and [`managing-insight-projects`](skills/managing-insight-projects/SKILL.md) use what already exists, and
[`dcipher-mechanics`](skills/dcipher-mechanics/SKILL.md) specifies the constructors without using them.

News reaches back 365 days and social roughly 30, so those two columns also show which
skills inherit that limit. There is no file-based column: uploading a user's own documents
is not available through these tools.

## Layout

```
.claude-plugin/plugin.json
skills/
  dcipher-mechanics/
    SKILL.md
    references/
      knowledge-bases.md        three KB constructors, query building, waiting for builds
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

1. [`analyst-standards.md`](skills/dcipher-mechanics/references/analyst-standards.md) - the quality bar every skill
   inherits: source adequacy, when to push back, how to caveat, how to deliver.
2. The description block of every SKILL.md, **as a set**. Trigger collisions are the main
   design risk and they are only visible when the descriptions are read together.
3. Any single skill body, for how a use case is actually built.
