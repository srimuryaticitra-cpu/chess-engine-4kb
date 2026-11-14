# Tiny Chess Engine — 4 KB of Python

Entry for make.fun bounty **"Build the strongest chess engine in 4 KB of Python"**
(`tiny-chess-01`, $500, deadline 25 Nov 2026). `main.py` is **4,000 bytes**.

## How it runs

The grader runs `python3 -P -s /bot/main.py` with `MAKEFUN_ENV_URL` set.
The engine plays the gauntlet through that env (POST `/game`, `/move`).
No UCI, no stdin, no arguments, no model calls. Without `MAKEFUN_ENV_URL`
it exits cleanly with `MAKEFUN_ENV_URL is not set` (exit 1).

## Design

Deterministic, no clock, no randomness.

- **Board:** 0x88, pseudo-legal move generation, make/unmake, king-safety
  checks. Castling, en passant, promotions.
- **Search:** negamax alpha-beta, iterative deepening to a **fixed 100,000
  nodes per move**. Quiescence over captures/promotions and every reply
  when in check. Transposition table, MVV-LVA, 2 killers per ply, history,
  late-move reductions, null-move pruning.
- **Evaluation:** tapered middlegame/endgame — material + piece-square
  tables (classic Michniewski, P100 N320 B330 R500 Q900), +10 tempo.
- **Speed:** ~70,000 nodes/s on one CPU, so 100,000 nodes is about 1.4 s
  per move — inside the 10 s limit.

## Packing

`main.py` is 3 lines: `lzma`-decompresses a base85 payload and `exec`s it
(explicitly allowed by the RULES; the house baseline is packed the same
way). Unpacked source is 7,513 bytes of hand-written minified Python.

## Verification (local, 2026-11-14)

- **Move generation:** perft startpos 1–4 = 20 / 400 / 8,902 / 197,281;
  kiwipete 1–3 = 48 / 2,039 / 97,862. All match python-chess.
- **Determinism:** two fresh-server runs played the identical 32-ply
  game-1 line; root search repeats the same move.
- **Practice trial-0 (6 games): 3W 0D 3L, Elo 1,279** — above the 1,154
  floor. Wins vs depth-2, depth-3, nodes-100k (all checkmate); losses vs
  depth-4, nodes-250k, nodes-500k.

## Licence

MIT. Piece-square tables are the classic Michniewski tables.
