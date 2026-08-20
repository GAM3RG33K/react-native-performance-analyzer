# React Native Performance Analyzer

A universal AI skill that analyzes, improves, and **validates** React Native app performance — finding the *real* bottleneck, applying safe surgical fixes, and proving results in release mode.

Drop-in compatible with any AI coding tool that reads a `SKILL.md` (Claude Code, Cursor, OpenCode, Codex CLI, Cline, Copilot, Windsurf/Devin Desktop, Aider, and more).

## What it does

- **Finds** the real bottleneck — never blind optimization
- **Fixes** with small, measurable, behavior-preserving changes
- **Validates** in release builds, never judging from debug mode
- **Reports** in a consistent, client-ready markdown format

## Use cases

- 🚀 Slow app startup or cold start
- 🎢 Laggy scrolling — FlatList / SectionList / heavy list rows
- 🔁 Unnecessary re-renders and unstable props
- 🖼️ Image and asset bloat
- 🧠 State management and Context re-render storms
- 🧭 Slow navigation transitions and focus-refetch loops
- 💾 Memory leaks (listeners, timers, subscriptions)
- 📦 Large bundle size and heavy dependencies
- ⚙️ Hermes config, native modules, release-build issues

## Execution modes

| Mode | Flow | Deliverable |
|------|------|-------------|
| **1. Analyze Only** | Read-only analysis, no code changes | `PERFORMANCE_ANALYSIS.md` |
| **2. Analyze & Fix** | Analysis report → your approval → surgical fixes | Report + fixes + change summary |
| **3. Fix & Report** | Analyze → fix → final report, uninterrupted | `PERFORMANCE_ANALYSIS.md` + `PERFORMANCE_CHANGES.md` |

You keep control of *what changes*; the skill runs *how to analyze* uninterrupted.

## Usage

```
Use the React Native Performance Analyzer. Find the real bottleneck in my
app and run it in mode 2: analyze, show me the report, then fix the safe
issues after I approve.
```

See [SKILL.md](./SKILL.md) for the full workflow, the 11 analysis areas, and the report templates in [`templates/`](./templates).

## License

MIT