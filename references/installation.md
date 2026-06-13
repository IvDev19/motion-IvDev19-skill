# Installation & versioning (Mode B)

Add Motion to a downstream project. Install from the npm registry — NOT via the `github:` protocol, which pulls an unbuilt monorepo. Full library source: https://github.com/IvDev19/motion-IvDev19

## Install (pick your package manager)

```bash
pnpm add motion@12.40.0
npm install motion@12.40.0
yarn add motion@12.40.0
bun add motion@12.40.0
```

Import from `motion/react` in a React app:

```ts
import { motion, AnimatePresence } from "motion/react"
```

## Version pinning

Pin to an exact version (`12.40.0`, no `^`) so a downstream install cannot drift onto an upstream release that lacks this fork's security fixes (see `references/security-divergence.md`). Upgrade deliberately, not automatically.

## Compatibility

| Target | Support |
|---|---|
| React | `^18.0.0 \|\| ^19.0.0` |
| Next.js | 14+ App Router ✅ — no `transpilePackages` |
| Electron | ✅ (Chromium renderer) |
| TypeScript strict | ✅ — `skipLibCheck: true` in the consumer |

## Mode B onboarding protocol

Before writing integration code:

1. Confirm this is Mode B — importing Motion into an app, not editing the library.
2. Ask the user: React version, CSS framework, build tool.
3. Check those against the compatibility matrix above.
4. Propose an integration plan and wait for approval before implementing.

## Source work instead of consuming

To modify Motion itself (Mode A), do not `npm install` — clone and build:

```bash
git clone https://github.com/IvDev19/motion-IvDev19
cd motion-IvDev19 && make bootstrap && yarn build
```

See `references/architecture.md` and `references/testing.md`.

## Updating the pinned version

When this fork publishes a new version, bump the pin everywhere `12.40.0` appears in *this skill repo*: `SKILL.md`, `.cursor/rules/motion-skill.mdc`, `README.md`, and this file. That is the complete list — keep the four in sync.
