# Feature map guide

Read this file when you **write or change** a node, not when you only navigate. The protocol
(navigate / update steps) lives in `AGENTS.md § Feature map`.

## Repo profile

The only stack-specific block in the system. Every procedure in `features/` refers to these fields by
name. Fill it once at adoption; keep it current when the repository's layout changes.

- name:            <project name>
- kit_version:     1.0.0
- base_url:        <local URL or launch command used in Example use>
- test_cmd:        <one command that runs the test suite from the repository root>
- entry_kinds:     <subset of: http-route, cli-command, scheduled-job, queue-consumer, screen, event-handler, webhook>
- entry_globs:     <where entry points are declared, per kind>
- layer_map:       <ordered layers and their folders: entry -> logic (<dir>) -> data (<dir>) -> presentation (<dir>) -> config (<dir>)>
- test_globs:      <where tests live and the naming rule that maps a source file to its test>
- data_kind:       <tables | collections | files | stores | endpoints>
- config_sources:  <where runtime config, flags and environment keys are read from>
- behaviour_dirs:  <folders where any change is behaviour; "Feature map: n/a" is never valid there>
- domains:         <ordered README sections, for example auth, billing, admin, shared>
- areas:           <optional: modules / apps / packages>
- unmapped:        <paths owned by no node, each with a reason>
- vcs_log:         git log --since=30.days --name-only --pretty=format:

---

Everything below is generic and identical in every repository that uses this kit version.

## Vocabulary

