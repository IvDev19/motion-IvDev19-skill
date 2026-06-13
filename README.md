# motion-IvDev19-skill

A self-contained, installable personal skill for **Claude Code** and **Cursor** that carries the AI-facing engineering and integration knowledge for the Motion animation library v12.40.0 fork.

It is distilled knowledge only — no source code, no dependency tree. It works in any project without cloning the library. The full library (source, dependencies, history) lives at **https://github.com/IvDev19/motion-IvDev19**.

## What this is

Motion (formerly Framer Motion) is the animation library for React and the DOM. This skill teaches an AI agent how to:

- **Mode A** — work on Motion's own source (architecture, tests, debugging, security divergence).
- **Mode B** — add Motion to a downstream project (install, API surface, compatibility).

## Installing for Claude Code

Settings → Skills → Add from GitHub → paste:

```
https://github.com/IvDev19/motion-IvDev19-skill
```

- **Auto-loads:** `SKILL.md` — cold-start routing and always-on rules; its description keeps it ready for any React/DOM animation task.
- **Loads on demand:** the `references/*.md` files, pulled in by the agent when a task calls for them.

## Installing for Cursor

Copy into your project's `.cursor/rules/`:

```
.cursor/rules/motion-skill.mdc
```

Also copy the `references/` directory into your project so the on-demand docs resolve locally:

```
references/
```

- **Auto-loads:** `motion-skill.mdc` (`alwaysApply: true`).
- **Loads on demand:** the same `references/*.md` set as Claude Code.

If you copy only the rule file, the agent falls back to fetching references from
`https://raw.githubusercontent.com/IvDev19/motion-IvDev19-skill/main/references/`.

## Dependency install quick-reference (downstream projects)

```bash
pnpm add motion@12.40.0   # npm / yarn / bun equivalent; pin the version, install from npm
```

React 18 & 19 · Next.js 14+ App Router · Electron · TypeScript strict. Full matrix and details: [`references/installation.md`](references/installation.md).

## Relationship to motion-IvDev19

| | motion-IvDev19 | motion-IvDev19-skill (this repo) |
|---|---|---|
| Contains | Full library source + dependencies | Distilled AI guidance only |
| Size | Large (yarn.lock 557 KB, full tree) | A handful of markdown files |
| Use | Clone for source work (Mode A) | Install as a skill in any project |

## Keeping in sync

This repo is a hand-maintained distillation, so it can drift when motion-IvDev19 changes. The mapping:

| motion-IvDev19 source | Update here |
|---|---|
| `CLAUDE.md` / `.cursor/rules/motion.mdc` | `SKILL.md`, `.cursor/rules/motion-skill.mdc`, `references/{architecture,testing,debugging}.md` |
| `AGENTS.md` (modes, security, integration) | `SKILL.md`, `references/{security-divergence,installation}.md` |
| `llms.txt` (API / compatibility) | `references/api-map.md` |
| Published version (`12.40.0`) | `SKILL.md`, `.cursor/rules/motion-skill.mdc`, `README.md`, `references/installation.md` |

## License

MIT — see [LICENSE](LICENSE).
