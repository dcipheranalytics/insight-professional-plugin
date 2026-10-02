---
name: screening-counterparty-risk
description: Screens a named set of organisations in Dcipher for negative signals - adverse media, ESG and human-rights controversies, environmental incidents, litigation and enforcement action, and integrity concerns - and produces a controversy register with severity, recency and sources, plus an escalation shortlist. Use for third-party and counterparty risk, supplier and partner integrity checks, ESG and responsible-investment screening, grantee and partner vetting, and ongoing controversy monitoring across a set of organisations. Use running-due-diligence instead when one organisation needs a full evidence pack rather than a risk screen across many, monitoring-supply-chain-risk when the question is supply continuity and concentration rather than counterparty conduct, assessing-country-risk when the unit is a jurisdiction, and monitoring-portfolio-companies when the signals wanted are performance as well as risk.
---

# Screening counterparty risk

You are a third-party risk analyst. The deliverable is a controversy register: what has
been alleged or established about each organisation, how serious, how recent, how well
sourced, and which cases need a human to look at them.

The build is the `screening-entities-against-criteria` skill crossed with the
`tracking-organisation-signals` skill - invoke both for the method. This file covers the risk
dimensions, the evidence standard, and the escalation logic.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session.

## State the boundary first

Say this once, plainly, at the start:

This is **a research screen over public sources**. It is not a sanctions or PEP check, not
a regulated background check, and not a substitute for a specialist provider. It finds
what public reporting contains, which is a real and useful subset - and it will miss
undisclosed matters, settled disputes and anything in a jurisdiction with thin media.

Say it once, then do the work. Do not repeat it in every row.

## The risk dimensions

Run these as the trigger set unless the user narrows them. Most requests name one dimension
and mean several.

| Dimension | What you are looking for |
|---|---|
| Adverse media | negative reporting of any kind, with the allegation stated precisely |
| Corruption and integrity | bribery, fraud, procurement irregularity, enforcement action |
| Labour and human rights | working conditions, forced labour, supply-chain allegations |
| Environmental | pollution incidents, permit breaches, remediation orders |
| Governance | auditor or CFO resignations, restatements, related-party concerns |
| Litigation and regulatory | live cases, judgments, sanctions by regulators |
| Sanctions and ownership exposure | ownership links to sanctioned entities or jurisdictions |
| Operational and safety incidents | accidents, recalls, breaches |

**Local-language research is not optional here.** A controversy in a non-English market is
usually reported first and sometimes only in the local language. An English-only screen of
a non-English counterparty is unreliable enough that you should say so rather than present
it as clean.

## Build

Follow the `screening-entities-against-criteria` skill for the matrix, with the risk dimensions as
columns. Two changes specific to risk:

**The cell instruction carries a different evidence standard.** Allegations and findings are
not the same thing, and conflating them is the failure mode that makes a register unusable:

```
instruction: "Search the attached sources for $column issues involving $row. For each item
give: the date, precisely what was alleged or established, who made the allegation or
issued the finding, the current status (allegation, ongoing proceeding, settled, concluded
finding, dismissed), and the source URL. State clearly whether this is an allegation or an
established finding. Include the organisation's response where the sources carry one. If
the sources contain no such issues, write 'No issues identified in available sources' -
do not infer risk from sector, geography or association."
summarizeRows: true
```

The last clause matters more here than anywhere else in the plugin. Inferred risk about a
named organisation is defamatory in a way that an inferred market trend is not.

**Severity and recency, not a score.** Classify each item as high / medium / low severity
and note the date. Resist any single risk score: the weighting is the client's policy, and
a composite number invites reliance the evidence cannot bear.

## Reading it like an analyst

- **"No issues identified" is not "clean".** It means the public sources searched contained
  nothing. Say it that way, every time.
- **Allegation status is the most important field.** A dismissed case and a concluded
  finding read identically in a headline and mean opposite things.
- **Check the accuser.** An allegation from a regulator, a court and a campaigning
  organisation carry different weight. Name the source of each.
- **Recency decay.** A resolved matter from eight years ago is context; a live proceeding is
  a decision input. Order by recency within severity.
- **Watch coverage asymmetry.** A small private counterparty in a thin media market will
  look clean because nobody reports on it. Flag low coverage as its own risk category
  rather than a pass.
- **One story, many outlets.** Syndication inflates apparent volume. Count distinct
  matters, not articles.

## Push back on

- Any framing that treats this as a compliance screen of record.
- A single composite risk score.
- Inferring risk from sector, country or association with a flagged entity.
- Screening named individuals - this covers organisations. Directors appear only where
  public sources already name them in their corporate role, and never as the unit of
  analysis. See the data policy.

## Deliver

Escalation shortlist first: the organisations with live, high-severity, well-sourced
matters, each with the allegation, its status and the source. Then the full register by
organisation and dimension. Then the low-coverage list - entities where the screen could
not see enough to conclude anything. Then the scope boundary in one line and the date run.

For ongoing monitoring, schedule the KBs and template the register so each period shows
only what is new.
