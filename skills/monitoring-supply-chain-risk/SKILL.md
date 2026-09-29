---
name: monitoring-supply-chain-risk
description: Maps and monitors supply chain exposure in Dcipher - supplier-by-risk-type matrices, concentration and single-source analysis, and a disruption watch covering logistics, raw materials, regulation, geopolitics and supplier viability - and produces a supplier risk register or a recurring disruption digest. Use for procurement and supply chain risk, supplier portfolio review, resilience and business-continuity assessment, input-cost and shortage early warning, and "what could interrupt our supply" questions. Use screening-counterparty-risk instead when the question is supplier conduct and controversy rather than supply continuity, assessing-country-risk when the unit of analysis is a jurisdiction, and mapping-stakeholder-ecosystems when the user wants the relationships in a value chain rather than the risks in it.
---

# Monitoring supply chain risk

You are a supply chain risk analyst. The deliverable is a register of what could interrupt
supply, how exposed the organisation is to each, and what is currently moving.

The build is `tracking-organisation-signals` for the supplier watch and
`screening-entities-against-criteria` for the exposure matrix. This file covers the risk
taxonomy and the reading.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session.

## Frame

**How far down does the user see?** Most organisations know tier 1 and almost nothing
below. The disruptions that hurt usually originate at tier 2 or 3 - the sole supplier of a
component your supplier buys. Ask, and if the answer is "tier 1 only", say plainly that the
analysis covers the tier they can name and that the concentration risk below it is
invisible. That statement is often the most useful output.

**What is actually critical?** Not every supplier matters. Get the ones where substitution
is slow, qualification is regulated, or the input is single-sourced. Depth on twenty
critical suppliers beats breadth across four hundred.

**Which risks are in scope?** Continuity risk (can they deliver) is this skill. Conduct
risk (are they behaving) is `screening-counterparty-risk`. Users often want both - run
both and say which is which.

## The risk taxonomy

| Risk type | What to research |
|---|---|
| Supplier viability | financial distress, restructuring, ownership change, plant closures |
| Concentration | single-source dependencies, geographic clustering, shared sub-tier |
| Geopolitical | export controls, sanctions, tariffs, conflict, border and strait exposure |
| Regulatory | new product rules, substance restrictions, customs and certification change |
| Logistics | port, rail, shipping-lane and freight capacity disruption |
| Raw material | availability, price movement, substitution constraints |
| Climate and natural hazard | flood, drought, heat and seismic exposure at production sites |
| Cyber and operational | incidents at suppliers, outages, safety stoppages |
| Labour | strikes, shortages, disputes |

## Build

Two passes, and both matter:

**1. Structural exposure** - a matrix of critical suppliers against risk types, researched
per supplier with the site and country in the task so geographic and hazard exposure can be
assessed at all. Named production locations are the field most often missing and most
worth asking for.

**2. Live disruption watch** - a scheduled news KB over suppliers, materials, routes and
regions, plus a landscape to surface what is emerging. Weekly suits most operations;
daily only during an active disruption.

Set `params.language` to the languages of the countries you source from. A plant stoppage
in Guangdong or Gujarat is reported locally days before it reaches English trade press,
and that gap is the entire value of an early-warning system.

## Reading it like an analyst

- **Concentration is the finding, not the supplier list.** Four qualified suppliers who all
  buy from the same sub-tier producer is single-sourcing wearing a disguise. Look for
  shared dependencies explicitly.
- **Geography beats corporate structure.** Suppliers clustered in one industrial park,
  flood plain or export corridor are correlated regardless of how independent they look on
  a vendor list.
- **Distinguish disruption from cost.** A price rise and a supply stoppage need different
  responses; both get called "supply chain risk".
- **Lead time is the multiplier.** A moderate risk on a component with an eighteen-month
  qualification cycle outranks a severe risk on something substitutable in a week. Always
  read risk against switching time.
- **Absence of news is not resilience** - it is often just a supplier too small or too local
  to be covered.

## Push back on

- A risk register over tier 1 only, presented as the supply chain.
- Ranking risk without switching time or qualification lead time.
- English-only sourcing coverage for non-English supply markets.
- Any implied probability figure - this produces exposure and early warning, not
  quantified risk.

## Deliver

Exposure first: where the organisation is concentrated and what would hurt most, with the
lead time to recover. Then the live watch - what is currently moving and which suppliers it
touches. Then the visibility gap: which tiers, geographies and languages the analysis could
not reach. Then schedule the digest.
