# Bridge — Architecture

The architectural model is **a chess engine's discipline, aimed at a single learner offline**.
Bridge is a large program — it did not fit in the hare's one response — so the load-bearing
decisions are the ones that let a many-shot pipeline build it in pieces and keep it honest.
There is no server here: an offline single-player game has no multiplayer story to hold up.
These decisions are language-agnostic — the notation contract (below) is what keeps them that
way.

## 1. A deal is a record, not a session (the keystone)

A deal *is* its event list: a seed (the deck order / deal id) plus the auction (the ordered
calls) plus the play (the ordered cards). Game state is always derived by folding the pure
engine over the events. Nothing else is authoritative. Consequences, all free:

- **Resume does not exist as a feature.** Reopening = reload the record, re-derive the state.
  There is no session to lose in a tunnel.
- **Undo/redo is a fold over a prefix.** As a learning aid it is nearly free: truncate the
  event list and re-derive. No mutable board to unwind.
- **Replay and post-hand review** are folds over auction/play prefixes — step the deal forward
  one call or one card at a time.
- **Deals are permanent local documents.** A year of nightly practice deals is kilobytes.
  Stats and hand history are aggregations over the archive.

## 2. PBN — the notation contract

The public contract is **PBN (Portable Bridge Notation)** — a real, standard, human-readable
bridge record format: the deal, the auction, the play, the contract and result. It is the
interface between every part of the system:

- The engine's interface is *notation in → legal calls / legal plays / next state out*.
- Property tests run over PBN round-trips (parse → re-emit → parse is stable).
- Persistence can be as boring as append-only PBN text.
- **A bug report is a PBN deal.** Reproduce any defect by handing the engine the deal file.
- The shell (view framework, platform) is swappable around it.

## 3. The engine is a peripheral, not an organ (UCI's lesson)

The bidding AI (`record → call`) and the play AI (`record → card`) are stateless functions
behind the PBN contract — the Stockfish/UCI shape. Each is handed only what its seat legitimately
knows (P1's no-cheating-on-hidden-hands rule lives here). Hints ("what are my legal plays," "what
did partner's bid promise"), difficulty levels, post-hand review, and attract-mode self-play are
all *callers of the engine*, never features woven into the app.

## 4. Client-only, offline-first — one self-contained file

There is no server. Everything runs in the browser, and it all ships as **one self-contained
`dist/index.html`** — JS, CSS, and the inline-SVG card art inlined into a single file you could
email. That file is offline the moment it is cached; there is no app shell to precache because
there is no shell — the file *is* the app. A minimal (~20-line) service worker plus a web app
manifest are the whole offline story: they exist only to make the iOS add-to-home-screen
"semi-PWA" install reliable, not to orchestrate a precache. Local persistence is `localStorage`
(a few KB: the PBN record history and stats). **The threat model is the metro tunnel:** the
player opened the app, descended underground, lost WiFi, and everything — deal, auction, play,
hints, review — must keep working from cache alone.

The single file is a **compile target, not the authoring format.** The tortoise ships the *same*
thin artifact as the hare — one offline, view-source-able HTML file — but compiled from tested,
composable modules instead of typed out in one shot. **Same artifact shape, opposite
provenance** is the exhibit.

## 5. Frugality by deletion

The offline single-player brief lets us delete almost everything a game backend usually carries:

- **No auth.** One learner, one phone, one local store.
- **No server, no sync, no online leaderboards, no matchmaking, no scaling tier.** The state
  ownership question — whose turn, which room, cross-device presence — never arises; it all
  folds in one browser tab.
- **Keep the archive.** Local hand history + stats: nearly free over the record, and the basis
  for "you keep misplaying no-trump contracts" style feedback.

## 6. Delivery topology

The source collapses to **two concerns**, not five packages — the boundary that matters is
"pure engine" vs. "thin view," and everything compiles to one file:

- **`src/core/`** — the pure engine + AI + PBN notation: rules + scoring (deal legality, auction
  legality, follow-suit, trick resolution, contract scoring), the bidding AI (`record → call`)
  and play AI (`record → card`) as stateless functions, and PBN parse/emit. **Zero DOM imports,
  framework-agnostic TypeScript**, property-tested with vitest. This is the *only* place the
  tortoise is "big," and it is big in **tests, not runtime** — tests never ship.
- **`src/app/`** — a **thin view in Svelte 5** (components + runes `$state`/`$derived`), input
  wiring, and `localStorage` persistence (PBN record + stats). State is a fold over the move
  log; re-render is cheap because a card table's DOM is small. `core/` never imports `app/`.

Both compile to a single **`dist/index.html`** via **Vite + `vite-plugin-singlefile`**, which
inlines JS + CSS + the original inline-SVG card art into one file — no code-splitting, no vendor
chunks. Deploy = that one file to **Cloudflare static** at `bridge.b28.dev`, CI on push to main.
The **b28.dev cover slot embeds the same file** in attract mode: no separate showcase bundle,
because the cover art *is* the production artifact folding a seeded self-play deal live. The zone
CSP rules for the embed are handled separately and are origin-agnostic.

## Stack (decided)

**TypeScript + Svelte 5 + Vite + `vite-plugin-singlefile` + Cloudflare static.** A pure, tested
TS engine in `src/core/` with zero DOM/platform imports; a thin Svelte 5 view in `src/app/`;
`localStorage` for local state; a ~20-line service worker + manifest for reliable iOS install;
Vite builds it all to one self-contained `dist/index.html`, static-deployed to `bridge.b28.dev`
on push to main.

This is a deliberate divergence from the sibling entries. **Not Next.js/SSR** (that was RowClear)
and **not Gleam/BEAM/Fly** (that was Consecutive) — both bought a server story an offline
single-player game does not have. There is no household table to own, no moves to POST, no
machine to wake. And **not Astro** — it is built for content/SSG, the wrong shape for a stateful
game. One self-contained file is the right shape: the whole program is the client, and "server"
would be dead weight.

**Svelte 5 is the view framework** — a compiler, so components disappear into ~1–3KB of vanilla
JS with no runtime VDOM; the best DX-to-weight ratio for an offline-mobile one-file app. It only
touches `src/app/`, so it is **swappable**: `core/` is framework-agnostic behind the PBN
contract, and the view layer could be replaced without touching the rules. The b28.dev embed/CSP
work is zone-level and survives that choice.
