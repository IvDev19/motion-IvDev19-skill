# Debugging & bug-report protocol (Mode A)

## Fixing a bug from a GitHub issue

1. **Read the issue first** (`gh issue view <number>`). Do not infer the ask from code — read what is actually requested.
2. **Check git history early:** `git log --grep="<keyword>" -- <file>`. The bug may already be fixed, or a prior commit may reveal the root cause — this can save a whole session.
3. **The reporter's reproduction is the basis for your test.** If the issue links a CodeSandbox/StackBlitz, fetch it (try multiple URL patterns). If it has inline code, use that.
4. **If you cannot get the reproduction, stop and ask** — do not guess at what the reporter meant; that yields tests that prove nothing.
5. **Do not write a fix without a test that fails for the right reason.** See `references/testing.md`.
6. **Run one clean install, foreground, and wait.** Do not run `make bootstrap` / `yarn install` as overlapping background tasks.

## Strategy

- **Get to a test fast.** Trace ~5 minutes to form a theory, then write a test and experiment. Most bugs surface faster through testing than through reading — over-reading the code is the most common time sink here.
- **Use targeted `grep`/`glob`** for specific functions (`isHTMLElement`, `supportsBrowserAnimation`) over broad exploration. Two targeted searches beat one sweep.
- **Pivot fast when a theory is wrong.** If a code path is inconclusive after 2–3 rounds, step back to adjacent systems — utility functions, type guards, environment checks. The bug is often one level removed from where you expect it.
- **Think defensively, not forensically.** If a function can receive an invalid value and pass it to a browser API, guard against it — regardless of which upstream path produced it. Ask "should this value ever reach this API?" If no, add the guard and move on.
- **Capture Cypress output on the first run** (`tail -60`). Do not re-run with different greps to catch errors — the information is in the first run.
- **Avoid background-task sprawl.** Do not launch multiple background exploration tasks early — they finish after they are needed and add noise.

## Environment-specific bugs

Electron and JSDOM differ from Chrome (e.g. `offsetHeight` on SVGElement, WAAPI support, React dev-mode reconciliation). If a test passes from the start and you cannot force a local failure after 2–3 attempts: (1) web-search the environment difference; (2) if the fix is clearly correct and defensive, apply it, write a test that validates the desired behavior, and note in the PR that the failure is environment-specific. A test that cannot fail without the fix is acceptable for environment-specific bugs.

## Known tooling gotchas

- **`gh pr edit` is broken on the source repo** (GitHub Projects Classic deprecation blocks the GraphQL mutation). Expected — do not retry or work around it. If `gh pr create` succeeded and code is pushed, you are done.
- **Do not set `TEST_PORT` globally via turbo** — it causes port conflicts between the React 18 and 19 dev servers. Start each server on its own port. See `references/testing.md`.
