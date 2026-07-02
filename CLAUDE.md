# CLAUDE.md

## Project

bridge — **A pocket bridge club that fits in your phone and works in a tunnel.** Real
single-player Contract Bridge, offline-first: you are South, North is your AI partner, East/West
are two AI opponents; a full deal runs auction → 13-trick play → contract scoring. Teaching-first
(bid explanations, hints, post-hand review) so a beginner with no club can actually learn.
Bridge is a public-domain game — the real name is used everywhere. Entry #3 in the b28.dev
tortoise-vs-hare series; this is the tortoise's own repo.

Read `docs/knowledge/vision.md` (scope + definition of done: "a curious beginner opens it a
second time and understands a bid they didn't before"), `docs/knowledge/charter.md` (value
function P1–P5), and `docs/knowledge/architecture.md` (the load-bearing decisions) before
designing or implementing anything.

## Stack

- **TypeScript + Svelte 5 + Vite + `vite-plugin-singlefile` + Cloudflare static.** An offline
  single-player game has no server story — no Next.js/SSR (that was RowClear), no BEAM/Fly (that
  was Consecutive), no Astro (built for content/SSG, wrong shape for a stateful game). The whole
  program is the client, and it ships as **one self-contained `dist/index.html`** (JS + CSS +
  inline-SVG card art inlined into a single emailable file). The single file is a **compile
  target, not the authoring format** — same thin artifact as the hare, compiled from tested
  modules instead of one-shot. Same artifact shape, opposite provenance.
- **Two concerns, not five packages** (see architecture.md §6): `src/core/` (pure engine + AI +
  PBN, zero DOM imports, framework-agnostic TS, property-tested with vitest — big in *tests*,
  which never ship) and `src/app/` (a thin Svelte 5 view — components + runes `$state`/
  `$derived` — input wiring, and `localStorage` persistence). `core/` never imports `app/`;
  Svelte is swappable because `core/` is framework-agnostic. Offline is a ~20-line service
  worker + manifest for reliable iOS install — no Workbox, no precache manifest.
- **PBN (Portable Bridge Notation) is the public contract.** The engine's interface is notation
  in → legal calls / legal plays / next state out. Property tests run over PBN round-trips; a bug
  report is a PBN deal.

## Architectural invariants (do not violate)

- A deal is its event record (seed + auction + play); state is always derived by folding the
  pure engine. Nothing else is authoritative.
- Undo/redo, replay, and review are folds over event-list prefixes — never a mutable board.
- The bidding AI (`record → call`) and play AI (`record → card`) are stateless peripherals,
  never woven into app code, and reason only from what their seat legitimately knows (no
  cheating on hidden hands).
- Ships as **one self-contained offline `index.html`** — the single file is a compile target,
  not the authoring format. Offline is a ~20-line service worker + manifest (reliable iOS
  install), not a precache framework; `localStorage` for progress/history/stats. No server.
- **No framework in `core/`** — the pure engine + AI + PBN is framework-agnostic TypeScript; the
  view is thin Svelte 5 in `src/app/` and is swappable without touching the rules.
- Randomness is seeded; full deals must be deterministically simulatable (AI-vs-AI doubles as
  attract mode).
- PBN is the contract. No accounts, no server, no online, no multiplayer, no matchmaking.

## Commands

The toolchain (node, pnpm, etc.) is pinned in the project's flox environment; the justfile
recipes run through it. From the repo root:

```bash
just dev      # local Vite dev server
just test     # vitest over core/ (engine property tests + unit)
just check    # svelte-check + tsc
just build    # vite build → single self-contained dist/index.html
just deploy   # Cloudflare static deploy (bridge.b28.dev)
```

If a `just` recipe is missing, run tools inside `flox activate -- <cmd>`.

### Directory Conventions

```
docs/active/tickets/    # Ticket files (markdown with YAML frontmatter)
docs/active/stories/    # Story files (same frontmatter pattern)
docs/active/work/       # Work artifacts, one subdirectory per ticket ID
docs/knowledge/         # Vision, charter, architecture — read before designing
```

---

The RDSPI workflow definition is in docs/knowledge/rdspi-workflow.md and is injected into agent context by lisa automatically.
