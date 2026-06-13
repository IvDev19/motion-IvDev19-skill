# Security divergence from npm

These fixes are present in this fork and **absent from the published `motion@12.40.0` npm package**. Do not revert them — re-introducing the upstream code re-opens the vulnerability. Source: https://github.com/IvDev19/motion-IvDev19

| Fix | File | Prevents |
|---|---|---|
| FIX-NEWC | `packages/motion-dom/src/effects/attr/index.ts` | Event-handler key denylist — blocks `setAttribute("onload", expr)` and similar `on*` attribute writes, an XSS vector when animating attributes on inline SVG |
| FIX-5A | `.github/workflows/claude.yml` | Third-party GitHub Actions pinned to immutable commit SHAs (not mutable tags) — a moved tag cannot inject code into CI |
| FIX-2B | `.github/workflows/claude.yml` | `id-token: write` permission removed from the workflow — CI can no longer mint OIDC tokens |

## Why each matters

- **FIX-NEWC** is library code shipped to every consumer. The `attr` effect writes attribute values during animation; without the denylist, an attribute keyed `onload` / `onclick` / etc. set via `setAttribute` becomes an executable event handler. The denylist rejects event-handler keys so an animated attribute value cannot become script.
- **FIX-5A + FIX-2B** harden the repo's own CI workflow against supply-chain and token-exfiltration attacks. They do not change library behavior, but they must survive any edit to `claude.yml`.

## When working near these

- Editing `packages/motion-dom/src/effects/attr/index.ts`: keep the event-handler key denylist intact, and add a test if you change the effect.
- Editing `.github/workflows/claude.yml`: keep the SHA pins (do not "tidy" them back to tags) and do not re-add `id-token: write`.
- Auditing this fork against upstream: these three are expected diffs, not drift to reconcile.
