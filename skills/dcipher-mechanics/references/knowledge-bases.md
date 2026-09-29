# Building knowledge bases

A knowledge base (KB) is a structured, semantically indexed text dataset. Three
constructors, three completely different input shapes. All three are asynchronous: they
return a `flowId`, and the KB materialises later.

## Choosing a constructor

| Source | Tool | Use for | Hard limits |
|---|---|---|---|
| News (200k+ global sources, Opoint) | `create_news_kb` | horizon scanning, narrative monitoring, industry news | last **365 days** only |
| Social (Netfeedr) | `create_social_kb` | voice of customer, sentiment, community discussion | roughly last **30 days** |
| Research agents (web/news via search) | `create_research_agent_kb` | desk research, entity lists, filings, government and academic sources | none, but slow at scale |

Several sources can be built as separate KBs and attached to one project. Do not try to
merge different source types into a single KB.

## No file upload

**These tools cannot ingest the user's own documents.** There is no constructor for
uploaded PDFs, Word files or spreadsheets, so support-ticket exports, survey response
files, interview transcripts, data-room extracts and internal reports cannot be analysed
through this interface.

Establish this **before** framing a piece of work around a customer's own material, not
after. A user who says "analyse these 400 support tickets" needs to hear that this cannot
be done here, in the first exchange.

What can still be done when the material is the user's own:

- **Find the public counterpart.** Product reviews, community and forum discussion, and
  media coverage reach much of what an internal corpus would show, from the outside.
- **Research the question instead of the documents.** A research-agent task can often
  answer what the user wanted the documents to answer.
- **Point them at the Studio UI.** File-based datasets may be available through the
  product directly even when these tools cannot create them - say so rather than
  pretending the capability does not exist anywhere.

Report `sheets` and `sheetInputs` reference uploaded spreadsheet files, so they are
unavailable for the same reason. Build report sections from knowledge-base `inputs`.

## Getting the search terms right

For news and social, **do not invent keywords**. Call
`get_search_queries_from_agent(interest_area, sources)` - it tests candidate queries
against the live APIs rather than guessing.

- News: `sources` must be exactly `["opoint-news"]`.
- Social: one or more of `"facebook"`, `"twitter"`, `"instagram"`, `"youtube"`. Nothing else.

Skip this only when the user has supplied concrete keywords themselves.

## `create_news_kb`

```
kbName    required  string
params    required  { any: string[]           # keywords, OR-combined - required
                    , language: string[]      # ISO codes
                    , location: string[]      # location codes
                    , timestamp: { since, until } }
limit     required  integer 100..60000        # articles per run
schedule  optional  see below
insightBoosterId  optional  24-hex project id - auto-links the KB on completion
summarization     optional  { areaOfInterest, language }
```

**Omit `params.timestamp` for any relative window** ("last month", "last quarter"). The
server resolves it against the current date. Only pass `since`/`until` for an absolute
range the user actually specified, as `YYYY-MM-DD` - no milliseconds, no epoch numbers.

**Language and location matter more than they look.** A claim about a non-English market
built on an English-only corpus is a defect, not a limitation. Either set
`params.language` to the market's languages, or say plainly in the answer that the view is
English-language media only.

Pass `insightBoosterId` when you already have the project - it saves an attach step and
keeps scheduled runs linked automatically.

## `create_social_kb`

Same `params` shape as news, plus:

```
sources  required  [ { platform: facebook|twitter|instagram|youtube, limit: int } ]
```

The combined limit across platforms must be 100..60000. Platform choice is an analytical
decision: YouTube comments and Instagram behave nothing like X/Twitter. For B2B topics,
social adds little - say so rather than building a thin KB.

## `create_research_agent_kb`

The distinctive Dcipher capability: run one research task in parallel across a list.

```
kbName    required  string
task      required  string with $-prefixed placeholders
variables optional  [ { label: "company", values: ["Siemens", "ABB", ...] } ]
multivariablePolicy  "combinations" (default) | "aligned"
selectedSources      ["web"] (default) | ["news"] | ["web","news"]
schedule / insightBoosterId / summarization as above
```

- Placeholder `$company` in the task maps to `variables[].label = "company"`.
- Each variable also becomes a KB column, so labels must not collide with the reserved
  output columns: `id`, `Text`, `Source`, `URL`, `Date`.
- `combinations` runs the cross-product (10 companies x 5 triggers = 50 runs). `aligned`
  pairs values position-by-position and requires equal-length lists.
- **Watch the run count.** The cross-product grows fast. Before firing 200 runs, tell the
  user the size and confirm.

Task-writing rules, in rough order of impact:

1. State the output shape you want in the task text. "Return the three most significant
   items with dates and sources" beats "research X".
2. Give a recency bound explicitly - the agent has no default sense of what is current.
3. Ask for local-language research when the entity is non-English:
   "Conduct the research in the local language of $country."
4. Say what to do when there is nothing to report. Otherwise the agent pads.

Worked example:

```json
{
  "kbName": "Nordic grid operators - investment activity",
  "task": "Research investments, capacity projects and procurement announcements made by $company during $period. Conduct the research in the local language where applicable and prioritise company filings, regulator publications and reputable trade press over secondary coverage. For each item give the date, the amount if disclosed, and the source URL. If nothing significant occurred, say so explicitly rather than reporting minor news.",
  "variables": [
    { "label": "company", "values": ["Svenska kraftnat", "Statnett", "Energinet", "Fingrid"] },
    { "label": "period", "values": ["the last 6 months", "the 6 months before that"] }
  ],
  "multivariablePolicy": "combinations",
  "selectedSources": ["web", "news"]
}
```

## Scheduling

Omit `schedule` for a one-time fetch. Pass it to stand up a self-refreshing flow: the
first run fires almost immediately, and **each subsequent run creates a new
timestamp-suffixed KB** rather than appending to the existing one.

```
schedule: { unit: second|minute|hour|day|week|month|quarter
          , interval: int                   # only for second/minute/hour
          , dataPeriod: { amount, unit }    # rolling fetch window, defaults to one cadence step
          , timezone: "Europe/Stockholm"    # IANA, defaults to UTC
          , limit: int }                    # optional cap on total runs
```

`dataPeriod` is independent of the cadence: weekly cadence with a one-month `dataPeriod`
means a weekly run that each time pulls the last month. Use overlap deliberately for
recall, but tell the user their document counts will overlap between runs.

Scheduling is the right default for monitoring deliverables (competitor tracking,
narrative monitoring, regulatory watch) and wrong for one-off questions.

## Polling

```
get_last_flow_run_status(flowId)  ->  { status, knowledgeBaseId?, message? }
```

`message` carries the failure reason when a run failed, and is absent otherwise. For a
scheduled flow this reports the most recent run, and the `knowledgeBaseId` returned is the
first run's KB.

Once complete, `sample_kb(kb_id)` before doing anything else. A KB full of boilerplate,
paywall stubs or off-topic documents will produce a confident and wrong analysis
downstream. Two minutes of sampling saves the deliverable.

Useful follow-ups: `kb_search_documents` to probe a single KB,
`get_kb_field_statistics(kb_id, field_name)` for the distribution of a metadata field,
`check_kb_has_timestamp(kb_ids)` before promising anything time-based.