| Term | Meaning |
|---|---|
| Node | One file in `features/`: a **feature** (`<slug>.md`) or a **hub** (`hub-<slug>.md`) |
| Base | `features/README.md`: one row per node, connections only |
| Feature | Anything with an entry point of its own (one of the profile's `entry_kinds`) that a tester can exercise end to end. One node may own several handlers when they are one user flow. A command or job belongs to the feature it serves unless it is its own subsystem. |
| Hub | No entry point of its own AND used by three or more features AND has a contract or gotcha worth writing down. Trivial shared pieces get no node. |
| Implicit hub | A hub every feature of the listed `areas` uses without declaring it (a base class, a shared layout, an auth filter). Features list it only when they deviate from its default. |
| Edge | A line under `connections:` (feature to feature) or `hubs:` (feature to hub): `- target-slug: reason`. Written once, on the side that depends or feeds. Reverse edges are found with grep. |
| Owner | The node whose `files:` contains a path. A path has one owner; parent and sub-feature pairs may share a handler file, and hubs may overlap with features. |

## Frontmatter grammar

A strict subset of YAML so every model writes it the same way and `grep` can read it:

```
key: scalar                 # rest of line, verbatim; no quotes needed
key: [a, b, c]              # inline list: allowed for aliases, data, config, implicit only
key:                        # block list: REQUIRED for entry, files, tests, connections, hubs
  - item
  - target-slug: reason     # only under connections: and hubs:
```

No nested maps, no multi-line values, no anchors. `files:` and `tests:` must be block lists (one path
per line) so the self-checks can see them; an empty list is written `[]`. Paths are repository-root relative, exact case; a directory
ends with `/`. Required keys for a feature: `slug title kind domain status summary entry files tests
data config connections hubs updated`; for a hub: `slug title kind domain status summary implicit
exposed_as files tests updated`. `area` and `aliases` are optional.

## Body

Fixed H2s, in this order. Feature: **Summary, Example use, Entry points, Chain, Flow, Gotchas, Tests,
Changelog**. Hub: **Summary, Contract, Example use, Gotchas, Changelog**. Delete the `<!-- -->` hints
from the template before committing. No line numbers anywhere; anchor on symbols
(`Class::method`, `function name`) and let the reader search.

## Naming

- `features/<slug>.md`; the slug matches `^[a-z0-9]+(-[a-z0-9]+)*$` and equals frontmatter `slug`.
- Hubs: `hub-<slug>.md` with `kind: hub`.
- Renames keep the old slug in `aliases:` and in the base's `## Aliases` section.
- Files starting with `_`, `README*.md` and `.htaccess` are not nodes.

## Size caps and the split rule

| Thing | Target | Hard stop | Remedy |
|---|---|---|---|
| Feature node | ≤ 140 lines / 7 KB | 180 lines / 10 KB | split (below) |
| Hub node | ≤ 60 lines / 3 KB | 80 lines / 4 KB | move detail to the repository's architecture doc |
| Changelog | ≤ 10 lines, ≤ 200 chars each | — | promotion ladder (below) |
| Gotchas | ≤ 8 bullets | 10 | promote to the architecture doc or delete |
| Flow | ≤ 12 steps | — | split by flow |
| summary | ≤ 90 chars, not equal to the title | — | rewrite |
| Fenced code block | ≤ 6 lines | — | replace with a symbol name |
| Base index | ≤ 300 lines | — | move the largest domain to `README-<domain>.md`, linked from the header |

**Split rule.** Over the target, split by flow into `<parent>-<flow>` (for example `orders-refunds`
out of `orders`) or along a job or CLI boundary. The child owns that flow's entries, files, tests and
gotchas; the parent keeps the shared handler file and links the child with the reason
`sub-feature: <what the child owns>`; the child links back with `parent: <slug>`.

**What goes nowhere.** Per-handler prose for inherited or generic actions (one row pointing at the
hub), field lists, code excerpts, screenshots, history older than ten changelog lines (version control
has it), anything already in the repository's architecture documents (link instead).

## The lesson line

Every change that touches a node's `files:` adds exactly one line at the top of its Changelog:

```
- YYYY-MM-DD (fix|feat|refactor|docs|migration, PR/ref): symptom -> cause -> what changed. Test: Symbol
```

Example: `- 2026-10-20 (fix, PR #104): retry re-queued while a job was pending -> handler never checked the queue -> guard before push. Test: RetryTest::skipsWhenPending`

**Promotion ladder, one direction only:** Changelog → Gotchas (still true, cross-cutting) → the
repository's architecture document (changes runtime flow). When the 11th line arrives, decide for the
oldest, then delete it. A product bug you are not fixing now goes to Gotchas as
`- KNOWN BUG (#ref): <symptom>`, never documented as behaviour. Until an issue exists write `(no issue yet)` in place of `#ref`; whoever opens the issue replaces it.

## The PR line and the waiver

Every PR or commit message that changes code ends with one of:

```
Feature map: updated features/<slug>.md[, features/<slug>.md ...]
Feature map: n/a — <reason, 10+ characters>
```

`n/a` is valid only for formatting, comments, pure presentation or generated files, and **never** when
a path under the profile's `behaviour_dirs` changed. Reviewers check the line against the diff. Nothing
mechanical blocks a stale map; that review is the enforcement. A repository may add its own CI check
later; the kit does not ship one.

## Self-checks

Copy-paste, no tooling. POSIX shell (Git Bash on Windows). Each prints nothing when the map is sound.

```bash
# 1. every node has a base row
for f in features/*.md; do s=$(basename "$f" .md); case $s in _*|README*) continue;; esac; grep -q "^| $s |" features/README*.md || echo "NOT IN README: $s"; done
# 2. every base row has a node
grep -ohE '^\| [a-z0-9-]+ \|' features/README*.md | tr -d '| ' | grep -vx slug | while read -r s; do [ -f "features/$s.md" ] || echo "NO FILE: $s"; done
# 3. every path listed in a block list exists
grep -hoE '^  - [^ :]+/[^ ]*' features/*.md | sed 's/^  - //' | sort -u | while read -r p; do [ -e "$p" ] || echo "MISSING PATH: $p"; done
# 4. no unverified edge in an active node
grep -l "TODO(verify)" features/*.md | xargs -r grep -l "^status: active"
```

Also worth a glance before a PR: `wc -c features/<slug>.md` against the caps, and that the base row's
`links` equals the node's `connections:` slugs.

## Merge conflicts

Base rows: keep both sides, re-sort the domain alphabetically. Changelog: keep both, newest first.
Frontmatter lists: union, then verify that the paths exist.
