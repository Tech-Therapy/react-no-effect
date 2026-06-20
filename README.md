# react-no-effect

A [Claude Code](https://claude.com/claude-code) skill that teaches Claude to spot and eliminate unnecessary `useEffect` calls in React code.

## What this is

This repo contains a single **skill**: `skills/react-no-effect/`. A skill is a packaged piece of domain knowledge that Claude Code can load on demand — it's not a library you import into your app, it's instructions Claude reads before touching your React components.

The skill encodes the guidance from React's official [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect) guide, plus extra rules for projects running **React Compiler** (where manual `useMemo`/`useCallback` are no longer needed either).

## Why it helps

Most `useEffect` calls in application code are misused — they fire because a component *rendered*, not because something happened. That mismatch causes extra render passes, stale state, race conditions, and bugs that are hard to reason about.

This skill gives Claude a decision procedure instead of guesswork:

```
Does this code need to run...

Because the component was DISPLAYED?    → Effect (or useSyncExternalStore)
Because of a specific USER ACTION?        → Event handler
Can it be calculated from props/state?    → Just calculate it at render time
Does it reset state when a prop changes?  → Use the `key` prop
```

It then covers 12 concrete patterns — derived state, expensive calculations, state resets, shared event-handler logic, POST requests, chained Effects, app init, parent notification, lifting data up, external store subscriptions, and correct data fetching — each with its own before/after code example under [`references/`](skills/react-no-effect/references/), one file per rule.

Once installed, Claude will automatically consult this skill whenever:
- you write or paste code containing `useEffect`
- you ask "do I need a useEffect here?"
- it sees derived state, Effect chains, or state synced from props
- you ask about an Effect running twice, dependency arrays, or cleanup

## Installation

Skills live in a `skills/` directory that Claude Code reads from. Two ways to install:

**Project-level** (this skill applies only to one repo):

```bash
git clone https://github.com/Tech-Therapy/react-no-effect.git
cp -r react-no-effect/skills/react-no-effect <your-project>/.claude/skills/
```

**User-level** (applies to every project you work on):

```bash
git clone https://github.com/Tech-Therapy/react-no-effect.git
cp -r react-no-effect/skills/react-no-effect ~/.claude/skills/
```

**Agents-level** (for tools that read the shared `.agants/skills` convention instead of `.claude/skills`):

```bash
git clone https://github.com/Tech-Therapy/react-no-effect.git
cp -r react-no-effect/skills/react-no-effect <your-project>/.agants/skills/
```

Claude Code picks up skills under `.claude/skills/` (project), `~/.claude/skills/` (user), or `.agants/skills/` (project, shared agent convention) automatically — no restart or config change needed. You can confirm it loaded by asking Claude to list its available skills, or just paste a component with a `useEffect` and see if it flags it.

## Repo layout

```
skills/react-no-effect/
├── SKILL.md                              # the rules Claude follows
└── references/
    ├── 01-derived-state.md
    ├── 02-expensive-calculation.md
    ├── 03-state-reset-on-prop-change.md
    ├── 04-adjusting-some-state.md
    ├── 05-sharing-logic-between-handlers.md
    ├── 06-post-requests.md
    ├── 07-chains-of-effects.md
    ├── 08-app-initialization.md
    ├── 09-notifying-parent.md
    ├── 10-passing-data-up.md
    ├── 11-external-store-subscriptions.md
    └── 12-data-fetching.md               # one file per rule, full before/after code
```
