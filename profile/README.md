# FtrOnOff

Feature toggle ecosystem for .NET — gate method execution from config with zero `if` statements, compile-time safety, a self-hosted management UI, CLI audit tooling, and GitHub Actions for deployment safety.

## The FtrIO Ecosystem

| Project | What it does | Get started |
|---|---|---|
| [FtrIO](https://github.com/FtrOnOff/FtrIO) | Core library. Decorate any method with `[Toggle]` and it becomes config-gated via IL weaving — no `if`, no boilerplate, no wrapper classes. Ships a Roslyn analyzer for compile-time validation, percentage rollouts, blue-green deployments, and dynamic providers (HTTP, Azure App Config, env vars). | `dotnet add package FtrIO` |
| [FtrIO.Toaster](https://github.com/FtrOnOff/FtrIO.Toaster) | Lightweight Docker web UI for managing toggles live. Boolean, percentage, and blue-green controls, multi-environment support, audit log, and Basic Auth / SSO via OAuth2 Proxy. | `docker compose up -d` |
| [FtrIO.onetwo](https://github.com/FtrOnOff/FtrIO.onetwo) | .NET CLI audit tool. Scans your source tree for every toggle reference, cross-references against `appsettings.json`, and reports each toggle's state — `ON`, `OFF`, `20%`, `BLUE`, or `MISSING` — with file and line number. Also supports importing flag state from LaunchDarkly, Flagsmith, and other providers, and generating migration reports. | `ftrio.onetwo` |

## GitHub Actions

Two actions that together gate deployments on missing toggle config — catching the gap between what your code needs and what a specific environment actually has configured.

| Action | What it does | Use in |
|---|---|---|
| [export-manifest-action](https://github.com/FtrOnOff/export-manifest-action) | Scan source code for `[Toggle]` usage and export a manifest of required toggle keys. | CI pipeline |
| [release-check-action](https://github.com/FtrOnOff/release-check-action) | Validate a target `appsettings.json` contains every toggle key required by this release. Blocks the deploy if anything is missing. | Deployment pipeline |

## How they fit together

```
┌─────────────────────────────────────────────────────┐
│  Your code                                          │
│  [Toggle] public void SendWelcomeEmail() { ... }    │
└───────────────────┬─────────────────────────────────┘
                    │ compile-time weaving (Roslyn analyzer)
                    ▼
┌─────────────────────────────────────────────────────┐
│  FtrIO core                                         │
│  gates method execution at runtime                  │
└───────────────────┬─────────────────────────────────┘
                    │ reads
                    ▼
┌─────────────────────────────────────────────────────┐
│  appsettings.json  — source of truth                │
└──────────┬──────────────────────────┬───────────────┘
           │ writes live              │ reads & audits
           ▼                          ▼
  FtrIO.Toaster                 FtrIO.onetwo
  (web UI — manage toggles)     (CLI — audit state)
```

### Deployment safety pipeline

```
CI pipeline                          Deployment pipeline
────────────────────────────────     ──────────────────────────────────
export-manifest-action           →   release-check-action
Scan source for [Toggle] keys         Download manifest artifact
Upload as build artifact              Fetch target appsettings.json
                                      Block deploy if any keys missing
```

No coupling between tools. Use any combination without changing your FtrIO core setup.

## The safety net

FtrIO catches toggle config drift at every stage of the pipeline:

| Stage | What catches it |
|---|---|
| Write `[Toggle]` without config entry | Roslyn analyzer — compile time |
| Release with key missing from target env | `release-check-action` — deploy time |

## Docs

Full documentation at **[ftronoff.github.io/FtrIO](https://ftronoff.github.io/FtrIO/)** — quick start, async, hot-reload, providers, strategies, multi-environment, DI, analyzer, and the full ecosystem.
tronoff.github.io/FtrIO](https://ftronoff.github.io/FtrIO/)** — quick start, async, hot-reload, providers, strategies, multi-environment, DI, analyzer, and the full ecosystem.
