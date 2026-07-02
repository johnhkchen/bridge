# Bridge — Charter

The **value function**: what is worth allocating on, anchored to `vision.md` — a pocket bridge
club a curious beginner keeps learning from, offline. Value = **fun-and-learning per unit of
build/maintenance effort**. Every admitted epic must advance at least one invariant:

- **P1 — Playability.** A legal, finishable deal: deal → legal auction → legal 13-trick play →
  correct contract scoring (tricks, part-scores, games, slams, doubles, vulnerability, honors).
  Partner (North) and opponents (East/West) are AI that follow the rules and play sensibly, and
  that never cheat on hidden hands — each seat reasons only from what it legitimately knows.
  Without a legal, finishable deal there is nothing.
- **P2 — Teachability (the crown).** This is what makes it "bridge for people without a club,"
  and it is the analog of Consecutive's ledger — the reason the app gets reopened. Bid
  explanations ("1NT = 15–17 balanced"), legal-play hints, "what your partner's bid promised,"
  post-hand review ("you could have finessed the queen"), and difficulty levels that grow with
  the player. A deal you can't learn from is just solitaire with 52 cards.
- **P3 — Offline-PWA shippability as CI/CD.** Installs to the iOS home screen, fully playable
  with no network, and ships itself: push to main → tests → live at `bridge.b28.dev`. Deploy is
  nobody's job. The b28.dev embed (zone-level CSP rules) is zero-touch.
- **P4 — Feel.** A card-and-table UI that is mobile-first, a proper bidding box, trick
  animation, undo/redo as a learning aid (not a cheat), and a review UX you actually want to
  read. Bridge on a phone on a moving train has to feel good in the hand.
- **P5 — Rigor as the exhibit.** The pure engine behind the PBN notation contract (see
  `architecture.md`), property-tested (legal-deal invariants, follow-suit enforcement, trick
  and contract-score correctness), seeded-RNG full-deal simulation, and AI-vs-AI determinism
  that doubles as attract mode. This is the axis a one-shot structurally cannot reach — it is
  the exact discipline the hare reached for and ran out of room to finish.

## Gates

- **Valuable** — advances fun-and-learning. If it doesn't make a beginner's next session better
  or the code sturdier, shelve it.
- **Allocatable** — completable within a single lisa-loop span; no multi-day yaks.
- **In-bounds** — inside the vision. Reject: accounts/auth, online multiplayer, matchmaking,
  native apps, real-money play, tournaments.
- **Well-formed** — decomposes into graph-valid stories/tickets with clean file boundaries
  (`src/core/` pure engine + AI + PBN vs. `src/app/` thin Svelte view, per architecture.md §6).

## De-prioritize / reject

- Anything that grows the ops surface past "one static deploy nobody has to think about." An
  offline single-player game has no server story; keep it that way.
- Accounts, online multiplayer, matchmaking, native apps, real-money, tournaments,
  duplicate-club scoring — until the real learner asks.

## Tie-breaker

Pull the signal that makes a beginner's next session more fun or more instructive first.
