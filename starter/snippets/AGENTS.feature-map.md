## Feature map (required for every code task)

`features/README.md` is a connections-only index of this codebase; `features/<slug>.md` holds one
feature's summary, example use, entry points, chain, flow, gotchas, tests and lessons. It cuts what
you read before touching source from the whole codebase to one index plus one to three files. It is
maintained by hand by whoever changes code — including you. Format, caps and self-checks:
`features/_guide.md`. Building or auditing nodes: `features/_discovery.md`, `features/_maintain.md`.

Navigate
1. Open `features/README.md`. Find the domain, then the row whose summary matches. Never read the
   `features/` folder wholesale. Sub-task exemption: a coordinator that already resolved the slugs
   hands them to its sub-tasks, which start at step 3 — the base is read once per task, not per agent.
2. Holding a path, route, store name or stack trace? `grep -l "<it>" features/*.md` gives the owning
   file(s).
3. Open ONLY the `features/<slug>.md` for those rows (at most 3). Bug: read Gotchas and Changelog
   first ("was this seen before?"), then Flow. Usage question: Example use and Entry points.
4. Go to the source files the node lists; search for the symbol the Flow names. Open a linked
   feature only when its edge reason names the thing you are working on.
5. A proof that uses one entry point is incomplete if `entry:` lists others sharing the code path.

Update (same commit or PR as the code change)
6. Before editing: `grep -l "<each path you will touch>" features/*.md` gives the owning slugs; the
   one whose behaviour you change is the primary.
7. New feature: copy `features/_template.md` to `features/<slug>.md` BEFORE coding; fill Summary,
   Example use and the planned `files:`/`connections:` first — they are the spec tests are written
   against. Add its base row.
8. After the change: edit the primary node — keep `files/entry/tests/data/connections/hubs` true, fix
   Flow/Chain/Gotchas your change made wrong, prepend ONE Changelog line
   (`- YYYY-MM-DD (kind, ref): symptom -> cause -> change. Test: Symbol`), set `updated:`. Secondary
   owners get one Changelog line and `updated:`. If summary or connections changed, edit that base row.
9. Run the four self-check one-liners in `features/_guide.md` (every node has a row, every row has a
   node, every listed path exists, no `TODO(verify)` in an active node).
10. End the PR or commit description with `Feature map: updated features/<slug>.md[, ...]` or
    `Feature map: n/a — <reason>`. `n/a` is never valid when a path under the Repo profile's
    `behaviour_dirs` changed.
11. Merge conflicts: base rows — keep both, re-sort the domain. Changelog — keep both, newest first.
