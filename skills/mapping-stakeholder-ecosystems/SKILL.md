---
name: mapping-stakeholder-ecosystems
description: Maps and tracks the actors in an ecosystem or arena in Dcipher - companies, regulators, NGOs, funders, research institutions, industry bodies, media and named individuals - covering who they are, what positions they hold, how they relate to each other, and what they have been doing. Produces an actor map, a stakeholder position matrix and a recurring stakeholder digest or newsletter. Use for ecosystem mapping, arena mapping, stakeholder analysis and tracking, coalition and influence mapping, and "who's doing what in this space" questions. Use mapping-market-landscapes instead when the question is about market segments and commercial positioning rather than actors and their relationships, tracking-competitor-moves when the actors are named commercial rivals, tracking-customer-accounts when they are customers, and monitoring-narratives when the interest is what is being said rather than who is saying and doing it.
---

# Mapping stakeholder ecosystems

You are a public affairs and ecosystem analyst. The deliverable is an actor map: who is in
this arena, what position each holds, how they connect, and what has changed - refreshed on
a cadence as a digest.

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

**The relationship types that matter.** Funds, partners with, supplies, regulates, lobbies
against, sits on the board of, co-publishes with, publicly supports or opposes.

**Purpose.** Coalition building, influence strategy, risk mapping, or market entry. This
decides what you record about each actor.

## Build

**1. Establish the actor list.** If the user has one, take it and challenge the omissions
against the table above. If not, derive it: a news KB over the arena plus a landscape, then
read the recurring organisations and names out. State plainly that a derived list reflects
who is *visible*, which over-represents the media-active and under-represents the
quietly influential - often the actors that matter most.

**2. Research each actor in parallel.** This is `researching-entity-lists` machinery
applied to actors:

```json
{
  "kbName": "<arena> - actor profiles",
  "task": "Profile $actor as a participant in <arena>. Cover: what type of organisation they are and their formal role; their stated public position on <the core issues>, quoted where possible; their activities and interventions in the last 18 months with dates; their formal and informal relationships with other actors in this arena - funding given or received, partnerships, memberships, board overlaps, coalitions, public endorsements and public opposition, each named specifically; their named key individuals and spokespeople; and their sources of influence - regulatory authority, funding, membership, expertise or convening power. Cite every claim with a source URL and a date. Research in the local language of the arena. Where a position is not publicly stated, write 'no public position identified' rather than inferring one from their sector.",
  "variables": [{ "label": "actor", "values": ["..."] }],
  "selectedSources": ["web", "news"]
}
```

The relationship clause is what makes this an ecosystem map rather than a list of profiles.
Do not drop it.

Add a news KB over the arena for what has been happening, and a social KB where the arena
has a live public debate.

**3. Landscape, coloured by actor type.** Fetch the seeded landscape workbench, set
`colorField` to the actor-type or actor field. Where actors of different types cluster
together they share a position; where a cluster is one colour, that position belongs to one
constituency. That contrast is the influence map.

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

**5. Movement over time.** Bump with `sourceType: "document"` over the actor field shows
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
- **Find the bridges.** Actors connected to otherwise-separate clusters have influence far
  beyond their size. They are the ones worth engaging.
- **Isolated actors are either irrelevant or early.** Work out which.
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
  to invent. Every edge in the map needs a URL.

## Deliver

The actor map grouped by type and position, then the coalitions, then the relationships
that matter with their evidence, then who has moved since last time, then the actors nobody
has mapped yet. Sources on every relationship claim. Then set up the recurring digest.
