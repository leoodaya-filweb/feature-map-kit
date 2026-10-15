# Maintenance: auditing the map against the source

Run on demand ("audit the feature map"), monthly, or after a large merge. Scope: a list of slugs, or
all nodes. Read-only against source; the only files you may edit are nodes and their base rows.

## Pass

1. **Index check.** Compare base rows with node files: rows without a file, files without a row,
   duplicate slugs, aliases that collide with slugs, rows whose `links` differ from the node's
   `connections:`. Fix these first; they are mechanical.
2. **Source compare, per node.** Using search (not whole-file reads), confirm each `entry:` still
   exists at the named handler, each `files:`/`tests:` path exists, each edge reason still names a
   real symbol, route or store, and each Flow step names a function that still does that. Note every
   discrepancy with a citation (`Symbol in path`).
3. **Churn sweep.** Run the profile's `vcs_log`. For every changed path under `entry_globs` or
   `behaviour_dirs`, find its owner with `grep -l "<path>" features/*.md`. A path with no owner is a
   missing surface; a path whose owner's `updated:` predates the change is a stale node. A surface
   counts as missing only with a concrete path.
4. **Reconcile.** Edit the node (and its base row if summary or links changed). Add a Changelog line
   `(docs, audit)` describing what was corrected. Never touch product code.
5. **Triage every finding** as exactly one of:
   - **doc-drift** — the node was wrong or incomplete: fix it now.
   - **harness-gap** — the map could not express something (a kind of entry point, a layer): note
     it under the Repo profile in `_guide.md` and, if general, propose it to the kit.
   - **product-gap** — the code is broken or behaves against its own intent: write
     `- KNOWN BUG (#ref): <symptom>` in Gotchas and open an issue. Never describe a bug as behaviour,
     never weaken a node so that it matches a bug.
6. **Outcome**, exactly one of:
   - `clean` — every node in scope checked, nothing to change.
   - `changed` — corrections made; list them.
   - `blocked` — say exactly what prevented coverage (missing access, unrunnable test command,
     ambiguous ownership) and which nodes were not checked.

## Report

```
outcome: clean | changed | blocked
| slug | outcome | citations |
|---|---|---|
| <slug> | clean / doc-drift fixed / product-gap (#ref) / blocked: <why> | <Symbol in path>, ... |
missing surfaces: <path> -> <proposed node> ...
```

## Sampling pass (after a first build or a bulk migration)

Pick ten nodes at random, give each to a **different** agent than the one that wrote it, run steps 2
and 5 only, and report per node. More than one doc-drift in ten means the batch that produced them is
redone before the map is trusted.
