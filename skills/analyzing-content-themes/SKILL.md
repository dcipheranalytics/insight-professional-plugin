---
name: analyzing-content-themes
description: Turns any reachable text corpus in Dcipher into a theme map - clusters, sub-themes, recurring questions and complaints - using the landscape workbench, and trends them over time where the documents carry timestamps. This is the general content-analysis and narrative-analysis capability: use it when the user wants to know what is in a body of text, built from news, social media or research agents. Note that these tools cannot ingest uploaded files, so corpora the user holds themselves - survey exports, transcripts, submitted documents - have to be reframed onto a public counterpart or a research question. Several skills apply this method to a specific audience: use analyzing-customer-voice for customer feedback, monitoring-narratives for news and social coverage, mapping-research-landscapes for publications, and querying-knowledge-bases when the user wants one question answered rather than the whole corpus mapped.
---

# Analyzing content themes

The general method for turning a body of text into structure. Every corpus-to-themes skill
in this plugin is a specialisation of what is written here; those skills add the audience,
the vocabulary and the reading, and reference this for the build.

Read `../dcipher-mechanics/references/analyst-standards.md` once per session - including
the data-policy section, which governs what may go into a corpus at all. Mechanics live in
`../dcipher-mechanics/references/`.

## Frame

**Whose words are these, and how did they get here?** Every corpus has a selection
mechanism, and it determines what the themes can support. People who filed a support
ticket, responded to a consultation, or posted in a forum are not a random sample of
anyone. Name the mechanism in the deliverable.

**What decision does the map serve?** "What's in this data" produces a labelled cluster
diagram nobody acts on. "What should we fix", "which arguments do we need to answer",
"where do respondents disagree" each produce a different read of the same landscape.

**Does time matter?** Trending needs timestamps. News and social carry them; check a
research-agent corpus with `check_kb_has_timestamp` before promising a trend line.

**Is the corpus reachable at all?** These tools build corpora from news, social media and
research agents. They cannot ingest the user's own files - survey exports, transcripts,
ticket dumps, submitted documents. If the material the user has in mind is theirs, say so
now, in the first exchange, and reframe onto a public counterpart or a research question
before any project is created. See `../dcipher-mechanics/references/knowledge-bases.md`.

## Build

**1. Corpus.** News, social, or research agents - see
`../dcipher-mechanics/references/knowledge-bases.md`. Mixed sources work well as separate
KBs attached to one project, and the split then becomes a `colorField`.

Where the user wanted their own documents analysed, the research-agent route is usually the
best substitute: instead of clustering their submissions, research the same question across
the public record. It answers a related question rather than the original one, so say which
you are answering.

**2. Sample before analysing.** `sample_kb`. Boilerplate, duplicate submissions, template
responses and paywall stubs all cluster beautifully and mean nothing. Filter them out with
a `pre` filter before building the landscape rather than explaining them afterwards.

**3. Schema.** `fetch_project_metadata_schema`. Whatever the corpus carries as metadata -
segment, region, source, respondent type, rating - is what turns a topic map into an
argument.

**4. Landscape.** The project is seeded with one; fetch it rather than creating a second.

```
update_insight_booster_landscape_workbench_config({
  id, workbenchId,
  outlierPolicy: "never",        // see below
  language: "English",           // full English name, never an ISO code
  colorField: "metadata.<the dimension that splits the corpus>",
  isContoursDisplayed: true,
  isAllDocsDisplayed: false,
  isColorHighlightDisplayed: true,
  isSelectedDocsPinned: false
})
get_landscape_max_broadness({ project_id, workbench_id })
get_landscape_result({ project_id, workbench_id, projection })
```

**`outlierPolicy`** is the main judgement call. `"never"` whenever small peripheral
clusters are the point - emerging issues, minority positions, new failure modes. `"always"`
on a noisy media corpus where the periphery is genuinely junk. `"auto"` if unsure.

**`colorField`** is what makes the map say something. Colour by respondent type and you see
which constituency holds which position. Colour by source and you see whether a theme is
one outlet's hobbyhorse. An uncoloured landscape is a list of topics.

**5. Read at two levels.** A high broadness level for the executive summary - five or six
themes someone can hold in mind - and level 1-2 for the specifics that are actually
actionable. Report both; they answer different questions.

**6. Trend, if timestamps exist.** `get_growth_result` with `broadness_level` for which
themes are accelerating; bump for what displaced what. Check `check_kb_has_timestamp` and
`get_project_time_range` first and skip silently on a short window rather than producing a
noisy chart.

**7. Questions as a separate output.** Recurring *questions* in a corpus are distinct from
recurring complaints and usually map to a different fix. Pull them with
`ask_research_chatbot` against the project, or as a dedicated report section.

## Reading it like an analyst

- **Cluster size is not importance.** Frequency and severity are different axes; say which
  you are ranking on, and ideally report both.
- **Quote.** Two verbatim sentences from a topic's `examples` do more than any label.
  Anonymise them - see the data policy.
- **The periphery is where the new thing is.** Check the small clusters before the large
  ones; the large ones are usually what the user already knows.
- **Silence is data.** A theme conspicuously absent from a corpus that should contain it is
  a finding.
- **Separate observation from inference.** "Forty-one submissions raise cost" is an
  observation. "Cost is the main barrier" is an inference.
- **Never report a sentiment proportion without its drivers.** A number with no reasons
  behind it cannot be acted on.

## Push back on

- Promising a trend line before checking `check_kb_has_timestamp`.
- Accepting a brief built on documents the user would have to upload.
- Generalising from a corpus without naming its selection mechanism.
- A theme resting on a handful of documents presented as a finding.
- A word cloud as a deliverable.

## Deliver

Themes ranked against the decision, each with: what it is, how many documents, which part
of the corpus it comes from, a verbatim example, and whether it is growing. Then the map.
Then the selection caveat, stated plainly.

If the corpus will be refreshed - a repeat survey, a rolling feedback export, a second
consultation round - template the report so the next round is comparable rather than a
fresh analysis. Comparability across rounds is worth more than a slightly better one-off.
