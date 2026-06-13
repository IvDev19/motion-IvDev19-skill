# Architecture (Mode A)

Internal structure of the Motion monorepo. Read this when navigating or building the library source. Full source: https://github.com/IvDev19/motion-IvDev19

## Dependency flow — never import upward

```
motion-utils  →  motion-dom  →  framer-motion  →  motion
(utilities)      (DOM engine)   (React layer)     (public API)
```

| Package | Purpose |
|---|---|
| `packages/motion-utils/` | Pure utilities, easing, math — zero dependencies |
| `packages/motion-dom/` | DOM animation, scroll, gestures, effects — framework-agnostic |
| `packages/framer-motion/` | React components, hooks, full animation system |
| `packages/motion/` | Public `npm install motion` package; re-exports framer-motion (`motion/react`, `motion/mini`) |

Dev apps: `dev/react` (React 18, port 9990), `dev/react-19` (port 9991), `dev/next` (port 3000), `dev/html` (port 8000). It is a Yarn workspaces monorepo managed by Turborepo and Lerna.

## Key source directories (packages/framer-motion/src/)

- `animation/` — animators, sequences, optimized-appear
- `components/` — AnimatePresence, LayoutGroup, LazyMotion, Reorder
- `context/` — MotionContext, PresenceContext, LayoutGroupContext
- `gestures/` — drag, pan, tap, hover, focus
- `motion/` — core motion component and feature system
- `projection/` — FLIP layout-animation projection
- `render/` — HTML / SVG / DOM render pipeline
- `value/` — motion values and hooks (useMotionValue, useSpring, useScroll, useTransform)

## File navigation — read on demand, never bulk-load

| When you need… | Read |
|---|---|
| Animation API | `packages/motion/src/index.ts` |
| DOM rendering | `packages/motion-dom/src/render/` |
| React integration | `packages/framer-motion/src/` |
| Gesture handling | `packages/motion-dom/src/gestures/` |
| Value interpolation | `packages/motion-dom/src/animation/` |
| RSC compatibility | `packages/framer-motion/src/components/` |

Never bulk-load `packages/framer-motion/src/index.ts` or `packages/motion/src/index.ts` — they are barrel exports that expand the entire library tree and flood context.

## Build & dev commands — always run from the repo root

```bash
make bootstrap   # first-time setup
yarn build       # build all packages (Turborepo). Never run from inside a package directory
yarn watch       # watch mode
yarn lint        # ESLint
```

Run only one install command at a time — overlapping `make bootstrap` / `yarn install` interfere with each other.
