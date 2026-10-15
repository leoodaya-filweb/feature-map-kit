# Installing the Feature Map Kit in a repository

Works for any language or framework. Nothing is generated; everything is plain Markdown that an agent
or a person edits by hand.

## 1. Copy the starter

```
cp -r <kit>/starter/features <repo>/features
```

Commit it as-is. The base index renders with headers only and the self-checks pass on an empty map.

## 2. Fill the Repo profile

Open `features/_guide.md`. The block at the top (`## Repo profile`) is the only place in the whole
system that knows what stack this repository uses. Fill every field; every procedure refers to these
fields by name and to nothing else.

| Field | What to put |
|---|---|
| `name` | Project name as it should appear in the index title |
| `kit_version` | The kit version you copied (see the kit's `CHANGELOG.md`) |
| `base_url` | The local URL or launch command an agent uses in *Example use* |
| `test_cmd` | One command that runs the test suite from the repository root |
| `entry_kinds` | Which of `http-route`, `cli-command`, `scheduled-job`, `queue-consumer`, `screen`, `event-handler`, `webhook` exist here |
| `entry_globs` | Where each kind of entry point is declared (folders or globs) |
| `layer_map` | The ordered layers a request passes through and the folder of each |
| `test_globs` | Where tests live and how a test file is named for a given source file |
| `data_kind` | The word for persistent data here: tables, collections, files, stores, endpoints |
| `config_sources` | Where runtime configuration, flags and environment keys are read from |
| `behaviour_dirs` | Folders where any change is a behaviour change (a `Feature map: n/a` is never valid there) |
| `domains` | The ordered section headers of the base index |
| `areas` | Optional second axis: modules, apps, packages |
| `unmapped` | Paths intentionally owned by no node, each with a reason |
| `vcs_log` | Command that lists recently changed files (a default is given) |

`examples/profile-examples.md` shows filled blocks for a server-rendered app, a SPA or mobile app, an
API-only service and a CLI tool.

## 3. Wire the protocol into the files your tools read

- Paste `snippets/AGENTS.feature-map.md` into the repository's `AGENTS.md` (create the file if it does
  not exist). Add `features/README.md` to any "read these docs" list it has.
- Claude Code: paste `snippets/CLAUDE.feature-map.md` into `CLAUDE.md`.
- GitHub Copilot: copy `snippets/copilot-instructions.md` to `.github/copilot-instructions.md`
  (check `.gitignore`; some repositories ignore `.github/*`).
- Cursor: copy `snippets/cursor-feature-map.mdc` to `.cursor/rules/feature-map.mdc`.
- Codex CLI reads `AGENTS.md` natively; nothing to add.

Keep one source of truth: the protocol text lives in `AGENTS.md`; every other file is a pointer.

## 4. Make the PR line mandatory

Copy `snippets/pull_request_template.md` to `.github/pull_request_template.md`, or add its
`Feature map:` line to whatever PR convention the team uses. Reviewers check that line against the
diff. This is the enforcement.

## 5. Build the map

- **Greenfield (no existing map):** follow `features/_discovery.md`. One agent per node, batches of
  about ten per domain. Agents never edit `features/README.md` concurrently; a coordinator adds the
  rows after each batch.
- **Brownfield (an existing map or architecture document):** split it by section into draft nodes
  first, then enrich each draft with `_discovery.md` steps 3–5. Mapping guidance:

| Old document had | Becomes |
|---|---|
| A heading per feature | `features/<slug>.md` with `title:` and `slug:` (keep the old anchor as the slug or in `aliases:`) |
| A routes / endpoints line | `entry:` |
| A files / components list | `files:` (only paths that exist; directories end with `/`) and `## Chain` |
| A flow or steps list | `## Flow` |
| "Related to" / "See also" links | `connections:` with `TODO(verify)` as the reason until an agent cites code |
| Notes, caveats | `## Gotchas` |
| Tables / stores named | `data:` |
| Shared components sections | hub candidates (`hub-<slug>.md`) |
| Nothing | `## Summary`, `## Example use`, `## Tests` start as `TODO`; the node stays `status: draft` until they are real |

Finish with the sampling pass in `features/_maintain.md`: a different agent re-reads ten random nodes
against source and reports `clean`, `changed` or `blocked`.

## 6. Keep it alive

- Every PR that changes code updates the owning node(s) and ends with the `Feature map:` line.
- Run `features/_maintain.md` monthly or after a large merge.
- When a node crosses the size cap, split it (rule in `_guide.md`) instead of trimming content.

## Upgrading the kit

Copy the new `_template.md`, `_hub-template.md`, `_discovery.md`, `_maintain.md` and everything in
`_guide.md` **below** the Repo profile. Keep the profile. Bump `kit_version`. Existing nodes keep
working; new fields are optional unless the kit changelog says otherwise.
