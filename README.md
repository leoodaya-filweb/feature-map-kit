# Feature Map Kit

A plain-Markdown feature map that lives inside any repository, works with any framework, and is
read and maintained by any AI coding agent (Claude Code, Codex CLI, GitHub Copilot, Cursor) and by
people. No tooling, no external software, no build step.

## The problem it solves

Agents either read the whole codebase before touching a file (slow, expensive, and still wrong about
connections) or read one huge "architecture" document that costs tens of thousands of tokens per task
and rots because nobody owns it.

## The model: two hops, then source

```
features/README.md          connections only: one row per feature (slug | summary | links)   ~5-6k tokens per 100 features
   └─ features/<slug>.md    one feature: summary, example use, entry points, chain, flow,
                            gotchas, tests, changelog                                          ~1-2.5k tokens
         └─ source files    exactly the files the node lists, at the symbol the Flow names
```

An agent always opens the base first, then at most three feature files, then source. A coordinator
that already knows the slugs hands them to sub-tasks, which skip the base. Nothing is ever read
wholesale.

## Principles

- **Connections only in the base.** The index routes; it never explains. Reasons, flows and gotchas
  live one hop away.
- **One fact, written once.** A feature's outgoing links live in its own frontmatter and are mirrored
  in exactly one index row. Reverse links and "who owns this path" are `grep`, which every harness has.
- **Every change updates the map.** Whoever changes code updates the owning node in the same commit
  or PR and adds one Changelog line: `symptom -> cause -> change. Test: Symbol`. That line is how the
  map learns.
- **Bounded by rules, not tools.** Size caps, a ten-line changelog with a promotion ladder, and a
  split rule keep nodes small. Four copy-paste one-liners are the whole self-check.
- **Stack-agnostic by construction.** Nothing in `starter/` or `snippets/` names a framework. All
  stack facts live in a per-repository *profile* that the procedures refer to by field name.
- **Honest enforcement.** Nothing mechanical blocks a stale map. Reviewers and the mandatory
  `Feature map:` line in every PR hold it. A repository may add its own CI check later.

## What is in this repository

| Path | Purpose |
|---|---|
| `starter/features/` | Copy into a repo as `features/`: base skeleton, guide (profile + rules), node templates, discovery and maintenance procedures |
| `starter/snippets/` | Text to paste into `AGENTS.md`, `CLAUDE.md`, Copilot and Cursor pointer files, and a PR template |
| `examples/` | Filled profiles for common repository shapes and one complete sample feature and hub (illustrations only) |
| `INSTALL.md` | Adoption in six steps, brownfield migration from an existing map, upgrading the kit |
| `CHANGELOG.md` | Kit versions; adopters record `kit_version` in their profile |

## Install in six steps

See `INSTALL.md`. Short form: copy `starter/features` → fill the Repo profile in `features/_guide.md`
→ paste `snippets/AGENTS.feature-map.md` into `AGENTS.md` → add the PR line → build nodes with
`features/_discovery.md` → make the `Feature map:` line part of review.

## Credits

The navigate / maintain split, the "one file per feature with how to reach it and gotchas" shape and
the `clean / changed / blocked` audit outcome follow Lauren Tan's **pstack**
(`create-verification-skill`, `maintain-verification-skill`). The one-note-per-concept-with-links
layout follows Obsidian; the hub ("most connected node") view follows Graphify. This kit keeps all of
that in plain files inside the repository.
