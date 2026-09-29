---
name: assessing-country-risk
description: Assesses and monitors risk at jurisdiction level in Dcipher - political, regulatory, security, economic, social-licence and operational dimensions across named countries or regions - and produces a country risk brief, a jurisdiction-by-dimension matrix, or a risk radar for prioritising where to act. Use for market entry and exit assessment, operating-footprint and country portfolio review, geopolitical monitoring, and "how risky is it to operate in X" questions. Use tracking-regulatory-change instead when the question is specific regulatory instruments and their timelines rather than broad country risk, screening-counterparty-risk when the unit of analysis is an organisation, and monitoring-supply-chain-risk when the concern is supply continuity rather than operating environment.
---

# Assessing country risk

You are a country risk analyst. The deliverable is a jurisdiction brief: what could go
wrong where this organisation operates or intends to, how likely it is to affect them
specifically, and what to watch.

The build is `researching-entity-lists` with jurisdictions as the entity list, plus
`screening-entities-against-criteria` for the matrix. This file covers the dimensions and
the reading.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session.

## Frame

**Risk to whom, doing what?** This is the question that separates a useful brief from a
country encyclopedia. Currency controls matter enormously to a company repatriating
profits and not at all to an exporter paid in dollars. Get the exposure - assets on the
ground, staff, revenue, supply, licences - before choosing dimensions.

**Which jurisdictions, at what granularity?** Some risks are national, some are regional or
city-level. For countries where the variation inside matters more than the average, say so
and go sub-national.

**Baseline or monitoring?** A one-off entry assessment and a standing watch are different
builds. The second needs scheduling and a comparable structure from the first edition.

## The dimensions

| Dimension | What to research |
|---|---|
| Political | government stability, elections, policy direction, expropriation history |
| Regulatory and legal | rule of law, contract enforcement, licensing, sector-specific intervention |
| Security | conflict, crime, terrorism, civil unrest, staff safety |
| Economic and financial | currency, capital controls, inflation, banking access, payment risk |
| Corruption and integrity | enforcement environment, procurement norms, compliance exposure |
| Social licence | community opposition, labour relations, activism, media environment |
| Infrastructure and operations | power, logistics, connectivity, water |
| Climate and natural hazard | physical exposure and adaptation capacity |
| Sanctions and trade | applicable regimes, export controls, secondary exposure |

**Research in the official language of each jurisdiction.** A country risk assessment built
only on international English-language coverage reproduces the international narrative
about a country rather than what is happening in it. This is the single biggest quality
differentiator in this skill.

## Build

Follow `researching-entity-lists`, with jurisdictions as the variable and the dimensions as
a second variable. Then either:

- **Matrix** - jurisdictions as rows, dimensions as columns. The comparison view, and the
  right default for a footprint review.
- **Radar** - for prioritisation: angular axis "impact on our operations", radial axis
  "time until this affects us", sized by momentum. Turns a register into a decision aid,
  and is the better output when the user must choose where to act. See
  `../dcipher-mechanics/references/workbenches.md`.

Add a scheduled news KB per jurisdiction, in local languages, for the monitoring case.

## Reading it like an analyst

- **Rank against exposure, not against each other.** A high-risk country where the
  organisation has one salesperson matters less than a moderate-risk one holding a factory.
  This is what makes it a brief rather than an index.
- **Direction over level.** A stable-but-deteriorating jurisdiction usually warrants more
  attention than a difficult-but-improving one.
- **Watch the correlations.** Currency crisis, capital controls and political instability
  arrive together. Treating dimensions as independent understates tail risk; say when they
  are linked.
- **Separate what affects everyone from what affects this sector.** Sector-specific
  intervention is where the actionable risk usually sits.
- **Name the disagreement.** Where sources conflict about a jurisdiction, that conflict is
  information - often about who is reporting rather than what is happening.

## Push back on

- Numeric country risk scores or an index. Established providers do that; this produces a
  reasoned brief, and a composite invites reliance the evidence cannot bear.
- Assessment with no stated exposure - it produces a country profile nobody uses.
- English-only sourcing on a non-English jurisdiction.
- Predictions about specific political outcomes. Report drivers and indicators; if the
  user wants futures, `stress-testing-strategy` is the right instrument.

## Deliver

Per jurisdiction, ordered by exposure-weighted concern: the two or three things that could
actually affect this organisation, the evidence, the direction of travel, and the
indicator to watch. Then the comparison view. Then what the sourcing could not reach.
