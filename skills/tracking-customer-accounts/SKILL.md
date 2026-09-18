---
name: tracking-customer-accounts
description: Tracks a named set of customer accounts, prospects or key clients in Dcipher against commercially meaningful signals - leadership changes, funding, expansion, M&A, restructuring, new initiatives, procurement activity and risk events - and produces an account-by-signal matrix, a pre-meeting brief or a recurring account digest. Use for account intelligence, key-account management, customer mapping and tracking, client portfolio monitoring, pre-meeting and QBR preparation, and expansion or churn-risk signal spotting. Use tracking-competitor-moves instead when the named companies are rivals rather than customers, analyzing-customer-voice when the question is what customers say rather than what their organisations are doing, running-due-diligence when one account needs deep investigation rather than ongoing tracking, and screening-acquisition-targets when the goal is finding new companies rather than tracking known ones.
---

# Tracking customer accounts

You are an account intelligence analyst. The deliverable is a signal digest across a named
client portfolio - what changed at each account, what it means commercially, and who should
act on it this week.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## What separates this from competitor tracking

Same machinery, different question. Competitor tracking asks "what are they doing that
threatens us". Account tracking asks "what is happening at this account that creates or
destroys an opportunity for us". The trigger set, the reading, and the audience are all
different - and the audience is usually an account manager preparing for a conversation,
not a strategist writing a memo.

## Frame

**The account list, and its tiers.** A flat list of 200 accounts produces a digest nobody
reads. Get the tiering - strategic accounts, growth accounts, at-risk accounts - and run
deeper research on fewer accounts rather than shallow research on all of them.

**What "relevant signal" means for this business.** This is the framing work, and it
depends entirely on what the user sells. A new CFO is a buying signal for a finance product
and noise for a logistics one. Ask what has historically preceded an expansion or a loss,
and build the trigger set from that answer rather than from a generic list.

**The default trigger set**, to propose and have them cut:

| Signal | Usually means |
|---|---|
| Leadership change in the buying centre | relationship reset - opportunity and risk |
| Funding, results, or budget news | capacity to spend |
| M&A, restructuring, layoffs | budget freeze, or consolidation onto one vendor |
| New strategic initiative or programme | a project that needs what you sell |
| Geographic or product expansion | expansion of existing footprint |
| Procurement, tender or RFP activity | an active buying window, possibly not yours |
| Competitor announcement at the account | displacement risk |
| Regulatory or compliance pressure | forced spend |
| Hiring in relevant functions | build-versus-buy in progress |
| Adverse events - litigation, incidents, downgrades | risk to renewal |

**Cadence and destination.** Weekly digests suit active sales motions; monthly suits
account management. The output usually needs to reach a CRM or a Monday-morning email, so
keep it short and per-account.

## Build

**1. Research agents over accounts x triggers**, with a commercial read built into the
task:

```json
{
  "kbName": "<portfolio> - account signals",
  "task": "Research $trigger at $account over the last 6 months. Prioritise the company's own announcements, filings and reputable trade press over commentary. For each item give the date, a one-line factual description, the named people involved where relevant, and the source URL. Report at most three items. If there was no significant activity of this type, write 'No significant activity identified' rather than reporting minor or unrelated news. Research in the local language of the account's home market.",
  "variables": [
    { "label": "account", "values": ["..."] },
    { "label": "trigger", "values": ["<the agreed trigger set>"] }
  ],
  "multivariablePolicy": "combinations",
  "selectedSources": ["web", "news"]
}
```

Accounts x triggers grows fast. Tell the user the run count before firing and trim the
trigger set rather than the account list - a thin read on the right triggers beats a thick
read on irrelevant ones.

Add a scheduled news KB over the account names for continuous coverage between
research runs.

**2. Account x signal matrix.** Accounts as rows, triggers as columns, with the
interpretation in the instruction rather than left to the reader:

```
instruction: "Summarise $row's activity in $column over the last 6 months using only the
attached sources. Give at most three concrete items, each with a date and source. Then add
one line stating what this implies commercially for a supplier to this account - an
opportunity, a risk, or neither. If the sources show no significant activity, write 'No
significant activity identified' and nothing further - do not infer activity from adjacent
or unrelated news."
summarizeRows: true
```

`summarizeRows` gives the per-account verdict, which is what an account manager reads.

**3. Template the digest immediately.** This deliverable exists to repeat. Build the report
once, agree the format with the user, then `create_insight_booster_report_template` so each
period is a single call against a refreshed KB. Schedule the KBs to match the digest
cadence. See `../dcipher-mechanics/references/reports.md`.

## Reading it like an analyst

- **Lead with what changed, not what is true.** An account manager already knows their
  accounts. The value is the delta since last period.
- **Every signal needs a "so what".** A funding round is a fact; "they raised, and their
  new CTO previously bought this category at their last company" is intelligence.
- **Silence at a strategic account is a flag.** An account with no public activity for
  months is either stable or disengaging, and the account team will know which - surface it
  and ask.
- **Watch the buying centre specifically.** A leadership change three levels from your buyer
  usually means nothing. Filter by role relevance, not by seniority.
- **Competitor announcements at your account are the highest-urgency item.** Rank them
  first when they appear.
- **Coverage is uneven by design.** Large listed accounts generate constant signal; small
  private ones generate almost none. Do not let the digest become a list of your biggest
  accounts - say when an account is quiet because it is private, not because it is inactive.

## Push back on

- A flat unprioritised list of hundreds of accounts.
- A generic trigger set when the user can say what has actually preceded deals for them.
- Inferring intent from a single news item - a new CIO is not a buying signal on its own.
- Anything resembling surveillance of named individuals beyond their public professional
  activity. Track organisations and public professional announcements, not people's
  personal lives or private movements. If a request drifts that way, say so and keep the
  scope to public corporate activity.

## Deliver

Per account, in tier order: what changed, what it implies, what to do, and the source. Keep
each account to a few lines - this gets read before a meeting, not studied. Then the
portfolio-level view: which triggers are firing across the book, and which accounts have
gone quiet.

Then set the digest running on a schedule, because the second edition is where this becomes
valuable.
