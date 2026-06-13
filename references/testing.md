# Testing (Mode A)

Write a failing test first for every bug fix and feature. A "failing test" reproduces the bug: it asserts the expected behavior and fails because of the bug, not because planned code does not exist yet. A TypeScript compile error for an API you are about to add is not a failing test.

## Pick the test layer

| Behavior | Tool | Location |
|---|---|---|
| Pure logic, math, value transforms, utilities | Jest | `__tests__/` alongside source |
| React, DOM, WAAPI, gestures, scroll, layout, anything visual | Cypress | `packages/framer-motion/cypress/integration/` |
| Vanilla JS, cross-browser | Playwright | `tests/`, pages in `dev/html/public/playwright/` |

**JSDOM has no WAAPI** (`Element.animate()`). A passing Jest test does NOT prove browser behavior. If a bug has a reproduction but your Jest test passes, escalate to Cypress — do not conclude "already works." For opacity, transform, React.lazy/Suspense, scroll, or layout bugs, start with Cypress and skip the unit test.

Some behaviors cannot be asserted in E2E (e.g. Chrome WAAPI console warnings are not catchable by a `console.warn` spy). Unit-test the underlying logic (`canAnimate`, `isAnimatable`) instead — that is the real regression gate.

## Cypress E2E setup

1. Test page: `dev/react/src/tests/<name>.tsx` exporting a named `App`. Auto-served at `?test=<name>`.
2. Spec: `packages/framer-motion/cypress/integration/<name>.ts`.

Run against **both React 18 and React 19** before any PR — CI runs both. Start Vite directly per server (turbo starts every dev server and is slow/unreliable):

```bash
# React 18
PORT=$((10000 + RANDOM % 50000))
cd dev/react && TEST_PORT=$PORT yarn vite --port $PORT &
DEV_PID=$!; npx wait-on http://localhost:$PORT
cd packages/framer-motion && cypress run --headed --config baseUrl=http://localhost:$PORT --spec cypress/integration/<name>.ts
kill $DEV_PID

# React 19 — same pattern, independent port
PORT=$((10000 + RANDOM % 50000))
cd dev/react-19 && TEST_PORT=$PORT yarn vite --port $PORT &
DEV_PID=$!; npx wait-on http://localhost:$PORT
cd packages/framer-motion && cypress run --config-file=cypress.react-19.json --config baseUrl=http://localhost:$PORT --headed --spec cypress/integration/<name>.ts
kill $DEV_PID
```

Do not set `TEST_PORT` globally via turbo — it hits both the React 18 and 19 servers and causes port conflicts. Give each its own port. If a test passes on one React version but fails on the other, investigate — do not skip it.

## Animation assertion patterns

- Use `.then()`, not `.should()`, for mid-animation snapshots. `.should()` retries until it passes, which waits out the animation and masks wrong target values.
- For target bugs: long duration + linear easing + mid-animation check. Set `transition={{ type: "tween", ease: "linear", duration: 10 }}`, wait 5s, read computed style with `.then()` — a wrong target is proportionally wrong and obvious.
- `getAnimations()` only inspects compositor properties (opacity, transform). It will not return WAAPI animations for height/width in Cypress/Electron — use computed-style checks there.
- Do not use `onUpdate` for mid-animation pixel values; for keyword targets like `"auto"` it reports the keyword. Use `getComputedStyle()`.
- Run Cypress in the foreground — background runs hang silently with empty output.

## Next-frame helper

```js
async function nextFrame() {
  return new Promise((resolve) => frame.postRender(() => resolve()))
}
```
