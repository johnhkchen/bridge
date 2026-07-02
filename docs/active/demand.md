# Vend — Demand (the pull board)

Thin demand **signals**, not epics — one line of "what + why it might matter." Epics are
**pulled** from here just-in-time when there's capacity; clearing (signal → epic →
stories/tickets) happens on pull, never ahead of demand. Cleared signals crystallize to
one line in `docs/archive/demand-cleared.md` and are deleted from here.

Seeded from **real demand**: the curious beginner on the metro with no WiFi and no bridge club
(the definition of done is "opens it a second time and understands a bid they didn't before"),
plus the tortoise-vs-hare series entry this project anchors (`docs/knowledge/vision.md` — Bridge
is above the line; the hare hit its max message length and never finished). The foundation
signal (architecture walking skeleton + `just dev` WIP preview) is already pulled as **E-001**
and off this board.

---

## Tier 1 — A finishable deal (P1: without this there is nothing)

- **Rules engine core** — legal deal, legal auction, follow-suit enforcement, trick resolution,
  and correct contract scoring (part-scores, games, slams, doubles, vulnerability), all behind
  the PBN contract. _(advances P1, P5)_
- **One competent bot table** — a bidding AI (`record → call`) and a play AI (`record → card`)
  for North/East/West that follow the rules, reason only from their own seat, and play sensibly
  end-to-end. _(advances P1)_

## Tier 2 — The teaching layer (P2: the reason this repo exists)

- **Bid explanations** — plain-language meaning for every call ("1NT = 15–17 balanced") shown
  in the bidding box as it happens. _(advances P2)_
- **Legal-play hints** — highlight the cards you may play and, on request, "what your partner's
  bid promised." _(advances P2)_
- **Post-hand review** — engine-powered replay walking the deal card by card with coaching notes
  ("you could have finessed the queen"), free off the record + engine peripheral. _(advances P2, P5)_
- **Difficulty levels** — tune opponent/partner strength so the game grows with the learner.
  _(advances P2)_

## Tier 3 — Offline-PWA + CI/CD (P3: it has to work in the tunnel and ship itself)

- **Single-file offline build + minimal service worker** — Vite + `vite-plugin-singlefile`
  compiling to one self-contained `dist/index.html`; a ~20-line service worker + web app
  manifest for reliable iOS add-to-home; `localStorage` for progress/history/stats. Fully
  playable with no network the moment the file is cached. _(advances P3)_
- **Cloudflare static deploy + CI/CD** — push to main → tests → live at `bridge.b28.dev`,
  zero-touch embed. Deploy is nobody's job. _(advances P3)_

## Tier 4 — Feel (P4: what makes it good in the hand)

- **Card/table feel pack** — mobile-first table layout, a proper bidding box, trick animation,
  and undo/redo as a learning aid (not a cheat). _(advances P4)_

## Tier 5 — Rigor as the series exhibit (P5: where the one-shot can't follow)

- **Property + simulation test suite** — legal-deal invariants, follow-suit enforcement, trick
  and contract-score correctness, PBN round-trips, and seeded-RNG full-deal simulation.
  _(advances P5)_
- **Static showcase bundle for b28.dev** — the production engine + view folding a seeded
  self-play deal in the visitor's browser as the series entry's cover, no backend, pinned
  rebuilds; AI-vs-AI determinism doubling as attract mode. _(advances P3, P5)_
