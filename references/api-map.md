# API map & compatibility (mostly Mode B)

Public surface of the Motion library, for consuming it in an app. Full source: https://github.com/IvDev19/motion-IvDev19

## Package entry points

| Import | Package | What it gives you |
|---|---|---|
| `motion` / `motion/react` | packages/motion | Public API; `motion.div` and friends, re-exported from framer-motion |
| `motion/mini` | packages/motion | Minimal animate/scroll API, smaller bundle |
| framer-motion | packages/framer-motion | Full React layer (used internally; consumers import `motion`) |
| motion-dom | packages/motion-dom | Framework-agnostic DOM engine: `animate`, `scroll`, gestures, effects |
| motion-utils | packages/motion-utils | Easing, math, shared helpers |

## Key React hooks (packages/framer-motion/src/value/)

- `useMotionValue` — a reactive value you animate imperatively
- `useSpring` — spring-driven motion value
- `useScroll` — scroll position / progress motion values
- `useTransform` — map one motion value onto another
- `useMotionValueEvent`, `useVelocity`, `useAnimate` — events, derived velocity, imperative animation handle

## Key components (packages/framer-motion/src/components/)

- `AnimatePresence` — exit animations when elements unmount
- `LayoutGroup` — shared scope for coordinated layout animations
- `Reorder` — drag-to-reorder lists
- `LazyMotion` — defer feature loading for a smaller initial bundle
- `motion.*` — the animatable primitives (`motion.div`, `motion.svg`, …)

## Gestures (packages/motion-dom/src/gestures/)

drag, pan, tap, hover, focus — surfaced as props on `motion` components (`whileHover`, `whileTap`, `drag`, …).

## Compatibility matrix

| Target | Support |
|---|---|
| React | `^18.0.0 \|\| ^19.0.0` |
| Next.js | 14+ App Router ✅ (no `transpilePackages` needed) |
| Electron | ✅ (Chromium renderer) |
| TypeScript strict | ✅ — set `skipLibCheck: true` in the consumer |

Install and version details: `references/installation.md`.
