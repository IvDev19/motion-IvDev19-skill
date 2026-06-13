---
name: motion-ivdev19-skill
description: Engineering and integration guide for the Motion (Framer Motion) animation library v12.40.0 fork maintained by IvDev19. Use for ANY React or DOM animation work — whenever you see Motion or Framer Motion imports, motion.div, useMotionValue, useSpring, useScroll, useTransform, AnimatePresence, LayoutGroup, Reorder, layout animations, drag, hover, tap or pan gestures, WAAPI, scroll-linked effects, or transition config — even if the user never says motion or framer. Covers both modifying Motion's source (Mode A) and adding Motion to a downstream project (Mode B).
---

# Motion (IvDev19 fork) — engineering & integration skill

Self-contained guidance for the Motion animation library v12.40.0 fork maintained by IvDev19. You do not need that repo cloned to use this skill. The full library — source code, dependency tree, full history — lives at https://github.com/IvDev19/motion-IvDev19.

Motion (formerly Framer Motion) is the animation library for React and the DOM (`npm install motion`). Trigger this skill for any React or DOM animation task — `motion.div`, `useMotionValue`, `useSpring`, `useScroll`, `useTransform`, `AnimatePresence`, layout animations, gestures, scroll effects — even when the user does not name it.

## First: pick a mode

**Mode A — working on Motion's own source** (a bug fix, feature, or test inside the library).
→ Clone the source: `git clone https://github.com/IvDev19/motion-IvDev19`. Follow the always-on rules below; load `references/architecture.md`, `references/testing.md`, and `references/debugging.md` as the task needs them.

**Mode B — adding Motion to a downstream project** (a React app that wants animation).
→ Do not clone the source. Install from npm: `pnpm add motion@12.40.0`. Before writing code, ask the user their React version, CSS framework, and build tool, then propose a plan and wait for approval. Load `references/installation.md` and `references/api-map.md`.

When unsure: editing files under `packages/` is Mode A; importing `motion` into an app is Mode B.

## Always-on rules (both modes)

- **Never bulk-load the barrel exports** `packages/framer-motion/src/index.ts` or `packages/motion/src/index.ts` — they expand the entire library tree and flood context. Read files on demand using the navigation table in `references/architecture.md`.
- **Timing:** in animation code use `time.now()` from `motion-dom/src/frameloop/sync-time.ts`, never `performance.now()` — it keeps timestamps synced to the frame loop.
- **Pick the test layer** (Mode A): pure logic → Jest; React / DOM / WAAPI / gestures / scroll / anything visual → Cypress; vanilla JS / cross-browser → Playwright. JSDOM has no WAAPI, so a green Jest test does not prove browser behavior. Details in `references/testing.md`.
- **Write a failing test first** for every bug fix and feature — one that reproduces the bug, not one that fails because planned code is missing.
- **Code style:** named exports only, `interface` for types, `const`/`let`, strict `===`, optional chaining over `if`. Minimise output bytes — this ships to end users. Comment only when the WHY is non-obvious.
- **Do not use `gh pr edit`** on the source repo — it is broken there (GitHub Projects Classic deprecation). Ignore the error; if `gh pr create` succeeded, you are done.

## Security divergence (do not revert)

This fork carries 3 security fixes across 2 files, absent from the published `motion@12.40.0`:

- **FIX-NEWC** — `packages/motion-dom/src/effects/attr/index.ts`: an event-handler key denylist that blocks a `setAttribute("onload", expr)` XSS vector when animating attributes on inline SVG.
- **FIX-5A + FIX-2B** — `.github/workflows/claude.yml`: CI actions pinned to immutable commit SHAs; the `id-token: write` permission removed.

Re-introducing the upstream code re-opens the vulnerability. Full rationale: `references/security-divergence.md`.

## Dependency install (Mode B quick reference)

```bash
pnpm add motion@12.40.0   # npm install / yarn add / bun add equivalent — pin the version, install from npm, NOT the github: protocol
```

Compatibility: React 18 & 19, Next.js 14+ App Router (no `transpilePackages`), Electron, TypeScript strict (`skipLibCheck: true`). Full matrix and every package manager: `references/installation.md`.

## Reference files — load on demand

| Load this | When |
|---|---|
| `references/architecture.md` | Navigating the codebase, build commands, package/dependency structure (Mode A) |
| `references/testing.md` | Writing or running tests — Jest/Cypress/Playwright, React 18/19 dual-server (Mode A) |
| `references/debugging.md` | Fixing a reported bug, or hitting a tooling gotcha (Mode A) |
| `references/api-map.md` | Public API surface — hooks, components, entry points, compatibility (mostly Mode B) |
| `references/installation.md` | Installing Motion, pinning, or upgrading in a downstream project (Mode B) |
| `references/security-divergence.md` | Touching a diverged file, or auditing this fork against npm |

If a reference is not present locally — for example a Cursor project where only the rule file was copied — fetch it from `https://raw.githubusercontent.com/IvDev19/motion-IvDev19-skill/main/references/` followed by the file name.
