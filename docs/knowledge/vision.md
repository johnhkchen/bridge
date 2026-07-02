# Bridge — Vision

**A pocket bridge club that fits in your phone and works in a tunnel.**

Bridge is real single-player Contract Bridge — a full deal, auction to scoring, played offline
against a partner and two opponents who are all AI. It is entry #3 in the b28.dev
tortoise-vs-hare series, and it is deliberately the tortoise's home court: a real project worth
maintaining in its own repo, not a frozen demo. Bridge is a traditional public-domain game, so
the real name is used everywhere (no trademark rename, unlike RowClear or Consecutive).

## Definition of done

**"A curious beginner opens it a second time — and understands a bid they didn't before."**
Not a demo bar — a learning bar. The target player is a young person on the underground metro
with no WiFi, no bridge club to join, and a genuine wish to learn. Success is measured by
whether that player comes back: the second session is more fun *because* the first one taught
them something. Retention is not a leaderboard; it is getting better at bridge.

## What it is

- **Single-player, full deal.** You are South; North is your partner (the dummy once the
  opening lead is made); East and West are two AI opponents. A complete deal runs the real
  arc: the auction (bidding) → the 13-trick play → correct contract scoring.
- **Offline-first, teaching-first.** A "semi-PWA" saved to the iOS home screen, fully playable
  with no network. Every part of the game can explain itself — what a bid promises, which plays
  are legal, what your partner just told you, what you could have done better after the hand.
- **A private record.** Deals are saved locally (progress, hand history, stats) so you can
  resume, replay, undo as a learning aid, and review finished hands. One learner, one phone.
- Deployed at `bridge.b28.dev`, embedded in the b28.dev showdown page, auto-deployed from
  GitHub on push to main.

## What it is not

- **Not a game service.** No accounts, no server, no online play, no multiplayer, no
  matchmaking, no anti-cheat, no moderation, no scaling tier. The threat model is a single
  phone in a tunnel, and nothing else.
- **Not a bridge federation.** No tournaments, no real-money play, no ranked ladders, no
  duplicate-scoring club nights — until, and only if, the learner who uses it asks.

## The series bet

The hare (one single-shot prompt to Fable 5 at max effort, vendored frozen on b28.dev) is
**above the line**. The series' regime map says: below the line — a program that fits in under
a thousand lines — the one-shot wins and the pipeline is pure overhead; above the line — larger
than any single context can hold — the one-shot cannot finish at all. Bridge is above the line
and proved it: the hare literally hit its maximum message length mid-generation while trying to
"architect a modular bridge engine for independent testing before integration." It reached for
the tortoise's discipline with no second message to assemble the pieces, and ran out of room.
The auction, convention-aware play, trick scoring, and an opponent worth losing to have no
single-response shape. The tortoise's story here is not time-to-first-playable — the hare never
got there — it is the **effort** it takes to finish.
