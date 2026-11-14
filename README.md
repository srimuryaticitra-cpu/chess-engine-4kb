# Tiny Chess Engine — 4 KB of Python

Entry for make.fun bounty **"Build the strongest chess engine in 4 KB of Python"**
(`tiny-chess-01`, $500, deadline 25 Nov 2026). `main.py` is **3,215 bytes**.

## How it runs

The grader runs `python3 -P -s /bot/main.py` with `MAKEFUN_ENV_URL` set.
The engine plays the gauntlet through that env (POST `/game`, `/move`).
No UCI, no stdin, no arguments, no model calls. Without `MAKEFUN_ENV_URL`
it exits cleanly with `MAKEFUN_ENV_URL is not set` (exit 1).

## Design

Deterministic, no clock, no randomness.

- **Board:** 0x88, pseudo-legal moves, make/unmake, king-safety checks.
  Castling, en passant, promotions.
- **Search:** negamax alpha-beta, iterative deepening to a **fixed 100,000
  nodes per move**. Quiescence over captures/promotions and replies to
  check. Transposition table, MVV-LVA, 2 killers/ply, history, LMR,
  null-move pruning.
- **Evaluation:** tapered middlegame/endgame — material + piece-square
  tables (classic Michniewski, P100 N320 B330 R500 Q900), +10 tempo.
- **Speed:** ~70,000 nodes/s on one CPU, ~1.4 s per move, inside the 10 s limit.

## Packing

`main.py` was produced by the kit's own tool from the readable source
`engine.py` (7,513 bytes), kept beside it in the working tree but **not**
committed (snapshot cap 8,192 bytes). Rebuild with:

```
node <kit>/cli/makefun.mjs compress engine.py --out main.py
```

## Verification (local, 2026-11-14, re-checked after repack)

- **Perft startpos 1–4 = 20 / 400 / 8,902 / 197,281**, confirmed
  move-by-move against python-chess (divide at depth 4).
- **Determinism:** two independent processes pick the same root move —
  startpos `b1c3`, Ruy Lopez (1.e4 e5 2.Nf3 Nc6 3.Bb5 a6) `b5c6`.
- **Practice trial-0 (6 games, same source): 3W 0D 3L, Elo 1,279** —
  above the 1,154 floor. Wins vs depth-2, depth-3, nodes-100k.

## Licence

MIT (`SPDX-License-Identifier: MIT`). Piece-square tables are the classic
Michniewski tables.
