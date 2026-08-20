---
name: react-native-performance-analyzer
title: React Native Performance Analyzer
description: Analyze, improve, and validate React Native app performance. Use when an RN app has slow startup, laggy scrolling, FlatList/SectionList issues, unnecessary re-renders, slow navigation, heavy screens, memory leaks, large bundle size, or poor Android/iOS release performance — and you need real bottlenecks found, safe fixes applied, and results validated in release mode. Supports analyze-only, analyze-and-fix, and fix-with-final-report execution modes.
version: 0.0.1
license: MIT
platform: react-native
tags: [react-native, expo, performance, flatlist, rendering, re-renders, memory-leak, hermes, bundle-size, startup, navigation, profiling, analysis, validation]
compatible_with: [claude-code, opencode, openai-codex-cli, openhands, cline, aider, pi, goose, continue, qwen-code, gemini-cli, antigravity-cli, kimi-code, cursor, devin-desktop, github-copilot, zed, jetbrains-junie, replit-agent, kiro, devin, generic-agent]
---

# React Native Performance Analyzer

**Scope:** React Native apps only (a `package.json` with `react-native`, optionally Expo — CLI or Expo). Covers JS/TS app code plus `android/` / `ios/` native projects where they affect performance. This is an **analyze-and-fix** skill, not a rewrite tool.

## When To Use

Use when an RN app has: slow startup, laggy scrolling, FlatList/SectionList issues, unnecessary re-renders, slow navigation, heavy screens, memory leaks, large bundle size, poor Android/iOS release performance, native-module bottlenecks, or performance complaints from users/clients/QA/monitoring.

The goal is **not** to blindly optimize. It is to identify the **real bottleneck**, apply **safe fixes**, and **validate the result properly**.

## Universal Compatibility

### Supported AI Tools (2026)

**Terminal / CLI agents**
- Claude Code (Anthropic)
- OpenCode (open source, most-starred CLI agent)
- OpenAI Codex CLI
- OpenHands (formerly OpenDevin)
- Cline (IDE extension + CLI + SDK)
- Aider (git-native)
- Pi (lean token-efficient harness)
- Goose (Block → Linux Foundation)
- Continue (`cn` CLI)
- Qwen Code
- Gemini CLI (retired June 2026) → Antigravity CLI (Google, closed-source successor)
- Kimi Code CLI
- Crush, Kilo Code, Forge, ZeroStack, OpenAgent, OpenClaude (newer open-source harnesses)

**IDE-embedded agents**
- Cursor (agentic IDE, acquired Continue.dev)
- Devin Desktop (formerly Windsurf)
- GitHub Copilot (VS Code / JetBrains / Xcode Agent Mode)
- Zed AI
- JetBrains AI / Junie
- Replit Agent
- Kiro (AWS, replacing Amazon Q Developer)

**Autonomous / cloud agents**
- Devin (Cognition)
- Codex Cloud (OpenAI)
- Amazon Q Developer (sunsetting 2026)

**Any agent that reads a `SKILL.md` file** — the format follows the Agent Skills open standard (agentskills.io) and the emerging Agent Client Protocol (ACP), so it loads in Claude Code, Gemini CLI, Cline, OpenCLI, Zed, JetBrains, and any other agent that reads `SKILL.md` / `AGENTS.md`.

### What Makes This Skill Truly Universal

1. **Single self-contained `SKILL.md`.** All logic, checklists, and worked examples are inline. There are **no cross-file references**, so it works even when only this one file is loaded into an agent.
2. **Standard frontmatter only.** `name`, `description`, `version`, `platform`, `tags`, `compatible_with` — the fields every agent indexes for auto-activation. The `description` is self-contained (no "see SKILL.md" tricks).
3. **No tool-specific syntax or commands.** Validation commands are plain shell (`npm`, `npx`, `./gradlew`, `pod install`) that every CLI and IDE agent can run.
4. **Environment detection first.** Step 1 detects the host agent and adapts: Cline Plan/Act mode, Claude Code plan mode + permission prompts, Codex CLI sandbox flags, Cursor Agent mode, IDE agents that show diffs for approval.
5. **File-based deliverables.** Reports are written to disk as markdown files (`PERFORMANCE_ANALYSIS.md`, `PERFORMANCE_CHANGES.md`), so outputs survive regardless of the chat UI or how the tool surfaces results. The canonical report structure lives in `templates/` **and** is inlined in this file (see Output Format), so consistent output is guaranteed even when only `SKILL.md` is loaded.
6. **Execution-mode selection** (below) is presented as plain numbered options — understood identically by every agent.
7. **Plan-before-act workflow.** Fits Cline's Plan/Act, Claude Code's plan mode, Cursor's agent loop, and Copilot's review flow — the skill never assumes a specific permission model.

