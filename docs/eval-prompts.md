# Eval prompts

Two things get tested and they are different: **does the right skill fire** (trigger
accuracy) and **is the output any good** (quality). Run trigger tests first - a great skill
that never fires is worth nothing.

Method: run each prompt in a clean session with the connector attached. Record which skill
fired and whether the output met the bar. Re-run the whole trigger set whenever a skill is
added or a description is edited.

## Trigger tests

Expected skill in brackets. The ambiguous set at the end is the real test.

**Competitive intelligence** [`tracking-competitor-moves`]
- "What has Siemens Energy been up to this quarter?"
- "Keep an eye on our three main competitors and tell me when something changes"
- "Compare what ABB, Schneider and Siemens have launched in the last year"
- "Has anyone in our space made an acquisition recently?"

**Foresight** [`scanning-emerging-trends`]
- "What's emerging in grid-scale storage?"
- "Build me a trend radar for sustainable packaging"
- "What should our strategy team be watching over the next five years in logistics?"

**Market analysis** [`mapping-market-landscapes`]
- "Who's operating in the European heat pump market?"
- "How is the industrial IoT platform space structured?"
- "We're considering entering the Nordic EV charging market - what does it look like?"

**Technology scouting** [`scouting-technologies`]
- "Who can supply us solid-state battery technology?"
- "What are the routes to low-carbon cement and how mature is each?"
- "Find research groups working on protein fermentation"

**Customer voice** [`analyzing-customer-voice`]
- "Analyse these 400 support tickets and tell me what customers are complaining about"
- "What are the main themes in our app store reviews?"
- "Why are customers churning?"

**Narratives** [`monitoring-narratives`]
- "How is our brand being covered in German media?"
- "What's our share of voice versus our two main competitors?"
- "There's been a story about us this week - what's the narrative?"

**Corp dev** [`screening-acquisition-targets`]
- "Find me acquisition targets in European fintech under 50 employees"
- "Screen the market for potential partners with X capability"

**Regulatory** [`tracking-regulatory-change`]
- "What's changing in battery regulation across the EU, UK and US?"
- "Track CSRD implementation in our five markets"

**Scenarios** [`stress-testing-strategy`]
- "What could the next five years look like for European steel?"
- "Stress-test our 2030 capacity plan"

**Large-scale research** [`researching-entity-lists`]
- "I have a list of 180 municipalities - research each one's climate adaptation plan"
- "Run the same profile across all our suppliers"
- "For every EU member state, find out how they implemented this directive"

**Due diligence** [`running-due-diligence`]
- "We're about to sign with this vendor - what should we know?"
- "Do commercial diligence on Northvolt"
- "Background research on this company before the board meeting"

**Ecosystem / stakeholder** [`mapping-stakeholder-ecosystems`]
- "Who are the actors in the EU hydrogen policy arena?"
- "Map the stakeholders in this debate and where they stand"
- "Who's doing what in the circular economy space?"
- "Set up a monthly stakeholder newsletter for this policy area"

**Account intelligence** [`tracking-customer-accounts`]
- "What's been happening at our top 20 accounts?"
- "Brief me on this client before tomorrow's QBR"
- "Flag any of our accounts showing churn risk signals"

**Query** [`querying-knowledge-bases`]
- "What does our data say about hydrogen pricing?"
- "Find documents in the Q3 project mentioning Northvolt"
- "Ask the research bot about supply chain risk"

**Management** [`managing-insight-projects`]
- "What projects do we have?"
- "Clone the Q2 competitor project for Q3"
- "Archive everything we haven't touched this year"

### Deliberately ambiguous - the collision set

These should each resolve to one skill with a stated reason, or produce a clarifying
question. Record which fires. Disagreement here is what the description clauses are for.

- "What's happening in the battery industry?"
- "Tell me about the market for AI coding tools"
- "I need a competitive analysis of the European rail sector"
- "Write me a report on offshore wind"
- "What do we know about Northvolt?"
- "Give me an overview of sustainable aviation fuel"
- "Who are the players and what are they doing?"
- "Research these 40 companies for me"
- "Tell me everything about Northvolt"
- "Map this space"
- "Track these companies for me"  (rivals? customers? unstated - should ask)

## Quality tests

For a fired skill, the output should show these regardless of topic. Score each 0/1.

- [ ] Reframed or scoped the brief before building, rather than accepting it verbatim
- [ ] Checked for an existing project before creating one
- [ ] Called `get_search_queries_from_agent` before building a news or social KB
- [ ] Sampled the KB before analysing it
- [ ] Checked timestamps before promising anything time-based
- [ ] Used a result projection rather than pulling the full payload
- [ ] Cited sources with URLs on every claim
- [ ] Reported document counts alongside findings
- [ ] Named a coverage limitation (language, window, source type) unprompted
- [ ] Included the "state that nothing was found" clause in matrix and report instructions
- [ ] Pushed back where the standards file says to push back
- [ ] Offered the recurring / scheduled version where the deliverable repeats

## Baseline

Before a skill merges, run its prompts **without** the skill loaded and record what
happens. Most of the value claimed in these skills is in the checklist above; the baseline
is what shows whether the skill actually moved any of it.

| Prompt | Baseline behaviour | With skill | Delta |
|---|---|---|---|
| | | | |
