---
name: mapping-stakeholder-ecosystems
description: Maps and tracks the actors in an ecosystem, arena or value chain in Dcipher - companies, partners, suppliers, integrators, regulators, NGOs, funders, research institutions, industry bodies and media - covering who they are, what positions they hold, how they relate to each other, and what they have been doing. Produces an actor register, a landscape of who is talking about what, position and relationship matrices, and a recurring ecosystem or stakeholder digest. Use for ecosystem and arena mapping, partner and integration ecosystem mapping, stakeholder and coalition analysis, influence mapping, and "who's doing what in this space" questions. Use mapping-market-landscapes instead when the question is market segments and commercial positioning rather than actors and the links between them, tracking-competitor-moves when the actors are named commercial rivals, monitoring-narratives when the interest is what is being said rather than who is doing it, and mapping-funding-landscapes when the question is specifically who funds what.
---

# Mapping stakeholder ecosystems

You are an ecosystem analyst. The deliverable is an actor register: who is in this arena,
what position each holds, how they connect, and what has changed - refreshed on a cadence as
a digest.

Be exact about what the platform draws. There is no network diagram. The landscape is a
semantic map of documents, so it shows who is talking about what; positions and
relationships are held in matrices and in the register itself.

The arena can be commercial or political, and the method is the same. A partner ecosystem -
who integrates with, resells, supplies and funds whom - is the same analysis as a policy
arena with different actor types and different relationship types. Both are covered here.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## What makes this different from a market map

`mapping-market-landscapes` answers "how is this market structured". This skill answers
"who are the actors and how do they relate". The unit is the **actor and the relationship**,
not the segment. An ecosystem map that is just a categorised list of organisations has
failed - the relationships are the product.

## Frame

**The arena.** An issue, a technology, a policy domain, a geography, a value chain. Wider
than a market: a market has buyers and sellers, an arena has everyone with a stake.

**The actor types.** Users almost always under-scope this, usually to companies. Propose
the full set and let them cut:

| Actor type | Why they matter | Usually forgotten? |
|---|---|---|
| Companies and incumbents | commercial power | no |
| Partners, resellers, integrators | route to market, lock-in | often |
| Suppliers and sub-tier providers | dependency, leverage | often |
| Startups and challengers | direction of travel | sometimes |
| Regulators and agencies | rule-setting power | no |
| Legislators and political actors | agenda-setting | often |
| Industry associations and lobbies | coordinated positions | often |
| NGOs and advocacy groups | narrative and legitimacy power | often |
| Research institutions and universities | evidence supply | usually |
| Funders, investors, philanthropies | resource allocation | usually |
| Standards bodies and consortia | technical gatekeeping | usually |
| Media and named journalists | amplification | usually |
| Individuals - experts, activists, executives | disproportionate influence | usually |

**The relationship types that matter.** Commercial: partners with, integrates with,
resells, supplies, competes with, has an exclusive with, invests in. Institutional: funds,
regulates, lobbies against, sits on the board of, co-publishes with, is a member of,
publicly supports or opposes.

For a partner ecosystem the commercial set is the analysis; for a policy arena the
institutional set is. Most real arenas need both.

**Purpose.** Coalition building, influence strategy, risk mapping, or market entry. This
decides what you record about each actor.

## Build

**1. Establish the actor list.** If the user has one, take it and challenge the omissions
against the table above. If not, derive it: a news KB over the arena, then read the recurring
organisations out of the landscape's topic examples and by asking the research bot who is
mentioned most. State plainly that a derived list reflects who is *visible*, which over-represents the media-active and under-represents the
quietly influential - often the actors that matter most.

**2. Research each actor in parallel.** This is `researching-entity-lists` machinery
applied to actors:

```json
{
  "kbName": "<arena> - actor profiles",
  "task": "Profile $actor as a participant in <arena>. Cover: what type of organisation they are and their formal role; their stated public position on <the core issues>, quoted where possible; their activities and interventions in the last 18 months with dates; their formal and informal relationships with other actors in this arena - funding given or received, partnerships, memberships, board overlaps, coalitions, public endorsements and public opposition, each named specifically; their named key individuals and spokespeople; and their sources of influence - regulatory authority, funding, membership, expertise or convening power. Cite every claim with a source URL and a date. Research in the local language of the arena. Where a position is not publicly stated, write 'no public position identified' rather than inferring one from their sector.",
  "variables": [
    { "label": "actor", "values": ["..."] },
    { "label": "actor_type", "values": ["...one type per actor, same order..."] }
  ],
  "multivariablePolicy": "aligned",
  "selectedSources": ["web", "news"]
}
```

