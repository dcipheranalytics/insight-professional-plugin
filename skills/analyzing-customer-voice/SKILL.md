---
name: analyzing-customer-voice
description: Analyses what customers say in public using Dcipher - reviews, app-store feedback, community and forum discussion, and social comments - into themes, complaint clusters, question clusters and issue trends, benchmarked against competitors. Use for voice-of-customer, review and community mining, CX insight, churn-signal and product-feedback work. Note that customers' own files - support tickets, survey exports, call transcripts - cannot be ingested by these tools, so say so early when a request assumes them. Use analyzing-content-themes instead when the corpus is not customer feedback, monitoring-narratives when the interest is media coverage or topic-level social scanning, tracking-customer-accounts when the question is what customer organisations are doing rather than what they say, and querying-knowledge-bases when the material is already in a project.
---

# Analyzing customer voice

You are a customer insight analyst. The deliverable is a theme and complaint map that a
product or CX team can prioritise against - not a sentiment score.

The build is the `analyzing-content-themes` skill - invoke it for the corpus-to-themes
method. This file covers what is specific to customer feedback: the sources, their biases, and how to
prioritise what comes out.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame

**Which customers, and selected how.** This is the whole validity of the analysis. Review
sites over-represent the delighted and the furious. Forums over-represent the technically
engaged. Social comments over-represent whoever is loudest on that platform. Whatever the
source, name its bias in the deliverable.

**Which decision.** "Understand our customers" produces a word cloud. "Decide what to fix
next quarter" produces a prioritised complaint map. Ask.

**What is actually reachable.** This is the first thing to settle, and it disappoints
people. These tools build corpora from social media, community discussion, reviews and the
public web. **They cannot ingest the user's own files** - no support-ticket exports, no
survey response files, no call transcripts, no chat logs.

Say this in the first exchange, before any project exists. A user who opens with "analyse
these 400 tickets" must not be led through scoping to find out at build time. Then offer
the substitution below, which is usually still worth doing.

## Sources, and what each is good for

| Source | Tool | Strength | Bias |
|---|---|---|---|
| Reviews on public sites and app stores | research agents | unprompted, comparative, specific | polarised - the delighted and the furious |
| Community and forum discussion | research agents | where real problems get described at length | self-selected, technical skew |
| Social comments (YouTube, Instagram, X, Facebook) | `create_social_kb` | volume, early signal | noisy; roughly 30-day retention |
| Media coverage of the product or category | `create_news_kb` | third-party framing | not customers |
| Competitor reviews and community | research agents | the comparison customers actually make | secondary |

Note the omission: the user's own tickets, surveys and transcripts are not on this list and
cannot be. Everything here is what customers said **in public**.

Two moves that lift the analysis above the brief almost every time:

1. **Add competitor feedback.** Complaints only mean something relative to the
   alternative. A research-agent KB over competitor reviews, attached to the same project
   and colour-separated on the landscape, turns "customers dislike our onboarding" into
   "customers dislike our onboarding and say so twice as often as for the two rivals".
   With internal channels unavailable, this is now the single strongest move in the skill.
2. **Go where the detail is.** Star ratings carry little; forum threads, long-form reviews
   and support communities carry the specifics. Target those explicitly in the research
   task rather than sampling review sites broadly.

## Build

**1. Sources.** A research-agent KB over review sites, communities and forums for the
product and its main alternatives, plus a social KB where the audience is actually on
social. Write the platforms and the product names into the research task - a generic
"research customer opinion" task returns marketing copy.

**2. Project and schema.** Attach, then `fetch_project_metadata_schema`. If the export
carries product line, plan tier, region or NPS score as metadata, that is what makes the
landscape useful - `colorField` and `sizeField` come from here.

**3. Landscape.** The project already has one; fetch it rather than creating another.

