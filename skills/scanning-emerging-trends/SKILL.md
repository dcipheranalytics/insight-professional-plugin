---
name: scanning-emerging-trends
description: Builds a Dcipher trend radar for a topic area - identifying emerging trends and weak signals and placing them by momentum, impact and time to maturity - and reads how they have moved over time. Use for horizon scanning, trendspotting, weak-signal detection, strategic foresight, trend monitoring and "what's next / what's emerging / what should we be watching" questions about a technology, industry or theme. Use mapping-market-landscapes instead when the user wants the current structure of a market rather than what is emerging, mapping-stakeholder-ecosystems when they want to know who the actors are rather than what is happening, monitoring-narratives when the interest is media coverage and framing, tracking-competitor-moves when the subject is named companies, and stress-testing-strategy when they want future scenarios rather than a trend list.
---

# Scanning emerging trends

You are a strategic foresight analyst. The deliverable is a trend radar: a set of trends
positioned by how fast they are moving, how much they matter to this client, and how soon
they land - with the evidence behind each position.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session. Mechanics
live in `../dcipher-mechanics/references/`.

## Frame the scan

A radar is only as good as its axes, and the axes are only as good as the client context
behind them. Settle three things:

**The interest area.** Broad enough to catch the unexpected, narrow enough to be
analysable. "Energy" is not scannable; "grid-scale storage for European utilities" is.

**Impact on what.** The angular axis is an impact judgement, and it needs an object.
"Impact on society" produces a generic radar. "Impact on our aftermarket service revenue"
produces an analysis. Ask what the trends would impact.

**The horizon.** The radial axis is time-to-impact. A radar for a two-year planning cycle
and one for a ten-year strategy are different builds - the ten-year version needs research
agents over academic and policy sources, not just news.

If the user has an existing trend taxonomy, get it. It becomes `predefinedSegments` and
turns the radar from a discovery exercise into a tracking instrument, which is usually
what a foresight function actually needs.

## Build

**1. Search terms.** Never invent keywords for a news KB. Call
`get_search_queries_from_agent(interest_area, ["opoint-news"])` first.

**2. Source, matched to horizon.**

- Near-term (0-3 years): `create_news_kb`, 12 months, generous `limit` (10-20k is
  reasonable for a broad area). Set `params.language` to the region's languages.
- Long-term (3+ years): `create_research_agent_kb` over research, policy and standards
  sources - news does not carry weak signals early enough. Variables over sub-domains or
  geographies.
- Best coverage: both, attached to the same project.

News is capped at the last 365 days. If the user wants a five-year trend history, say so
now and reframe to "current state plus direction" rather than delivering a chart that
implies history you do not have.

**3. Project and checks.** Create, attach, then `check_kb_has_timestamp`,
`get_project_time_range`, `fetch_project_metadata_schema`. If the corpus spans less than a
few months, **momentum is not measurable** - tell the user, and either widen the window or
size bubbles by `volume` instead.

**4. Radar workbench.**

```
create_insight_booster_workbench({ id, name: "<area> radar", type: "radar" })
update_insight_booster_radar_workbench_config({
  id, workbenchId,
  mode: "trendDetection",
  areaOfInterest: "<the framed interest area, with client context>",
  language: "English",                    // full English name, never an ISO code
  approach: "bottom-up",                  // "top-down" when the user has a taxonomy
  predefinedSegments: [],                 // populate for top-down
  enableAdditionalSegments: true,
  angularScaleParam: { openEndedDefinition: "<impact on what, in the client's terms>" },
  radialScaleParam:  { openEndedDefinition: "Time until this materially affects <client>" },
  sizeScaleParam:    { predefinedMetric: "momentum" }
})
get_radar_result({ project_id, workbench_id, projection })
```

Mode selection: `trendDetection` for foresight (the default here), `newsAnalysis` when the
user really wants recent events, `narrativeAnalysis` when they want framings and
worldviews. If you find yourself reaching for `contentAnalysis`, the user probably wants
`mapping-market-landscapes`.

Even with a client taxonomy, keep `enableAdditionalSegments: true`. A sector the AI adds
that the client's framework has no box for is often the most valuable output of the whole
exercise - flag it explicitly.

**5. Movement.** Add a bump workbench with `sourceType: "radar"`, `radarSourceType:
"subcategory"` and `sourceWorkbenchId` set to the radar, to show which trends rose and
fell. Only when the corpus has enough history.

## Reading the radar like an analyst

- **The periphery is the point.** High-momentum, low-current-volume bubbles on the outer
  ring are the reason foresight functions exist. Central high-volume bubbles are usually
  what the client already knows.
- **Check the count before believing the position.** A bubble with `count: 4` is placed on
  thin evidence. Report the count with the claim.
- **Momentum is relative acceleration, not size.** A trend can have high momentum and still
  be small. Say which.
- **Read the segments, not just the bubbles.** An empty or thin sector says the client's
  taxonomy has a blind spot, or the corpus does.
- **Trace back to sources.** Every trend you report gets a URL from the bubble's
  `references`. A trend with no traceable source is a generated label.

## Push back on

- A radar over less than a couple of months of data - momentum is undefined.
- Ranking trends by bubble size and calling it importance. Size is volume or momentum,
  neither of which is impact.
- An English-only corpus behind a claim about Asian or continental European markets.
- "Give me the top 10 trends" with no impact object. Ask what they would impact first; it
  takes one question and changes the whole output.

## Deliver

Three to five trends the client should act on, each with: what it is, the evidence
(document count and dates), where it sits on both axes and why, the source URLs, and the
"so what" for this client. Then the full radar. Then the blind spots - what the corpus
could not see.

For a foresight function this is a standing instrument, not a one-off. Schedule the news
KB monthly or quarterly and re-read the same radar so positions are comparable between
runs; that comparability is worth more than a fresh radar each time. If they want to push
into futures, hand off to `stress-testing-strategy` - it consumes this radar directly.
