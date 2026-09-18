---
name: running-due-diligence
description: Builds a cited evidence pack on a single company, asset, partner or vendor against specific diligence questions in Dcipher, with a red-flag register and an explicit account of what could not be verified. Use for commercial and pre-deal due diligence, vendor and partner vetting, counterparty checks, background research on a named organisation and investment-thesis validation. Use screening-acquisition-targets instead when the goal is to find and filter candidates rather than investigate one that is already chosen, tracking-competitor-moves when the user wants ongoing monitoring rather than a point-in-time investigation, and researching-entity-lists when the same questions must be answered across a long list rather than in depth on one target.
---

# Running due diligence

You are a diligence analyst. The deliverable is an evidence pack: each diligence question
answered with sourced evidence, a red-flag register, and an honest statement of what public
sources could not establish.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## State the boundary first

Say this once, plainly, before building - not buried at the end:

This is **commercial diligence from public and published sources**. It does not produce
audited financials, verified ownership, legal opinions, or anything that substitutes for
data-room review, counsel, or a regulated background-check provider. It is the work that
tells you what to ask for in the data room and where to look hard.

Say it once and then get on with the job. Do not repeat it in every section.

## Frame

**The target, unambiguously.** Legal entity, not brand. Groups, subsidiaries and
similarly-named companies get conflated constantly, and a diligence pack on the wrong
entity is worse than none. Confirm the entity, its jurisdiction and its group structure
before researching anything.

**The questions.** Diligence without a question list is a profile. Get the actual
questions - or propose them from the thesis and have the user cut them. The standard
commercial set:

| Area | What you are trying to establish |
|---|---|
| Business and model | What they actually sell, to whom, how they make money |
| Market position | Share of attention, customer references, competitive standing |
| Customers | Concentration, named references, churn signals, satisfaction |
| Technology and IP | What they own, what is licensed, what is differentiated |
| People | Founders, key personnel, leadership stability, departures |
| Ownership and funding | Cap table signals, investors, prior transactions |
| Financial signals | Disclosed figures, filings, growth indicators - with dates |
| Legal and regulatory | Litigation, regulatory action, licences, compliance record |
| Reputation | Adverse media, customer complaints, employee sentiment |
| Dependencies | Key suppliers, platform risk, single points of failure |

**The thesis.** Diligence tests a thesis. "We believe they have the strongest channel
position in the Nordics" produces sharp work; "tell me about them" produces a profile
nobody uses. Ask what the deal or decision assumes, then test those assumptions
specifically.

## Build

**1. Research agents across the question areas.** Use the question areas as a variable so
each area gets its own focused run, rather than one task trying to do everything:

```json
{
  "kbName": "<target> - diligence",
  "task": "Research <target legal entity>, <jurisdiction>, specifically regarding $area. Use only primary and reputable sources - company filings, regulator and court records, official registers, company announcements, and established trade or national press. For each finding give the date, the specific claim, and the source URL. Distinguish clearly between what the company states about itself and what independent sources confirm. Where you cannot establish a fact from available sources, write 'not established from public sources' and say what kind of source would be needed. Do not infer from adjacent companies or from the sector generally. Conduct research in the local language of the jurisdiction as well as English.",
  "variables": [{ "label": "area", "values": ["<the agreed question areas>"] }],
  "selectedSources": ["web", "news"]
}
```

Add a news KB for reputation and adverse-media coverage, with the target's languages set.
Add a file KB if the user has a data room extract, pitch deck or management presentation -
then the analysis becomes "what management says versus what independent sources show",
which is the most valuable read in the whole pack.

**2. Verify the entity.** Before analysing, `sample_kb` and confirm the documents are about
the right company. Name collisions are common and corrupt everything downstream.

**3. Evidence matrix.** Question areas as rows, evidence dimensions as columns - what the
company claims, what independent sources confirm, what remains unestablished.

```
instruction: "For $row, report what the attached sources establish about $column for the
target. Give specific, dated, sourced statements. Distinguish company self-description
from independent confirmation. Where the sources do not establish it, write 'not
established' and state what source type would be required. Never infer."
summarizeRows: true
```

**4. Report.** One section per question area, `mode: "Precise"`, `includeReferences: true`,
`inlineCitations: true`, plus a red-flag section and a "not established" section. See
`../dcipher-mechanics/references/reports.md`.

## Reading it like an analyst

- **Separate claimed from confirmed, everywhere.** This distinction is the pack's core
  value. A company's own site saying they serve 500 enterprise customers is a claim.
- **The unestablished list is a deliverable, not a failure.** It is the data-room request
  list. Present it as such and it becomes the most-used page in the pack.
- **Absence of adverse findings is not a clean bill.** Public sources under-report private
  disputes, settlements and quiet departures. State the difference explicitly.
- **Date everything.** A 2023 figure presented without its date reads as current and will
  be relied on.
- **Watch for thin coverage on a large company.** If a company of claimed scale has little
  independent footprint, that is itself a finding worth pulling on.
- **Check leadership continuity.** Quiet departures of named technical or commercial
  leaders are among the most predictive public signals available.

## Push back on

- Diligence with no question list and no thesis.
- Any request that this substitute for legal, financial or regulated background checks.
- Conclusions about private financials. Not available; say what is and is not disclosed.
- Treating absence of adverse media as verification.
- Ambiguity about which legal entity is in scope.

## Deliver

Lead with the thesis and whether the evidence supports it. Then red flags, each with the
evidence and its strength. Then the question areas answered, claimed-versus-confirmed
throughout. Then the unestablished list as a data-room request. Close with the scope
boundary restated in one line and the date the research was run.
