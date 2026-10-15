# Feature Map — <name> (index)

Connections only. Each row is `features/<slug>.md`. Read this file first, then open ONLY the feature
file(s) for your task (at most 3), then the source files they list. `links` = features this one
depends on or feeds; the reason for each link, entry points, flow, tests and gotchas live in the file.
- Who links TO a feature:    grep -l "^  - <slug>:" features/*.md
- Which feature owns a path: grep -l "<path>" features/*.md
- Changing a feature? Follow AGENTS.md § Feature map. Format and caps: features/_guide.md.

## <domain>
| slug | summary | links |
|---|---|---|

## shared (hubs)
| slug | file | contract |
|---|---|---|

## Aliases
