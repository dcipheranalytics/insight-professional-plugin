# Contributing skills

## Where things live

- Skills are directories: `skills/<gerund-name>/SKILL.md`, plus optional
  `references/`, `scripts/`, `assets/` inside the skill directory.
- Shared machinery goes in `skills/dcipher-mechanics/references/`, referenced from a skill
  as `../dcipher-mechanics/references/<file>.md`. Do not restate mechanics in a use-case
  skill - it will drift.
- Naming: gerund phrase, lowercase, hyphenated, named after the deliverable
  (`tracking-competitor-moves`), not the role (`competitive-intelligence`) and not the
  workbench (`landscaping-content`).

## Authoring workflow

1. **Agree the row first.** Deliverable, subrole, the task phrases a user would actually
   type, the tool chain, and what unassisted Claude gets wrong today. A short doc or table
   is fine for this; the SKILL.md itself is written in Git.
2. **Write the eval prompts before the skill.** Five to ten real prompts, added to
   `docs/eval-prompts.md`. Run them against the connector *without* the skill and record
   what happens. That baseline is what makes the later review objective rather than a
   taste argument.
3. **Draft.** Use `skill-creator` if you have it - it handles description optimisation and
   eval loops. Otherwise copy the shape of an existing skill here.
4. **Test in a separate session.** One session refines the SKILL.md, another tests it cold.
   Feed failures back as specific complaints ("it built the matrix without the
   'no significant activity' clause, so three cells are invented") rather than rewrites.
5. **PR.** One skill per PR. Include the eval results in the description.

## PR checklist

- [ ] `name` matches the directory name; gerund, lowercase, hyphenated.
- [ ] `description` states **what** the skill does *and* **when** to use it, in third
      person, and carries explicit "use X instead when..." clauses for its nearest
      neighbours.
- [ ] Description read alongside every other description in the repo, not in isolation.
- [ ] SKILL.md under ~500 lines; mechanics delegated to `dcipher-mechanics`, not repeated.
- [ ] Every tool name and parameter checked against the live schema - not against this
      repo, and not from memory.
- [ ] No time-sensitive facts (dates, "currently", model versions in prose).
- [ ] File references are one level deep and resolve from the skill directory.
- [ ] Contains real analyst judgement - defaults, push-back conditions, how to read the
      output - not a restatement of what the tools do.
- [ ] Eval prompts added to `docs/eval-prompts.md` and run.
- [ ] Neighbouring skills re-tested: adding a skill degrades its neighbours' triggering,
      and you only find out if you check.

## Trigger collisions

The single highest-risk failure mode. "What's happening in the battery industry"
legitimately matches foresight, market mapping, competitor tracking and narrative
monitoring at once.

Rules:

- Every description names its nearest neighbours and says when to prefer them.
- The README table is the canonical index. Update it in the same PR.
- When adding a skill near an existing one, re-run the existing skill's eval prompts and
  confirm it still fires. Do not merge on the new skill's evals alone.

## Verifying against the live connector

Tool names, parameters and enum values in this repo were transcribed from the MCP schemas
and from
<https://help.dcipheranalytics.com/en/articles/16540771-supported-tools>. They will drift.
Before relying on a snippet, check the live schema. Enum values that change silently and
break things: report `engine` identifiers, radar `mode`, workbench `type`, social
`platform`.
