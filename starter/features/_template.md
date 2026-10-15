---
slug: <equals filename without .md>
title: <Human Title>
kind: feature
domain: <one of the Repo profile domains>
area: <one of the Repo profile areas, or delete this line>
status: draft
summary: <one line, <= 90 chars, not the title; copied verbatim into the README row>
aliases: []
entry:
  - <VERB /route | cli <command> | job <name> | screen <route> | event <name> | webhook <name>>
files:
  - <repo-root-relative path; directories end with />
tests:
  - <test file path>
data: []
config: []
connections:
  - <other-feature-slug>: <why — name the symbol, route or store that creates the dependency>
hubs:
  - <hub-slug>: <why — only when this feature deviates from the hub's default contract>
updated: YYYY-MM-DD
---

# <Human Title>

## Summary
<!-- 2-3 sentences: what it does, who triggers it, what it produces. No file paths. -->

## Example use
<!-- At least one RUNNABLE line: a <base_url>/... URL or screen path, a CLI or API call, and the
     <test_cmd> invocation for this feature's tests. This is how an agent exercises the feature. -->

## Entry points
<!-- Table: Entry | Handler (Symbol) | Kind / access | Note. Only entries this feature implements
     itself; inherited or generic ones are one row "-> hub-<slug>". No line numbers. -->

## Chain
<!-- One bullet per layer of the Repo profile layer_map: entry -> logic (public functions used)
     -> data (stores, key constants) -> presentation/output -> config. Paths and symbol names, no code. -->

## Flow
<!-- Numbered happy path, 5-12 steps, each naming the function or method that does it. -->

## Gotchas
<!-- <= 8 bullets. Durable traps: config, ordering, silent fallbacks, platform differences.
     A product bug not being fixed now: "- KNOWN BUG (#ref): <symptom>" — never describe a bug as behaviour. -->

## Tests
<!-- How to run them, what the suite proves, known gaps. Paths live in frontmatter tests:. -->

## Changelog
<!-- Newest first, <= 10 lines, <= 200 chars each:
     - YYYY-MM-DD (fix|feat|refactor|docs|migration, PR/ref): symptom -> cause -> what changed. Test: Symbol
     When an 11th line arrives: promote the oldest to Gotchas (still true) or to the repo's architecture
     doc (changes runtime flow), otherwise delete it. Version control is the archive. -->