## Execution Modes — Always Select First

Before reading any code, ask the user to pick one of three execution modes. This decides what the skill does and what the final deliverable is.

| Mode | Flow | User intervention | Final deliverable |
|------|------|-------------------|-------------------|
| **1. Analyze Only** | Read → inspect → profile → reason. **No code changes.** | None during the run | `PERFORMANCE_ANALYSIS.md` — a well-structured markdown report |
| **2. Analyze & Fix** | Full analysis → analysis report → **user approves the fix plan** → surgical fixes applied | One approval gate after the analysis | Analysis report + applied fixes + short change summary |
| **3. Fix & Report (full autonomy)** | Analysis → fixes → final report of every change, end-to-end **without interruption** | Permission granted up front; only blocking questions | Analysis report + fixes + `PERFORMANCE_CHANGES.md` final report |

### Mode selection prompt

Ask exactly:

> Run in which mode?
> 1. **Analyze Only** — produce a markdown analysis report, change nothing.
> 2. **Analyze & Fix** — analysis report first, then you approve the fix plan before any edits.
> 3. **Fix & Report** — analysis, fixes, and a final report of changes, uninterrupted.

Map the response:
- "1" / "analyze only" / "just analyze" → **Analyze Only**
- "2" / "analyze and fix" → **Analyze & Fix**
- "3" / "fix and report" / "fix it" / "full autonomy" → **Fix & Report**

**Default recommendation:** Mode 2. If the user does not answer, default to Mode 2 (analysis + plan approval before edits). Never silently skip the mode question — but never let it block the analysis phase (see Permission Contract).

### Permission Contract — control without interruption

The user keeps control over the skill's **decisions**, but the skill's **internal tasks** are never interrupted. This is how:

- **Internal tasks never prompt.** Reading files, searching, inspecting screens/components, running read-only checks (typecheck, lint, tests, bundle-size, config inspection), and writing report files are internal — always run uninterrupted.
- **Validation builds are attempted, then recorded.** Build/install commands (`./gradlew assembleRelease`, `pod install`) may raise a permission prompt in the host agent. Attempt each once; if rejected, record **"permission denied"** in the report's Validation section and continue — never silently omit the check.
- **Approval points exist only at mode boundaries:**
  1. Selecting the execution mode (start).
  2. Approving the fix plan after the analysis report (Mode 2).
  3. Granting a one-time permission bypass up front (Mode 3).
- **Mode 1 (Analyze Only):** always read-only. No permission bypass is ever requested — it simply cannot modify code.
- **Mode 2 (Analyze & Fix):** run the whole analysis without pausing. Deliver the analysis report, then present the fix plan and ask the user to approve it — either file-by-file or as a **scope-limited permission bypass for exactly the listed files**. Once approved, execute all fixes in one uninterrupted batch.
- **Mode 3 (Fix & Report):** at the start, ask the user to grant a permission bypass (approve-all for this task) so analysis + fixes + reporting run end-to-end without pausing. Clearly state the scope and risk first. **If the user declines, degrade to Mode 2** — the skill never edits without the user retaining an approval point.
- **The bypass decision depends on the analysis report.** When the user is unsure, the skill offers: "Run Mode 2 — read the analysis report, then decide how much permission to grant for the fixes." This keeps the user in control of *what changes* while the skill runs *how to analyze* uninterrupted.

## Core Behavior Contract (non-negotiable)

