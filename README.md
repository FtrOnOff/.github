# FtrOnOff

Feature toggle ecosystem for .NET — gate method execution from config with zero `if` statements, compile-time safety, a self-hosted management UI, and CLI audit tooling.

## The FtrIO Ecosystem

| Project | What it does | Get started |
|---|---|---|
| [FtrIO](https://github.com/FtrOnOff/FtrIO) | Core library. Decorate any method with `[Toggle]` and it becomes config-gated via IL weaving — no `if`, no boilerplate, no wrapper classes. Ships a Roslyn analyzer for compile-time validation, percentage rollouts, blue-green deployments, and dynamic providers (HTTP, Azure App Config, env vars). | `dotnet add package FtrIO` |
| [FtrIO.Toaster](https://github.com/FtrOnOff/FtrIO.Toaster) | Lightweight Docker web UI for managing toggles live. Boolean, percentage, and blue-green controls, multi-environment support, audit log, and Basic Auth / SSO via OAuth2 Proxy. | `docker compose up -d` |
| [FtrIO.onetwo](https://github.com/FtrOnOff/FtrIO.onetwo) | .NET CLI audit tool. Scans your source tree for every toggle reference, cross-references against `appsettings.json`, and reports each toggle's state — `ON`, `OFF`, `20%`, `BLUE`, or `MISSING` — with file and line number. | `ftrio-onetwo` |

## How they fit together

```
┌─────────────────────────────────────────────────────┐
│  Your code                                          │
│  [Toggle] public void SendWelcomeEmail() { ... }    │
└───────────────────┬─────────────────────────────────┘
                    │ compile-time weaving
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

No coupling between tools. Toaster and onetwo both work against the same `appsettings.json` contract — use either, both, or neither without changing your FtrIO core setup.

## Docs

Full documentation at **[ftronoff.github.io/FtrIO](https://ftronoff.github.io/FtrIO/)** — quick start, async, hot-reload, providers, strategies, multi-environment, DI, analyzer, and the full ecosystem.