Refer to `$actor_type` in the task as well (for example "Profile $actor, a $actor_type, as a
participant in ..."). The `aligned` policy pairs the two lists position by position, so every
document carries both an `actor` field and an `actor_type` field. That is what lets the
landscape be coloured by type in step 3.

The relationship clause is what makes this an ecosystem analysis rather than a list of
profiles. Do not drop it.

Add a news KB over the arena for what has been happening, and a social KB where the arena
has a live public debate.

**3. Landscape, coloured by actor type.** The landscape is a semantic map, not a network.
Every dot is a document, placed by what it is about, and the labelled clusters are topics.
Fetch the seeded landscape workbench and set `colorField` to the `actor_type` field (confirm
the field name with `fetch_project_metadata_schema` and a first call). Colouring answers
**who is talking about what**: a cluster of one colour is a topic one constituency owns, and a
cluster of mixed colours is a topic where different kinds of actor are all engaged.

It does not show who agrees. Two actors in the same cluster are talking about the same thing
and may be on opposite sides of it, so read positions from the matrix in step 4. Colour by
`actor` itself instead when the list is short enough to read, about a dozen values.

**4. Position matrix.** Actors as rows, the contested issues as columns.

```
instruction: "State $row's public position on $column using only the attached sources.
Quote their own words where available, with a date and source. If they have taken no public
position on this issue, write 'no public position identified' - do not infer a position from
their sector, their allies, or their positions on other issues."
summarizeColumns: true
```

`summarizeColumns` gives the coalition read per issue - who lines up with whom - which is
usually the actual question behind the request.

**5. Relationship matrix, for the core actors.** Relationships are text in the profiles, and
nothing in the platform draws them as a network. The way to see them together is a matrix
with the same actors on both rows and columns:

```
instruction: "Describe the publicly documented relationship between $row and $column:
funding, partnership, membership, board overlap, coalition, endorsement or opposition, each
with its date and source. If $row and $column are the same actor, write 'same actor'. If no
relationship is documented, write 'none documented' - do not infer one from sector or
geography."
```

N actors is N x N cells, each one a generation, so keep this to the dozen or so who matter and
check the engine's `costTier` first. For a drawn network, take the register into a diagramming
tool.

**6. Movement over time.** Bump with `sourceType: "document"` over the actor field shows
whose voice is growing in the arena. Needs timestamps; news KBs have them.

## The recurring digest

Stakeholder tracking is ongoing by nature, and the newsletter is the deliverable people
actually consume. Set it up rather than offering it:

- Schedule the news KB (weekly or monthly) and the research-agent KB (monthly or quarterly
  - positions move slower than events).
- Build the digest once as a report: what changed, who moved, new actors, new coalitions,
  and what it means for the user's position.
- `create_insight_booster_report_template` from it, then each period is one call.

See `../dcipher-mechanics/references/reports.md`.

## Reading it like an analyst

- **Position changes are the signal.** An actor who has moved is more newsworthy than one
  who has not. Compare against the previous run - which is the argument for templating
  early.
- **Find the bridges.** An actor whose documents span several otherwise-separate clusters is
  talking across the arena, and one that shows relationships on both sides of a divide in the
  relationship matrix links communities. Both are worth engaging; check which you have.
- **An actor alone in its own cluster is either irrelevant or early.** Work out which.
- **Silence is a position.** An actor with no public stance on a contested issue has
  usually chosen that. Report it as a finding, not a data gap.
- **Do not confuse visibility with influence.** The loudest actor in the corpus is often not
  the one who decides. Weight formal authority, funding and convening power separately from
  media presence, and say which you are reporting.
- **Name individuals where the sources do.** Arenas are moved by people, and the individual
  is often the actionable unit.

## Push back on

- An actor list containing only companies.
- Inferring a position from an actor's sector or allies rather than their own statements.
- Ranking influence by document count.
- Relationship claims without a source - these are the most consequential and the easiest
  to invent. Every relationship in the register needs a URL.
- Presenting the landscape as a network, or as agreement. Proximity means two sets of
  documents are about the same thing, not that the actors think the same.

## Deliver

The actor register grouped by type and position, with the landscape as a picture of who
talks about what, then the coalitions, then the relationships
that matter with their evidence, then who has moved since last time, then the actors nobody
has mapped yet. Sources on every relationship claim. Then set up the recurring digest.
