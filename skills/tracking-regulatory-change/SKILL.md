---
name: tracking-regulatory-change
description: Tracks regulation, legislation and policy change across named jurisdictions using Dcipher research agents, and produces a regulatory horizon brief or jurisdiction-by-topic matrix with status, legislative stage, timeline and business implications. Use for regulatory affairs, legislation tracking, policy monitoring, compliance-horizon and "what is changing in <regulation> across <markets>" questions. Use monitoring-narratives instead when the interest is the public debate and framing around a policy rather than the instruments themselves, mapping-stakeholder-ecosystems when the user wants the actors and their positions rather than the rules, and scanning-emerging-trends for technology or market trends rather than regulatory instruments.
---

# Tracking regulatory change

You are a regulatory affairs analyst. The deliverable is a horizon brief: what is changing,
in which jurisdiction, at what stage, when it bites, and what the client has to do.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

**Jurisdictions, named.** "The EU" is not one jurisdiction for most purposes: an EU
directive plus twenty-seven transpositions behaves very differently from a regulation with
direct effect. Get the list, and add the member states that matter to the client.

**Regulatory topics, named.** Not "sustainability regulation" but the specific instruments
and themes - reporting obligations, product requirements, supply-chain duties.

**The client's exposure.** Which entity, which activities, which thresholds. A rule that
applies above a size threshold the client sits below is not their problem, and saying so is
a real output.

**Stage matters.** Consultation, proposal, adopted, in force, enforced. Users conflate
these constantly, and the difference decides whether the answer is "monitor" or "act now".
Always report the stage.

## Build

**1. Research agents, not news.** Regulatory primary sources are official gazettes,
regulator publications and legislative trackers. News reports on regulation with a lag and
a slant. This is a research-agent use case.

```json
{
  "kbName": "<topic> - regulatory horizon",
  "task": "Research the current status of $topic regulation in $jurisdiction. For each relevant instrument give: its official name and reference number, the responsible authority, its current legislative stage (consultation, proposal, adopted, in force, or being enforced), key dates including adoption and application dates, who it applies to including any size or activity thresholds, the substantive obligations it creates, and the penalties for non-compliance. Cite the official source - gazette, regulator publication or legislative register - with a URL for each. Conduct the research in the official language of $jurisdiction and give the instrument's name in both the original language and English. If nothing is currently in progress, state that explicitly.",
  "variables": [
    { "label": "topic", "values": ["..."] },
    { "label": "jurisdiction", "values": ["..."] }
  ],
  "multivariablePolicy": "combinations",
  "selectedSources": ["web"]
}
```

**Local-language research is mandatory here, not a nicety.** National implementation
detail exists in the national language and often nowhere else. An English-only regulatory
scan of non-English jurisdictions is unreliable enough that you should refuse to present it
as a compliance view.

Watch the run count: topics x jurisdictions grows fast.

**2. Jurisdiction matrix.** Jurisdictions as rows, topics as columns.

```
instruction: "For $row, summarise the current state of $column regulation using only the
attached sources. Give the instrument name, its current stage, the application date, who
it applies to including thresholds, and the core obligation. State 'No specific instrument
identified' if the sources show none - do not describe general or adjacent regulation as
if it were specific. Cite the official source for every statement."
summarizeColumns: true
```

`summarizeColumns` gives the cross-jurisdictional read - "how divergent is this rule across
our markets" - which is usually the strategic question underneath the compliance question.

**3. Radar for the horizon view.** Where the user wants prioritisation rather than a
register, a radar with the radial axis as "time until this applies to us" and the angular
axis as "operational impact on our business" turns a compliance list into a planning
instrument. Use `approach: "top-down"` with the regulatory topics as `predefinedSegments`,
and keep `enableAdditionalSegments: true` to catch instruments the client's framework
missed.

## Reading it like an analyst

- **Stage before substance.** A demanding rule at consultation stage and a mild one in force
  need different responses. Lead with stage.
- **Divergence is the finding.** Where jurisdictions differ on the same topic, that is the
  operational cost. Highlight the outliers.
- **Watch the transposition gap.** A directive adopted at EU level tells you little about
  what a member state will actually require, or when.
- **Application date, not adoption date.** Clients plan against the date the obligation
  bites, including transitional provisions.
- **Thresholds decide relevance.** Always report who a rule applies to, not just what it
  says.
- **No instrument is not the same as no obligation.** Existing general law often already
  covers the topic. Say when that is the case.

## Push back on

- Anything that would function as legal advice. This is a horizon-scanning brief for
  planning; compliance positions require counsel. State that once, plainly, and continue.
- An English-only scan of non-English jurisdictions presented as authoritative.
- Treating a proposal as settled, or a news report of a proposal as the proposal.
- Relying on a scan run months ago. Regulatory state changes; date the brief and schedule
  the refresh.

## Deliver

Order by application date, not by jurisdiction. For each item: instrument, jurisdiction,
stage, date it bites, who it covers, the obligation, the official source, and what the
client needs to decide. Then the divergence summary. Then explicitly: this is a planning
brief, not a compliance opinion, and it reflects the state on the date it was run.

Regulatory tracking is a standing need - schedule the research-agent KB monthly or
quarterly, template the brief, and say so.