1. **Find the real bottleneck — do not blindly optimize.** Every fix must trace to a concrete named cause (heavy/inline `renderItem`, unstable prop, missing cleanup, blocking startup call). Do not sprinkle `useMemo`/`useCallback`/`React.memo` "just in case." Premature memoization is a defect, not a fix.
2. **Validate by RUNNING commands, not by listing them.** A change that "looks faster" is not proof. During the validation phase, attempt every applicable check (typecheck, lint, tests, bundle size, release build, profiler trace) so the report is complete on delivery. Record each outcome — ✅ pass, ❌ fail, ⛔ permission denied, or ⏭ unavailable. If a check is permission-rejected or the environment can't run it, state that in the report — never silently skip it and never defer it to "later." Never judge final performance from a debug build.
3. **Always separate debug-mode from release-mode performance.** Debug JS is dramatically slower (no Hermes optimizations, dev warnings, remote debugging). Never judge final performance from a debug build. Label every observation debug/release; prefer release builds for final verdicts. Turn OFF remote JS debugging when measuring.
4. **Make surgical edits only.** Touch the minimum needed. No refactors, reformatting, dependency churn, or architecture changes "while you're in there." Follow the project's existing patterns (TS vs JS, state library, navigation library, list component).
5. **Protect the project.** Do not remove business logic, change app behavior, break Android/iOS builds, touch signing configs, or change package name / bundle identifier. Do not remove existing analytics, crash reporting, or monitoring without explicit approval.
6. **Be careful and honest with dependencies and Hermes.** Do not add heavy libraries (e.g. FlashList, an image cache, a state library) without a measured reason — and explain the trade-off. Do not blindly enable/disable Hermes; check the RN version and platform support first.
7. **Prefer small, measurable wins over risky rewrites.** A stabilized `renderItem` + `keyExtractor` + list-window tuning beats "rewrite the screen." Stage changes so each can be measured and reverted independently.

## Input Requirements

Ask for whatever's available (RN version, Expo SDK, platforms, main problem, device, debug vs release, affected screens, package manager, state lib, navigation lib, list components, native modules, screenshot/video), then **proceed with best-practice assumptions — ask only blocking questions.**

## Files To Inspect Before Editing

```
package.json
yarn.lock | package-lock.json | pnpm-lock.yaml | bun.lockb
app.json | app.config.js
index.js | index.ts
App.js | App.tsx
metro.config.js
babel.config.js
tsconfig.json
src/navigation/*  src/screens/* (focus on the reported slow screen)
src/components/* (focus on heavy / list-row components)
src/store/*  src/hooks/*  src/services/*
android/app/build.gradle  android/gradle.properties  ios/Podfile
```

## Execution Workflow / Decision Order

0. **Select execution mode** (Analyze Only / Analyze & Fix / Fix & Report) and agree the permission contract. Do not read code yet.
1. **Understand the problem** — which screen, which action, which platform, debug vs release, low-end device or not.
2. **Detect the project** — RN version, Expo SDK, package manager, state library, navigation library, list components, native modules. Also detect the host agent and adapt (plan mode, approval flow, sandbox flags).
3. **Locate / reproduce the bottleneck** — categorize: startup | rendering | lists | images | state | navigation | animations | memory | native module | bundle size | release-build issue.
4. **Branch on execution mode:**
   - **Mode 1:** produce the analysis report file → done. No edits.
   - **Mode 2:** produce the analysis report → present the fix plan → **await approval** → apply fixes → short change summary.
   - **Mode 3:** produce the analysis report (internal) → apply fixes → write `PERFORMANCE_CHANGES.md` final report. No pause between these.
5. **Apply focused, safe fixes** (modes 2 and 3) that match project conventions and preserve behavior.
6. **Validate by running commands** — typecheck + lint + tests + bundle size + release build + profiler trace. Attempt every applicable command during the run and record each result (✅ pass / ❌ fail / ⛔ permission denied / ⏭ unavailable) in the report's Validation section. See "Validation Commands — Run, Don't List." A report with unrun checks is not complete.
7. **Report** according to the mode's deliverable (see Output Format below) — client-ready.

**Agents without shell access:** produce exact edits + copy-paste validation/build commands, and mark results **"needs user verification."**

---

## The 11 Analysis Areas (with checks and fixes)

