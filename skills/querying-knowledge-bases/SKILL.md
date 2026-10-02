---
name: querying-knowledge-bases
description: Answers questions against data already in a Dcipher Insight Booster project using the research bot and document search, returning cited answers and the underlying documents. Use whenever the user asks what their existing Dcipher data says about something, wants to find documents mentioning a term, or wants a quick answer without building anything new - "what does our data say about X", "find documents about Y", "ask the research bot". Use the analysis skills instead when answering requires building a new knowledge base or workbench, and managing-insight-projects when the user wants to manage projects rather than query their content.
---

# Querying knowledge bases

The fast path: the data already exists, the user has a question, and no new analysis needs
to be built. This is the most frequently used skill in the plugin. Keep it quick - if you
find yourself creating a workbench, you are in the wrong skill.

## Find the right project first

```
list_insight_boosters                     # unless the user named one
get_insight_booster(id)
list_insight_booster_knowledge_bases(id)  # what is actually in scope
```

Never guess a project id. If several projects could answer the question, say which you
picked and why - the answer depends entirely on the corpus behind it, and the user may know
a better one.

Before answering, know what the project contains. An answer drawn from a project whose KBs
do not cover the question is confidently wrong, and this is the main failure mode here.
Check the attached KBs, and `get_project_time_range` if the question has any time element.

## Pick the retrieval tool

| Question | Tool |
|---|---|
| Anything analytical or open-ended | `ask_research_chatbot` |
| "Find documents mentioning X" | `ib_search_documents` (project-wide) |
| Same, but within one KB | `kb_search_documents` |
| "Show me the actual text" | `ib_get_documents` |
| Distribution of a metadata field | `get_kb_field_statistics` |
| "What is in this dataset?" | `sample_kb` |

### `ask_research_chatbot`

```
ask_research_chatbot({
  project_id,
  question,
  answer_mode: "direct" | "subtopic-summary",
  use_agentic_mode: true,          // default; better on multi-part questions
  filters: [ { label, values, revert? } ]   // request-scoped, merged with project filters
})
```

- `direct` for a specific factual question. `subtopic-summary` when the question has
  natural sub-parts ("what are the main concerns raised") - it returns a structured
  breakdown instead of one paragraph.
- `filters` here are local to the single request and merge with the project's filters. Use
  them to scope a question to a region or source without disturbing the project's saved
  state. Field labels must come from `fetch_project_metadata_schema`.
- `fetch_research_chatbot_conversation_history` retrieves recent turns for continuity.

Ask one question at a time. Multi-part questions retrieve worse than two separate calls,
even in agentic mode.

### Search

`ib_search_documents` returns ids only - pair it with `ib_get_documents` for text. Use
`exact_search: true` for names, codes and quoted phrases, and leave it false for concepts.
Default `limit` is 1000; lower it when you only need a count or a sample.

## Answer like an analyst, not a search box

- **Cite everything.** Every claim gets a source and URL from the returned references. An
  uncited answer from a research corpus is not usable.
- **Say how much evidence there is.** "Six documents, all from Q1, all from two outlets" is
  part of the answer, not a footnote.
- **Distinguish what the corpus says from what is true.** The research bot reports the
  corpus. If the corpus is one-sided, say so.
- **Report absence honestly.** "The project contains nothing on this" is a complete and
  useful answer. Never fill the gap from general knowledge without labelling it clearly as
  outside the data.
- **Notice when the question outgrows the tool.** "How has this changed over the last two
  years" or "how do these five companies compare" is an analysis, not a lookup. Say so and
  point at the right skill rather than assembling a weak version from chat answers.

## Configuring an embedded research bot

Separate task, same neighbourhood - the widget embedded on a client site:

```
get_insight_booster_research_bot_config(id)
update_insight_booster_research_bot_config({
  id,
  domains: ["*.client.com"],       // EMPTY LIST ALLOWS ANY ORIGIN
  rateLimit: 20,                   // per minute per visitor IP; omit for unthrottled
  uiConfig: { mode, fontFamily, fontSize, userMessageColor, answerModes }
})
```

Two things to flag to the user every time: an empty `domains` list allows embedding from
**any** origin, and `uiConfig` is replaced wholesale, so send every key you want to keep.
Recommend setting both `domains` and `rateLimit` before a public deployment.
