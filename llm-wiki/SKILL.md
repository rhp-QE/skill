---
name: llm-wiki
description: >
  Operate the user's personal LLM-wiki knowledge base. Use this skill whenever
  the user mentions llm_wiki, llm-wiki, personal wiki, knowledge base, inbox,
  ingest, query my wiki, lint/check wiki, migrate old notes, Obsidian notes,
  "沉淀到 wiki", "入库", "处理 inbox", "记录到我的知识库", or provides a new
  learning note/diary/article/idea and wants it remembered. This skill bootstraps
  fresh Codex sessions by locating the wiki root, reading its local operating
  files, and running Query, Ingest, Lint, Setup/Migration, or Report workflows.
  Shortcut commands: /wiki-ingest, /wiki-query, /wiki-lint, /wiki-migrate,
  /wiki-report.
---

# LLM Wiki

This skill operates the user's local personal LLM-wiki. It exists so the user does not need to paste a bootstrap prompt in every new Codex session.

## Contract

- Default wiki root is `/root/llm_wiki`.
- If the current working directory already contains `system/skills/llm-wiki/SKILL.md`, use the current directory as the wiki root.
- Do not ask the user to paste startup instructions. This skill is the startup instruction.
- Before mutating the wiki, read the local operating files listed below.
- Preserve raw material in `sources/` and compiled knowledge in `wiki/`.
- Update `wiki/log.md` after meaningful mutations.
- Query is read-only. Ingest, Lint, Setup/Migration, and persisted Reports may mutate files.

## Bootstrap

When the skill triggers:

1. Locate the wiki root:
   - Prefer current working directory if it has `AGENTS.md` and `system/skills/llm-wiki/SKILL.md`.
   - Otherwise use `/root/llm_wiki` if it exists.
2. Read, in order:
   - `AGENTS.md`
   - `system/skills/llm-wiki/SKILL.md`
   - `system/lifecycle.md`
   - `system/resolver.md`
   - `wiki/index.md`
3. Then resolve the user's intent and run the matching workflow.

## Workflow Selection

Use the local `system/resolver.md` as the source of truth.

- `/wiki-ingest`: process pasted content or `inbox/` as new material.
- `/wiki-query`: answer from existing wiki only; read-only.
- `/wiki-lint`: run health checks for links, citations, stale pages, duplicates, and schema.
- `/wiki-migrate`: run stock setup/migration; must start with inventory, mapping, and sample import.
- `/wiki-report`: generate a persisted or conversational report depending on the user's request.

- **Query**: user asks a question about existing wiki knowledge. Read-only.
- **Ingest**: user provides new material, asks to process `inbox/`, or says "沉淀", "入库", "记录".
- **Lint**: user asks to check health, links, citations, stale pages, duplicates, or schema.
- **Setup / Migration**: user wants to import historical notes, old Obsidian vaults, diaries, folders, or large batches. Must run inventory, mapping, sample import, sample validation, full import, rebuild, health check, and migration report.
- **Report**: user asks for briefing, pulse, task report, weekly review, learning review, health report, or migration report.

## Ingest Minimum Bar

For new material:

1. Preserve the raw note under `sources/` unless it is already archived.
2. Enrich durable entities, relationships, timeline entries, concepts, Q&A, aliases, and review tasks.
3. Fix citations enough that future agents can trace claims.
4. Run the relevant maintenance checks.
5. Update `wiki/log.md`.
6. Return an auditable summary.

## Migration Minimum Bar

For stock or historical import:

1. Inventory sources.
2. Design mapping from old structure to this wiki.
3. Import 5-10 representative samples.
4. Validate samples before bulk work.
5. Only then run full import.
6. Rebuild derived structures.
7. Run health check.
8. Write a migration report.

Do not skip sample validation.

## Output Format

For mutating workflows, end with:

```text
workflow:
wiki_root:
inputs:
files_read:
files_created:
files_updated:
sources_created_or_used:
pages_created:
pages_updated:
links_added:
citations_fixed:
maintenance_done:
open_questions:
needs_user_review:
next_actions:
```

For Query, end with:

```text
answer:
pages_consulted:
source_evidence:
uncertainties:
suggested_followups:
```

## Anti-Patterns

- Do not require the user to paste `START_HERE.md`.
- Do not treat generic model knowledge as the user's knowledge unless the user supplied, accepted, or applied it.
- Do not rewrite raw notes in place.
- Do not full-import historical data before sample validation.
- Do not silently mutate the wiki without `wiki/log.md`.
- Do not create many domain-specific skills when one local wiki skill plus directory `AGENTS.md` files is enough.