### 1. Startup Performance
- [ ] No heavy synchronous logic in `App.tsx`
- [ ] No blocking startup API calls before first render
- [ ] No synchronous storage reads on the critical path
- [ ] Splash not held longer than needed
- [ ] Root providers are lean (not re-rendering the whole tree)
- [ ] Analytics / background services deferred after first render
- [ ] No `console.log` in production; debug-only tools excluded from release

**Fix:** Lazy-load screens; move non-critical work after first render (`InteractionManager.runAfterInteractions`); defer analytics/preload; reduce root-provider complexity; remove unused imports; test in release.

### 2. Rendering Performance
- [ ] No unnecessary re-renders (verify with profiler / why-did-you-render)
- [ ] Heavy children receive stable object/array/function props
- [ ] No inline functions passed into heavy lists
- [ ] `React.memo` used only where it measurably helps
- [ ] `useMemo` / `useCallback` used correctly, not everywhere
- [ ] Expensive calculations moved out of render
- [ ] Large components split where it reduces re-render scope
- [ ] Context not used for frequently-changing state without selectors

**Fix:** Memoize only when useful; keep props stable for heavy children; move expensive calculations out of render; use selectors for global state.

### 3. FlatList / SectionList Performance
- [ ] FlatList (NOT `ScrollView` + `.map()`) used for large or unbounded data
- [ ] Stable, unique `keyExtractor` (not array index for dynamic data)
- [ ] `renderItem` is a **stable reference** (defined outside render or `useCallback`), not inline
- [ ] Row component wrapped in `React.memo`; receives **stable props** (no new object/array/function literals per render)
- [ ] No per-row inline arrow functions — pass `item.id` + a stable handler
- [ ] `getItemLayout` provided for fixed-height rows (skips measurement)
- [ ] `initialNumToRender` ≈ one screenful; `maxToRenderPerBatch` tuned (lower for heavy rows)
- [ ] `windowSize` tuned (default 21; lower = less memory, higher = fewer blank cells); `updateCellsBatchingPeriod` tuned if batching janks
- [ ] `removeClippedSubviews` considered (Android especially) — verify it doesn't blank rows
- [ ] Pagination / infinite loading via `onEndReached` (+ sensible threshold)
- [ ] No nested vertical FlatLists (flatten or use SectionList); no same-direction ScrollView wrapping a FlatList
- [ ] No `setState` on every scroll event (throttle / `scrollEventThrottle` / move to Reanimated)
- [ ] Thumbnails (not full-res images) in rows; fixed image dimensions

**Fix:** Stable keys + memoized rows + `getItemLayout` + window tuning + pagination + thumbnails. **FlashList only after measuring FlatList as the bottleneck in release** — then explain API differences, `estimatedItemSize`, and the extra dependency, and record the measurement that justified it.

### 4. Images and Assets
- [ ] Local images sized for actual display; remote images resized/thumbnailed for lists
- [ ] Caching strategy in place where needed
- [ ] PNGs compressed / WebP used where supported; no large base64 images; no heavy SVGs
- [ ] Below-the-fold images lazy-loaded

**Fix:** Resize for display size; use WebP; thumbnails for lists; caching lib only if needed; lazy-load below the fold.

