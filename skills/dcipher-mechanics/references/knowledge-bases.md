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

`create_file_kb` and `get_file_upload_url` are not on the server. `list_files` and
`get_file_by_name` still are, but nothing uploads a file or builds a KB from one, so they
lead nowhere in these workflows.

Report `sheets` and `sheetInputs` reference spreadsheet files by id, and there is no way to
upload one here, so treat them as unavailable. Whether a file already in the organisation
could be attached is untested. Build report sections from knowledge-base `inputs`.

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

**Beware false friends.** A keyword can mean different things across languages (Swedish
"gift" is poison or married). Set `params.language` before trusting keyword hits in a
multilingual set, and prefer phrases to single words.

**`params.location` is a filter, not a tag.** On social, posts without country metadata are
dropped when a country is set, so the corpus shrinks silently. Say so when a country-scoped
social KB is thinner than expected.

**A `limit` below the number of matches returns a slice, not a random sample.** Posts come
back most recent first by default, so a low limit on a busy topic covers only the latest
days. Raise the limit or narrow the window rather than reading a thin slice as the whole.

There is no exclusion field (`params` takes `any` only). Handle noise afterwards with a
metadata or semantic filter, or by deleting off-topic clusters, and say that you did.

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
- Each variable also becomes a **categorical metadata field** on the resulting documents.
  This is verified on a live project: a task with `$risk_area` and `$provider` produced
  fields `risk_area` and `provider`. It is what lets a landscape be coloured by, or a bump
  chart ranked over, the entity you researched. Labels must not collide with the reserved
  output columns: `id`, `Text`, `Source`, `URL`, `Date`.
- The schema lists these fields by their bare label, but `get_kb_field_statistics` wants
  `metadata.<label>` and rejects the bare form (HTTP 400, "Unexpected field name"). Use
  `metadata.<label>` when a tool asks for a field name, and confirm the form that
  `colorField`, `sizeField` and `metadataField` want with a first call before relying on it.
- `combinations` runs the cross-product (10 companies x 5 triggers = 50 runs). `aligned`
  pairs values position-by-position and requires equal-length lists. It is also the way to
  attach an attribute to each entity: two aligned variables, such as `actor` and
  `actor_type`, give every document both fields, so a landscape can be coloured by type.
  Reference both placeholders in the task.
- **Rows the agent finds nothing for are dropped by default** (`excludeEmptyResults` is on).
  When the deliverable needs to show which entities came back empty - screening, entity
  lists, due diligence - set it to `false`, or an entity with no evidence is
  indistinguishable from one you never asked about.
- **Mostly empty run?** Retry with `thirdPartyScrapingEnabled: true` (routes scraping
  through third-party services; higher success on sites that block direct scraping) before
  concluding the sources are silent. It is off by default.
- `enableExtensiveSearch` finds more and is noticeably slower per run; leave it off unless
  the user wants depth over speed, or a pilot on five came back thin. For a long run,
  `sendEmailNotification: true` tells the user when it finishes.
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

## Waiting for a build

```
wait_for_flow(flowId)  ->  { done, knowledgeBaseId?, message? }
```

Prefer this to `get_last_flow_run_status` after any `create_*_kb` call. Each call blocks for
up to 25 seconds and returns as soon as the run reaches a terminal status. While `done` is
false, call it again with the same `flowId`. A news or social KB typically takes about ten
minutes, which is roughly two dozen consecutive calls, so do not stop after two.
Research-agent builds scale with the number of runs.

- State the `flowId` once when the build starts and report again when `done` is true. Do not
  narrate each call, and do not hand back to the user between calls.
- On `done`, the response carries `knowledgeBaseId`, or the failure reason in `message`.
- A dropped wait is safe to resume from the `flowId`: a run that finished in the meantime
  returns immediately.

`get_last_flow_run_status(flowId)` is the non-blocking check. Use it to look at a scheduled
flow: it reports the most recent run on the schedule, and the `knowledgeBaseId` it returns is
the first run's KB, because every run materialises a new timestamp-suffixed KB.

Once complete, `sample_kb(kb_id)` before doing anything else. A KB full of boilerplate,
paywall stubs or off-topic documents will produce a confident and wrong analysis
downstream. Two minutes of sampling saves the deliverable.

Useful follow-ups: `kb_search_documents` to probe a single KB,
`get_kb_field_statistics(kb_id, field_name)` for the distribution of a metadata field,
`check_kb_has_timestamp(kb_ids)` before promising anything time-based.
