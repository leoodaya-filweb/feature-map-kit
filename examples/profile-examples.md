# Repo profile examples

Illustrations only. The kit itself never names a framework; these show how four common repository
shapes fill the same fields. Copy the closest one into `features/_guide.md` and adjust.

## A. Server-rendered MVC web app (PHP, Ruby, Python, Java…)

```
- name:            Acme Admin
- kit_version:     1.0.0
- base_url:        http://localhost:8080
- test_cmd:        make test
- entry_kinds:     http-route, cli-command, scheduled-job, queue-consumer
- entry_globs:     app/controllers/**, app/console/**, app/jobs/**, config/routes.*
- layer_map:       entry (app/controllers) -> services (app/services) -> models (app/models) -> jobs (app/jobs) -> views (app/views, public/js) -> config (config/, .env keys)
- test_globs:      tests/**/<Name>Test.*  (unit under tests/unit, HTTP under tests/functional)
- data_kind:       tables
- config_sources:  config/params.*, .env
- behaviour_dirs:  app/controllers, app/services, app/models, app/jobs, app/console
- domains:         auth, accounts, billing, catalog, reports, admin, shared
- areas:           web, api, cli
- unmapped:        app/console/HelloCommand.* (framework sample)
- vcs_log:         git log --since=30.days --name-only --pretty=format:
```

## B. SPA or mobile app (Angular, React, Vue, Flutter…)

```
- name:            Acme Mobile
- kit_version:     1.0.0
- base_url:        http://localhost:4200  (device: `make run-android`)
- test_cmd:        npm test
- entry_kinds:     screen, event-handler, scheduled-job
- entry_globs:     src/app/pages/**, src/app/**/*.routes.*, src/app/**/*.page.*, src/background/**
- layer_map:       screen (src/app/pages) -> state (src/app/state) -> services (src/app/services) -> native bridges (src/app/native) -> storage (src/app/storage) -> config (src/environments/)
- test_globs:      **/*.spec.*  next to the source file
- data_kind:       stores
- config_sources:  src/environments/*.ts, remote config keys
- behaviour_dirs:  src/app/services, src/app/state, src/app/native
- domains:         onboarding, sync, maps, capture, settings, shared
- areas:           app, background
- unmapped:        src/app/pages/dev-tools/ (internal only)
- vcs_log:         git log --since=30.days --name-only --pretty=format:
```

## C. API-only service (Node, Go, Python, .NET…)

```
- name:            Acme Billing API
- kit_version:     1.0.0
- base_url:        http://localhost:3000  (OpenAPI at /docs)
- test_cmd:        make test
- entry_kinds:     http-route, queue-consumer, scheduled-job, webhook
- entry_globs:     src/routes/**, src/consumers/**, src/cron/**, src/webhooks/**
- layer_map:       entry (src/routes) -> handlers (src/handlers) -> domain (src/domain) -> repositories (src/repos) -> clients (src/clients) -> config (src/config)
- test_globs:      tests/**/<name>.test.*
- data_kind:       collections
- config_sources:  src/config/*.ts, environment variables listed in .env.example
- behaviour_dirs:  src/handlers, src/domain, src/repos, src/consumers, src/webhooks
- domains:         accounts, invoices, payments, webhooks, ops, shared
- areas:           (none)
- unmapped:        src/routes/health.* (liveness only)
- vcs_log:         git log --since=30.days --name-only --pretty=format:
```

## D. CLI tool or library

```
- name:            acme-cli
- kit_version:     1.0.0
- base_url:        ./bin/acme --help
- test_cmd:        make test
- entry_kinds:     cli-command
- entry_globs:     cmd/**, internal/commands/**
- layer_map:       command (cmd/) -> use case (internal/app) -> adapters (internal/adapters) -> config (internal/config, ~/.acme.toml)
- test_globs:      **/*_test.*  next to the source file
- data_kind:       files
- config_sources:  internal/config, flags, environment variables
- behaviour_dirs:  cmd, internal/app, internal/adapters
- domains:         init, build, deploy, inspect, shared
- areas:           (none)
- unmapped:        (none)
- vcs_log:         git log --since=30.days --name-only --pretty=format:
```
