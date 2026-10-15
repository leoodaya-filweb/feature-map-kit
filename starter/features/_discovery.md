# Discovery: building or extending the map from source

Read this only when you are **creating or back-filling nodes**. Never during an ordinary fix — for
that, `AGENTS.md § Feature map` is enough. All stack facts come from the Repo profile in `_guide.md`.

## Inputs

- The Repo profile fields: `entry_kinds`, `entry_globs`, `layer_map`, `test_globs`, `data_kind`,
  `config_sources`, `domains`, `areas`, `unmapped`.
- A scope: the whole repository (first build), a list of slugs (back-fill), or a list of changed paths
  (after a large merge).

## Reading discipline (applies to every step)

- Search, list and count; do not read whole files. Open a file only at the function a Flow step names.
- Budget about 6–10k tokens of search output per node. If a node needs more, it is probably two nodes.
- Never read `features/` wholesale. Never edit `features/README.md` while other agents are writing
  nodes; a coordinator adds base rows after each batch.

## Procedure

### 1. Inventory entry points
For each kind in `entry_kinds`, list every declaration found under its `entry_globs`: routes and
their handlers, commands, scheduled jobs, queue consumers, screens or pages, event handlers,
webhooks. Produce a flat list: `kind | identifier | file | symbol`. Count it; this is the surface
the map must cover.

### 2. Cluster entries into features
Group entries that form one user flow or own the same data: same store or model, same screen family,
same command namespace, same resource in a REST path. Give each cluster a slug. Rules of thumb:
- A CRUD set over one resource is one feature unless a flow inside it has its own lifecycle
  (approval, import, export, sync) — that flow is a sub-feature `<parent>-<flow>`.
- A background job or command belongs to the feature whose data it produces or consumes; it is its
  own feature only when it serves several features or has its own operator-facing interface.
- Shared code reached by three or more clusters with no entry of its own is a **hub** candidate.
Mark unresolved groupings with `status: draft` rather than guessing.

### 3. Trace the chain, by search
For each feature, follow `layer_map` from its entries: which files in each layer it touches, which
public functions it calls, which stores it reads or writes (`data`, in `data_kind` terms), which
config keys it reads (`config_sources`), and which tests match its files (`test_globs`). Record
paths and symbols; exclude files owned by a hub unless the feature overrides them.

### 4. Record edges, with reasons
For every pair of features that interact, write one edge on the side that depends or feeds, with a
reason that names a symbol, route or store:
- **data**: A writes what B reads (`A: rows with status X are what B lists`)
- **call**: A invokes B's logic (`A: calls B's ExportService::run()`)
- **navigation**: A links or redirects to B
- **workflow**: A must happen before B (status transitions, approval chains)
Hub edges likewise, only where the feature deviates from an implicit hub or uses a non-implicit one.
If you cannot cite code, write `TODO(verify)` and leave the node `draft`.

### 5. Write the node
Copy `_template.md` (or `_hub-template.md`), fill frontmatter and all sections. `Example use` must
contain at least one runnable line built from `base_url` or a command, plus the `test_cmd` invocation
for the node's tests. Set `status: active` only when Example use is real, Tests is real and no
`TODO(verify)` remains. Hand the base row (`slug | summary | links`) to the coordinator.

### 6. Coverage check
After a batch: every file under `entry_globs` (minus `unmapped`) must appear in exactly one node's
`files:`. List the leftovers; each becomes a new node, joins an existing one, or is added to
`unmapped` with a reason. Then run the self-checks in `_guide.md`.

## Brownfield: an existing map or architecture document

1. Split the old document by section into draft nodes (mapping table in the kit's `INSTALL.md`).
   Keep old anchors as slugs or `aliases:` so existing links survive.
2. For each draft run steps 3–5 above: verify every path, replace every `TODO(verify)` with a cited
   reason or delete the edge, write Summary / Example use / Tests, flip to `active`.
3. Run step 6 to find what the old document never covered.
4. Finish with the sampling pass in `_maintain.md`.

## Parallelizing

One agent per node, batches of about ten within a domain. Each agent receives: its slug(s), the
Repo profile, this file, the template, and the instruction to write only its own node file(s). The
coordinator writes base rows, runs the coverage check and the self-checks, and triages leftovers.
