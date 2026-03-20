# Changelog

## 1.1.0 — Logging Skill

### New Skill

- **jahro-logging** — Structured logging best practices for Unity C# projects: `[Tag] Action — key=value` format, severity contracts, criticality-based verbosity tiers, boundary-based log placement, antipattern detection (10 patterns), and logging infrastructure scaffolding (LogTag constants, optional helper class). Principle-driven and tool-agnostic — works with raw `Debug.Log`, mentions Jahro as optional enhancement.

### Evaluation Infrastructure

- 3 new eval scenarios (IDs 22–24) covering passive logging in new code, antipattern review, and infrastructure setup

---

## 1.0.0 — Initial Release

**Target Jahro version:** 1.0.0-beta6+

### Skills

- **jahro-setup** — Installation, API key configuration, feature overview, project detection
- **jahro-commands** — `[JahroCommand]` attribute authoring, dynamic registration, group organization
- **jahro-watcher** — `[JahroWatch]` attribute authoring, supported types, performance guidance
- **jahro-snapshots** — Snapshot mode selection, QA capture workflow, team setup
- **jahro-production** — Three-tier disable mechanism, CI/CD guidance, production checklist
- **jahro-troubleshooting** — Decision trees for commands not appearing, watcher not updating, console not opening, snapshots failing, launch button missing
- **jahro-migration** — Migration from IMGUI menus, custom loggers, cheat systems, performance HUDs

### Reference Files

- **api-reference.md** — Complete Jahro API: attributes, runtime API, supported types, configuration, lifecycle
- **common-patterns.md** — RegisterObject lifecycle, combined commands+watchers, static vs instance, dynamic registration, anti-patterns

### Evaluation Infrastructure

- 21 eval scenarios (3 per skill) in `evals/scenarios.md`
- Machine-readable eval definitions in `evals/evals.json`
- Baseline gap analysis in `evals/baseline-results.md`

### Distribution

- README with installation instructions for Claude Code, Cursor, and generic AI assistants
