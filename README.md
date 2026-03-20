<div align="center">

# Jahro Agent Skills

**Your AI coding assistant becomes a Unity debugging expert.**

<picture>
  <img alt="Jahro Agent Skills" src="https://vdepoiw1jnimcohf.public.blob.vercel-storage.com/Jahro_3%201-f2rMPbdeHAvaTZaFEGHbfxIQ8hqFM0.png" width="800">
</picture>

**8 purpose-built skills that teach Claude Code, Cursor, and other AI assistants how to generate correct [Jahro](https://jahro.io/?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills) code — commands, watchers, snapshots, structured logging, production config, migration, and troubleshooting.**

[![Jahro Console](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fjahro-console%2Funity-package%2Fmain%2Fpackage.json&query=%24.version&logo=unity&label=Jahro%20Console&color=purple&logoColor=black)](https://github.com/jahro-console/unity-package)
[![Unity 2021.3+](https://img.shields.io/badge/Unity-2021.3%2B-black?logo=unity)](https://unity.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

[Getting Started](https://jahro.io/docs/getting-started?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills) •
[Website](https://jahro.io?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills) •
[Documentation](https://jahro.io/docs?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills) •
[Web Console](https://console.jahro.io?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills) •
[Discord](https://discord.gg/txcHFRDeV4)

</div>

---

## What is this?

**[Jahro](https://jahro.io/?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills)** is an in-game debugging platform for Unity — runtime commands, variable watching, log viewing, and shareable snapshot sessions, all accessible on-device without cables or ADB.

**Agent skills** are markdown files that give AI coding assistants domain-specific knowledge. Instead of your AI guessing at Jahro's API, these skills provide exact attribute syntax, registration patterns, lifecycle rules, and diagnostic decision trees.

Install the skills, then talk to your AI naturally:

- **"Add debug commands to my PlayerController"** — AI generates `[JahroCommand]` attributes with correct naming, groups, parameter types, and `RegisterObject`/`UnregisterObject` lifecycle
- **"Monitor these variables at runtime"** — AI generates `[JahroWatch]` attributes with supported types and performance-safe patterns
- **"Migrate my custom IMGUI debug menu to Jahro"** — AI analyzes your existing code and produces Jahro equivalents file by file
- **"Review the logging in my SaveManager"** — AI detects antipatterns, classifies system criticality, adds structured `[Tag] Action — key=value` logs at every boundary
- **"My commands aren't showing up"** — AI walks a diagnostic decision tree: assembly scanning → registration → method visibility → lifecycle timing

## Skills

| Skill | What Your AI Can Do |
|:------|:--------------------|
| **jahro-setup** | Detect Jahro in your project, guide installation, configure API keys, recommend which features to adopt first |
| **jahro-commands** | Generate `[JahroCommand]` attributes with correct syntax, `RegisterObject` boilerplate, group organization, and Visual/Text Mode guidance |
| **jahro-watcher** | Generate `[JahroWatch]` attributes with supported types, group strategies, and performance implications |
| **jahro-snapshots** | Configure snapshot capture modes (Recording vs Streaming), QA workflows, team sharing, and web console setup |
| **jahro-production** | Set up production safety: `JAHRO_DISABLE` define, auto-disable in release builds, lifecycle controls, and build validation |
| **jahro-logging** | Review and improve `Debug.Log` usage: structured format, context tags, severity contracts, criticality tiers, boundary-based placement, and antipattern detection |
| **jahro-troubleshooting** | Diagnose common issues using decision trees — commands missing, watcher not updating, console not opening, snapshots failing |
| **jahro-migration** | Analyze existing debug systems (IMGUI menus, custom loggers, cheat frameworks) and generate incremental migration plans |

Each skill is a self-contained SKILL.md file (~300-450 lines). Most skills are backed by shared reference files with the complete Jahro API and common code patterns. The `jahro-logging` skill is principle-driven and tool-agnostic — it works with raw `Debug.Log` and doesn't require Jahro.

## Quick Start

```bash
git clone https://github.com/jahro-console/unity-agent-skills.git .agents/skills/jahro
```

Then ask your AI: *"Help me add Jahro commands to my PlayerController"*

That's it. Claude Code discovers skills automatically. For Cursor and other assistants, see [detailed installation](#installation) below.

## Installation

### Claude Code

Clone into your project's skills directory:

```bash
git clone https://github.com/jahro-console/unity-agent-skills.git .agents/skills/jahro
```

Or clone to a shared location and symlink into each project:

```bash
git clone https://github.com/jahro-console/unity-agent-skills.git ~/jahro-agent-skills
ln -s ~/jahro-agent-skills .agents/skills/jahro
```

Claude Code automatically discovers skills in `.agents/skills/` and activates them based on each skill's description field.

<details>
<summary><b>Cursor</b></summary>

Copy skill files into Cursor's rules directory:

```bash
git clone https://github.com/jahro-console/unity-agent-skills.git /tmp/jahro-skills

# Copy each skill as a rule
for skill in /tmp/jahro-skills/skills/*/SKILL.md; do
  name=$(basename $(dirname "$skill"))
  cp "$skill" ".cursor/rules/${name}.md"
done

# Copy reference files
mkdir -p .cursor/rules/jahro-references
cp /tmp/jahro-skills/references/*.md .cursor/rules/jahro-references/

rm -rf /tmp/jahro-skills
```

In Cursor Settings, mark `jahro-setup.md` as **Always** so it provides context for all Jahro-related queries. Other skill rules can be left as **Auto**.

</details>

<details>
<summary><b>Other AI Assistants</b></summary>

Place the `skills/` and `references/` directories in your project root or any location your AI assistant scans for markdown context files. Most assistants that support project-level context will pick up the SKILL.md files automatically.

</details>

## How It Works

Each skill is a standalone SKILL.md file with:

- **YAML frontmatter** — `name` and `description` fields that tell the AI *when* to activate the skill
- **Markdown body** — concise instructions, code templates, and links to shared reference files

Skills reference two shared files in `references/`:

- **api-reference.md** — complete Jahro API: attributes, methods, events, supported types
- **common-patterns.md** — reusable code patterns (RegisterObject lifecycle, combined commands + watchers, anti-patterns)

The AI reads only what it needs: the triggered skill plus reference files on demand. Total context per query stays lean.

## Jahro at a Glance

If you're new to Jahro — here's what the skills help you set up:

**In-Game Logs** — Jahro intercepts all `Debug.Log`, `Debug.LogWarning`, and `Debug.LogError` calls automatically. Filter, search, and read stack traces directly on-device. No ADB logcat, no Xcode, no cables.

**Runtime Commands** — Add `[JahroCommand]` to any method and it becomes callable from an in-game console. Supports parameters (`int`, `float`, `bool`, `string`, `Vector2`, `Vector3`, `enum`), autocomplete, and a touch-friendly Visual Mode with generated forms.

```csharp
[JahroCommand("spawn-enemy", "Spawning", "Spawn enemies at position")]
public void SpawnEnemy(Vector3 position, int count) { /* ... */ }
```

**Variable Watcher** — Add `[JahroWatch]` to fields and properties for a real-time monitoring dashboard. No `Debug.Log` spam, no log scrolling. Values only update when the Watcher tab is open — near-zero overhead.

```csharp
[JahroWatch("Health", "Player", "Current HP")]
public float health = 100f;
```

**Shareable Snapshots** — Capture logs + screenshots + device metadata in a single session. Share via URL. Supports live streaming to the [web console](https://jahro.io/docs?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills) for real-time team collaboration.

Works on **PC, Mac, Android, and iOS**. Press `~` on desktop, triple-tap on mobile. Unity 2021.3+, zero external dependencies.

## Updating

```bash
cd .agents/skills/jahro   # or wherever you cloned
git pull
```

Skills are versioned alongside Jahro releases. See [CHANGELOG.md](CHANGELOG.md) for details.

## Links

- **Website** — [jahro.io](https://jahro.io/?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills)
- **Documentation** — [jahro.io/docs](https://jahro.io/docs?utm_source=github&utm_medium=readme&utm_campaign=unity_agent_skills)
- **Unity Package** — [github.com/jahro-console/unity-package](https://github.com/jahro-console/unity-package)
- **Discord** — [Join the community](https://discord.gg/txcHFRDeV4)

## Requirements

- **Unity**: 2021.3.0f1 or later
- **Jahro**: 1.0.0-beta6+ (skills target current stable)
- **AI Assistant**: Any assistant that supports markdown context files (Claude Code, Cursor, Windsurf, etc.)

## Contributing

Issues and PRs are welcome. If you find a case where the AI generates incorrect Jahro code with skills loaded, [open an issue](https://github.com/jahro-console/unity-agent-skills/issues) — that's exactly the kind of bug these skills are built to prevent.

## License

MIT
