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

## 4. Client-only, offline-first

There is no server. Everything runs in the browser. The service worker (Workbox via
vite-plugin-pwa) precaches the app shell and the engine so the whole game is available with no
network. Local persistence (IndexedDB / localStorage) holds progress, hand history, and stats.
The install path is the iOS add-to-home-screen "semi-PWA." **The threat model is the metro
tunnel:** the player opened the app, descended underground, lost WiFi, and everything — deal,
auction, play, hints, review — must keep working from cache alone.

## 5. Frugality by deletion

The offline single-player brief lets us delete almost everything a game backend usually carries:

- **No auth.** One learner, one phone, one local store.
- **No server, no sync, no online leaderboards, no matchmaking, no scaling tier.** The state
  ownership question — whose turn, which room, cross-device presence — never arises; it all
  folds in one browser tab.
- **Keep the archive.** Local hand history + stats: nearly free over the record, and the basis
  for "you keep misplaying no-trump contracts" style feedback.

## 6. Delivery topology

Consumers of the same pure core, split so package boundaries police the architecture:

- **`engine`** — pure rules + scoring (deal legality, auction legality, follow-suit, trick
  resolution, contract scoring). PBN in → legal calls/plays / next state out. Imports nothing
  DOM- or platform-specific.
- **`ai`** — bidding AI and play AI as stateless functions. Imports `engine` only.
- **`view`** — card/table components, `DealState → view`. No engine mutation, no platform
  coupling.
- **`client`** — the Vite app shell + service worker: the installable offline PWA at
  `bridge.b28.dev`, local persistence for progress/history/stats.
- **`showcase`** — a static bundle for the b28.dev cover slot: `engine` + `view` folding a
  seeded self-play deal in the visitor's browser. No backend, always up — the cover art *is*
  the production engine playing a deal live. Rebuilt deliberately and pinned, not auto-tracking.

Deploy = Cloudflare static (Workers static assets or Pages) at `bridge.b28.dev`, CI/CD on push
to main; the zone CSP rules for the embed are handled separately and are origin-agnostic.

## Stack (under discussion)

The baseline: **TypeScript + Vite + vite-plugin-pwa (Workbox) + Cloudflare static.** A pure,
tested TS engine in a package with zero DOM/platform imports; a Vite SPA whose service worker
precaches the app shell and engine for offline; IndexedDB/localStorage for local state; static
deploy to `bridge.b28.dev` on push to main.

This is a deliberate divergence from the sibling entries. **Not Next.js/SSR/vinext** (that was
RowClear) and **not Gleam/BEAM/Fly** (that was Consecutive) — both of those bought a server story
that an offline single-player game simply does not have. There is no household table to own, no
moves to POST, no machine to wake. A static SPA PWA is the right shape: the whole program is the
client, and "server" would be dead weight.

The **view framework is TBD**. React is the default because the team knows it, but Preact or
Solid are on the table if offline-mobile bundle size argues for a lighter runtime — the engine
and `ai` packages are framework-agnostic behind the PBN contract, so the view layer can be
chosen (or swapped) late without touching the rules. The b28.dev embed/CSP work is zone-level
and survives any of these choices.