### 5. State Management
- [ ] Selectors used (components don't subscribe to too much state)
- [ ] No unnecessary global state; no storing derived data unnecessarily
- [ ] Derived data memoized, not recomputed every render
- [ ] Server state in a cache lib (React Query/Apollo), not manual global state
- [ ] Large datasets normalized

**Fix:** Use selectors; keep server state separate from UI state; memoize derived data carefully; normalize large datasets.

### 6. Navigation Performance
- [ ] No heavy logic on screen mount; focus effects don't refire API calls unnecessarily
- [ ] Headers are not expensive; large objects not passed through route params
- [ ] Expensive screens lazy-loaded where possible
- [ ] Navigation listeners cleaned up (`navigation.addListener` / `useFocusEffect` cleanup)

**Fix:** Move heavy work after the transition; use focus effects carefully; lazy-load expensive screens; clean up listeners.

### 7. Animations and Gestures
- [ ] Animations native-driven where possible (no JS-thread animation)
- [ ] Reanimated worklets used for complex gestures/animations
- [ ] No state updates on every frame; heavy JS work kept off the animation path
- [ ] Gesture handlers not causing re-renders; layout animations not causing jank

**Fix:** Use native-driven animations; Reanimated worklets for complex gestures; avoid per-frame state updates.

### 8. Memory Leaks
- [ ] Every `useEffect` that subscribes/opens/starts something returns cleanup; matched add/remove pairs; correct dependency arrays
- [ ] Event listeners removed (`AppState`, `Dimensions`, `Keyboard`, `Linking`, `BackHandler`, DeviceEventEmitter / NativeEventEmitter)
- [ ] Timers / intervals cleared (`clearTimeout` / `clearInterval` / `cancelAnimationFrame`); `InteractionManager.runAfterInteractions` cancellation handled
- [ ] Subscriptions closed: WebSocket, Firebase (`onSnapshot`, `on`, `onAuthStateChanged`, RTDB), Redux/Zustand/RxJS, push/messaging listeners
- [ ] Navigation listeners: `addListener` return value unsubscribed; `useFocusEffect` cleanup returned; no re-register-per-focus without cleanup
- [ ] Async requests aborted (`AbortController`) or `isMounted`-guarded before `setState` after `await`; promises don't retain large objects
- [ ] Media released (video/audio `release()` / pause+unload; camera/BLE/location stopped); large images/blobs and arrays not retained after unmount

**Fix:** Proper cleanup in `useEffect`; abort fetches; unsubscribe; clear timers; release media resources.

### 9. Native Modules
- [ ] Native-heavy features reviewed (camera, maps, video, audio, BLE, payments, Firebase, push, location, ML)
- [ ] No heavy work on the main thread blocking navigation/render
- [ ] No obvious memory pressure from native resources

**Fix:** Identify modules running heavy work on the main thread or causing memory pressure; move/offload where possible.

### 10. Hermes and JavaScript Loading
- [ ] Hermes status checked (NOT blindly toggled — check RN version + platform support)
- [ ] Bundle size reviewed; large/unused dependencies flagged
- [ ] Dynamic imports used where useful
- [ ] Release build behavior considered

### 11. Release Build Validation
**Never judge performance from a debug build.** Debug runs without Hermes optimizations, with dev warnings, and sometimes over a remote debugger — it can be many times slower.

```bash
# Android release
cd android && ./gradlew clean && ./gradlew assembleRelease   # APK
cd android && ./gradlew bundleRelease                         # AAB for Play
# iOS release
cd ios && pod install
# then: Xcode → Product → Scheme → Edit Scheme → Run → Release (or archive with Release config)
```

- [ ] Release APK/AAB builds; app launched from release and the slow scenario **re-measured**
- [ ] Tested (or flagged) on a **low-end** device, not just a flagship
- [ ] `pod install` succeeds; app built/run with the **Release** scheme
- [ ] Bundle size reviewed (existing tooling; add an analyzer only if useful); dynamic imports where they meaningfully shrink the initial bundle
- [ ] Profiler trace captured: React Native DevTools Profiler (re-renders/commit times), Hermes profiler (JS CPU), Android Studio Profiler (CPU/memory), Xcode Instruments (Time Profiler/Allocations/Leaks), Flipper/Sentry (only if already integrated)
- [ ] Before/after measurements captured for the fixed scenario (release where possible)

---

## Validation Commands — Run, Don't List

Validation is part of the analysis, not a follow-up. **Attempt every applicable command during the run** so the report ships complete. Never write "unverified until run" and defer it — run it now, or record the exact reason it could not run.

```bash
# Read-only checks — always run, run first (covered by the permission contract)
npm run typecheck                            # or: tsc --noEmit
npm run lint
npm test                                     # or the repo's test command, e.g. ENVFILE=.env.test npx jest

# Bundle size — read-only, always run
npx react-native bundle --entry-file index.js --platform ios --dev false --bundle-output /tmp/{app}_bundle.js
ls -lh /tmp/{app}_bundle.js                  # record the size

# Android release build — attempt; may be permission-gated
cd android && ./gradlew assembleRelease

# iOS — attempt; requires Xcode, may be permission-gated
cd ios && pod install                        # then build with the Release scheme

# Config confirmation — read-only, always run
# android/gradle.properties -> hermesEnabled / newArchEnabled
# ios/Podfile -> :hermes_enabled / :new_arch_enabled

# Profiler trace — where available (may need a device/simulator)
# React Native DevTools Profiler (re-renders / commit times)
# Hermes profiler (JS CPU), Android Studio Profiler (CPU/memory)
# Xcode Instruments (Time Profiler / Allocations / Leaks)
```

**Recording results — exactly one outcome per check:**

- **✅ pass** — ran clean.
- **❌ fail** — ran with errors; paste the key error into the Result cell.
- **⛔ permission denied** — the user / host agent rejected the command; write "permission denied" in the Result cell — never silently omit it.
- **⏭ unavailable** — toolchain missing (no Android SDK / Xcode / pod) or the repo lacks the script; state the reason.

**Rules:**
1. Read-only checks (typecheck, lint, tests, bundle size, config confirmation) run inside the permission contract — do not pause for them.
2. Build/install commands (gradle, pod install) may raise a permission prompt. Attempt each once; if rejected, record **⛔ permission denied** and move on — do not retry in a loop.
3. If the environment cannot run a check, record **⏭ unavailable** with the reason.
4. Compute the **Report Coverage** line from these results: **100% complete** when every applicable check has a recorded outcome, otherwise **~X% complete** with the pending items listed.

---

## Rules and Constraints (Do Not Violate)

- Do **not** optimize without naming the bottleneck first.
- Do **not** overuse `useMemo` / `useCallback` / `React.memo`, or add them without a measured reason.
- Do **not** swap FlatList → FlashList (or add any heavy library) without explaining why and measuring the bottleneck first.
- Do **not** blindly enable/disable Hermes — check RN version and platform support.
- Do **not** claim performance is fixed without testing; do **not** judge final performance from a debug build.
- Do **not** remove business logic or change app behavior without permission.
- Do **not** break Android or iOS builds, change signing configs, or modify package name / bundle identifier.
- Do **not** remove existing analytics, crash reporting, or monitoring without approval.
- Do **not** edit code in Mode 1 (Analyze Only) — analysis is read-only, always.
- Do **not** pause the analysis phase for permission prompts — internal tasks run uninterrupted.
- Do **proceed** with documented best-practice assumptions when info is sufficient; ask only blocking questions.

## Output Format (per mode)

**Canonical templates.** Every report MUST be generated from the canonical templates shipped with this skill, so the structured output stays identical for every operation and every app, regardless of host tool:

- `templates/PERFORMANCE_ANALYSIS.template.md` → output filename `PERFORMANCE_ANALYSIS.md`
- `templates/PERFORMANCE_CHANGES.template.md` → output filename `PERFORMANCE_CHANGES.md`

**Rules when generating a report:**
1. Copy the matching template verbatim; fill every `{placeholder}`; keep section headings, order, and the header block EXACTLY as in the template.
2. Write the file to the project root (or the path the user specifies), never only to chat.
3. If the template files are not present, reproduce the identical structure inline from the section lists below — never invent a new layout.
4. Fill all fields (project, RN version, Expo SDK, platforms, scope, build mode, date, mode statement) in the header block on every report.
5. Keep the `Issues Found` / `Files Changed` / `Validation` tables in the template's shape — one row per issue/file/check, severity always Critical/High/Medium/Low, and every issue traced to `file:line`.
6. Attempt every applicable validation command during the run; record each outcome (✅ pass / ❌ fail / ⛔ permission denied / ⏭ unavailable). A check that did not run is still listed, with the exact command and the real reason — never silently omitted.
7. Include the **Report Coverage** line in the header block: completion percentage (100% or X%) plus exactly which checks are still pending and why. This tells the reader at a glance whether the report is complete or partial.

### Mode 1 — Analyze Only: `PERFORMANCE_ANALYSIS.md`

Generate from `PERFORMANCE_ANALYSIS.template.md`. Required sections (in template order):

  1. **Header block** — mode, project, RN/Expo versions, platforms, scope, build mode, date, mode statement, and **Report Coverage** (completion % + pending items).
2. **Summary** — what was reviewed, which screens/actions, debug vs release.
3. **Issues Found** — table, each with severity (Critical/High/Medium/Low), traced to specific code.
4. **Recommended Fixes** — per issue: surgical fix, files affected, risk (do not apply in this mode).
5. **Validation Notes** — every applicable command attempted; each result recorded (✅ pass / ❌ fail / ⛔ permission denied / ⏭ unavailable); unrun checks listed with the exact command and the reason.
6. **Manual Testing Steps** — how to reproduce/confirm on device.
7. **Remaining Risks** — real-device/production items.
8. **Next Steps** — what to do next (including "run Mode 2/3 with this report").

Mode statement (must be the first line of the body): **"Mode: Analyze Only — no code was changed."**

### Mode 2 — Analyze & Fix: report → approval → fixes

1. Produce `PERFORMANCE_ANALYSIS.md` from the template exactly as in Mode 1.
2. Present the **Fix Plan** (issues, files, strategy, tests, risk) and ask the user to approve it or grant a scope-limited bypass.
3. After approval, apply surgical fixes.
4. Deliver a **short change summary** in chat (files changed + why). Optionally extend `PERFORMANCE_ANALYSIS.md` with an **Fixes Applied** section matching the `PERFORMANCE_CHANGES.md` template's shape.

### Mode 3 — Fix & Report (full autonomy): `PERFORMANCE_CHANGES.md`

End-to-end without pausing. Generate BOTH files from the templates:

- `PERFORMANCE_ANALYSIS.md` — from `PERFORMANCE_ANALYSIS.template.md` (as Mode 1).
- `PERFORMANCE_CHANGES.md` — from `PERFORMANCE_CHANGES.template.md`, required sections (in template order):
1. **Header block** — mode, project, RN/Expo versions, platforms, scope, build mode, date, mode statement, and **Report Coverage** (completion % + pending items).
  2. **Summary** — what was analyzed and changed.
  3. **Issues Found** — table, severity, traced to code.
  4. **Files Changed** — table: each file and why.
  5. **Fixes Applied** — one block per issue, tied back to the Issues Found table by number.
  6. **Validation** — every applicable command attempted and its result recorded (✅ pass / ❌ fail / ⛔ permission denied / ⏭ unavailable); unrun checks listed with the exact command and reason.
  7. **Manual Testing Steps** — steps to test the affected screens.
  8. **Remaining Risks** — anything needing real-device testing or production monitoring.
  9. **Next Steps** — recommendations.

Mode statement (must be the first line of the body): **"Mode: Fix & Report — changes were applied per the approved permission grant."**

## Definition of Done

Complete only when:
- The reported bottleneck is identified and traced to specific code, **and**
- The selected mode's deliverable is produced (report file for Mode 1; report + approved fixes for Mode 2; report + fixes + final report for Mode 3), **and**
- Fixes (modes 2/3) are surgical, match project conventions, and preserve behavior, **and**
- Every applicable validation command was attempted and its result recorded (✅ pass / ❌ fail / ⛔ permission denied / ⏭ unavailable), **and**
- The report is client-ready, states the mode explicitly, and includes the **Report Coverage** line (completion % + pending items).

---

## Worked Example: FlatList Analysis (condensed)

**Symptom:** Feed stutters on scroll, worse on low-end Android, reproduces in release.

**Before (problem code):**
```tsx
<FlatList
  data={posts}
  renderItem={({ item }) => (            // ❌ inline -> new fn every render
    <FeedCard post={item} liked={!!liked[item.id]}
      onPress={() => setLiked(s => ({ ...s, [item.id]: !s[item.id] }))} />  // ❌ fresh closure/row
  )}
  keyExtractor={(_, index) => String(index)}  // ❌ index key on a paginated list
/>
```
```tsx
// FeedCard.tsx — NOT memoized, full-res image in row
<Image source={{ uri: post.imageUrl }} style={styles.image} />
```

**After (fixed code):**
```tsx
const CARD_HEIGHT = 280;
const toggleLike = useCallback((id: string) =>
  setLiked(s => ({ ...s, [id]: !s[id] })), []);            // ✅ stable handler
const renderItem = useCallback(({ item }) => (
  <FeedCard post={item} liked={!!liked[item.id]} onToggleLike={toggleLike} />),
  [liked, toggleLike]);                                    // ✅ stable renderItem
const keyExtractor = useCallback((item) => item.id, []);   // ✅ stable unique keys
const getItemLayout = useCallback((_, index) =>
  ({ length: CARD_HEIGHT, offset: CARD_HEIGHT * index, index }), []);  // ✅ fixed height

<FlatList data={posts} renderItem={renderItem} keyExtractor={keyExtractor}
  getItemLayout={getItemLayout}
  initialNumToRender={6} maxToRenderPerBatch={6} windowSize={9} removeClippedSubviews />
```
```tsx
export const FeedCard = React.memo(function FeedCard({ post, liked, onToggleLike }) {
  // ✅ thumbnail sized for the row, not the original
  return <Image source={{ uri: post.thumbnailUrl ?? post.imageUrl }} style={styles.image} />;
});
```

**Why each change is safe:** identical behavior; `getItemLayout` only because height is fixed; no new dependency (FlashList NOT introduced — FlatList is fine once rows stop re-rendering and images are thumbnailed). Validate: `tsc --noEmit` + `lint` pass; release APK rebuilt and scroll re-measured on the low-end device (record profiler numbers before/after).

**Startup pattern (same logic):** blocking `await api.get('/me')` + `analytics.init()` + `preloadEverything()` before first render → move to `InteractionManager.runAfterInteractions`, fetch profile in the screen that needs it, feature flags load with safe defaults, heavy screens via `React.lazy()`. Same data still loads — just off the cold-start critical path.

## Example Prompts

- **Full analysis (Mode 1):** "Analyze this RN app for performance. Detect RN version, package manager, state/navigation libs, list components, native modules. Locate the real bottleneck, categorize it, and produce a well-structured `PERFORMANCE_ANALYSIS.md` report with issues + severity, recommended fixes, validation commands, and manual test steps. Do not change any code."
- **Analyze & fix (Mode 2):** "Analyze this RN app for performance. Produce the analysis report, present a fix plan, wait for my approval, then apply only safe surgical fixes that preserve behavior. Validate with typecheck, lint, tests, and a release build where possible."
- **Fix & report (Mode 3):** "Run the full RN performance workflow without interrupting me: analyze, apply safe fixes, and deliver `PERFORMANCE_ANALYSIS.md` plus `PERFORMANCE_CHANGES.md` (files changed, fixes applied, validation, manual test steps, risks, next steps). I grant a permission bypass for this task."
- **Slow list:** "The `<Screen>` FlatList stutters on scroll (low-end Android, reproduces in release). Stabilize `renderItem`/`keyExtractor`, memoize the row, use thumbnails, tune list windowing. Only suggest FlashList if FlatList is still the bottleneck after measuring."
- **Slow startup:** "Cold start is slow. Analyze `App.tsx`, root providers, startup API calls, storage reads, and `RootNavigator`. Defer non-critical work, keep behavior identical, validate in a RELEASE build, report before/after."
- **Memory leak:** "Memory grows as I navigate. Analyze `useEffect` cleanup, listeners, timers, subscriptions (WebSocket/Firebase/navigation), async requests, and media resources. Fix leaks, validate, and report."

## Final Validation Checklist

- [ ] Execution mode selected and permission contract agreed (before reading code)
- [ ] Project structure reviewed; RN version checked; Expo version checked if applicable
- [ ] Navigation, heavy screens, FlatList/SectionList, images, state management, native modules, and memory-leak risks reviewed
- [ ] Hermes config checked (not blindly toggled); debug vs release mode considered
- [ ] Every applicable validation command attempted: typecheck + lint + tests + bundle size + release build + profiler; each result recorded (✅ pass / ❌ fail / ⛔ permission denied / ⏭ unavailable)
- [ ] Android build attempted where possible; iOS build attempted where possible
- [ ] Fix plan presented before editing (Mode 2); edits surgical and behavior-preserving
- [ ] No edits made in Mode 1; mode stated explicitly in the deliverable
- [ ] **Report Coverage** line at the top of the report: completion % + pending items
- [ ] Manual testing steps written; remaining risks listed
- [ ] Final report is client-ready (Summary, Issues+severity, Files Changed, Fixes, Validation, Manual Steps, Risks, Next Steps)