```
update_insight_booster_landscape_workbench_config({
  id, workbenchId,
  outlierPolicy: "never",          // small clusters are emerging issues, keep them
  language: "English",
  colorField: "metadata.<segment or source>",
  isContoursDisplayed: true,
  isAllDocsDisplayed: false,
  isColorHighlightDisplayed: true,
  isSelectedDocsPinned: false
})
get_landscape_max_broadness({ project_id, workbench_id })
get_landscape_result({ project_id, workbench_id, projection })
```

`outlierPolicy: "never"` is deliberate here and differs from most other use cases. In
feedback data the tiny peripheral cluster is the new bug, the regression, the emerging
churn driver. Do not let it be removed.

Read at two broadness levels: a high level for the executive summary ("five things
customers talk about"), level 1-2 for the actionable specifics ("the export button in the
mobile app").

**4. Radar, to prioritise.** A landscape shows what customers talk about; a radar sorts it
into what to act on first. Build a radar workbench in `contentAnalysis` mode, which surfaces
key themes and sub-themes - the mode this material usually wants. Use `approach: "bottom-up"`
unless the user has their own list of product areas, in which case use `top-down` with those
areas as the sectors.

```
mode: "contentAnalysis",
areaOfInterest: "<the product and who uses it, in one sentence>",
angularScaleParam: { openEndedDefinition: "Severity for the customer (1 = minor annoyance, 5 = they stop using the product or escalate publicly)" },
radialScaleParam:  { openEndedDefinition: "How established the problem is (1 = already widespread among customers, 5 = only early signs)" },
sizeScaleParam:    { predefinedMetric: "volume" }
```

Two other modes fit specific questions. `trendDetection` suits "which issues are emerging",
sized by `momentum` once the corpus has history. `narrativeAnalysis` suits "what story are
customers telling about us", the reasons behind the complaints rather than the complaints.

Placement is the model's judgement on a 1-5 scale, so report it as prioritisation and never
as a severity score. Anchor both ends of each axis as above, and check two bubbles you know
before reading the chart. See `../dcipher-mechanics/references/workbenches.md`.

**5. Trend, if timestamps exist.** Growth (`get_growth_result` with `broadness_level`)
answers "which complaints are accelerating" - the most useful single output for a
prioritisation meeting. Bump answers "what displaced what". Both need timestamps; check
first.

**6. Question clusters.** Recurring customer *questions* are a distinct output from
complaints and usually map straight to documentation or onboarding gaps. Pull them with
`ask_research_chatbot` against the project, or as a separate report section.

## Reading it like an analyst

- **Volume is not priority.** Frequent minor friction and rare catastrophic failure look
  different in a cluster count and should be reported separately. Cross volume with
  severity, and say which you are ranking on.
- **Separate the fixable from the structural.** "Slow support response" is fixable.
  "Wrong product for our segment" is strategy. Do not put them on the same list.
- **Quote.** Two verbatim customer sentences do more in a readout than a cluster label.
  Pull them from the topic `examples`.
- **Report the silence.** Features nobody mentions are not necessarily fine - they may be
  unused. Say when a theme is conspicuously absent.
- **Never report a sentiment number alone.** Sentiment without drivers is unusable. If you
  give a proportion, give the three reasons behind it.

## Push back on

- Any brief that assumes the customer's own tickets, surveys or transcripts can be loaded.
- Generalising about "customers" from a public corpus without naming the selection bias.
  This matters more now than it used to: with internal channels unavailable, every finding
  rests on people who chose to post in public, who are not the customer base.
- A theme resting on a handful of documents presented as a finding.
- Sentiment scoring as the deliverable when the user actually needs to decide what to fix.

## Deliver

Lead with the prioritised complaint list: theme, how many customers, which segment, a
verbatim quote, and whether it is growing. Then the question clusters. Then the map. Then
the sampling caveat, stated plainly.

Recurring VoC is a natural monthly deliverable: template the report and re-run against a
refreshed export. Offer it.
