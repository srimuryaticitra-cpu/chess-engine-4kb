# Build log — tiny-chess-01 (2026-11-14)

Dev engine: 0x88 board, alpha-beta + quiescence, TT, killers, history,
LMR, null-move, tapered eval, fixed 100k nodes/move. Perft validated
(startpos 20/400/8902/197281, kiwipete, pos-3) vs python-chess; eval
matches the reference on 60 random positions.

Match 16 game (30k nodes): sunfish PSTs beat classic 13-3 (+255 Elo).
Single-phase sunfish build: trial-0 1W 5L (below floor) — endgame eval
too weak, so the entry uses tapered classic eval. Final: trial-0 3W 0D 3L,
Elo 1,279 (wins vs depth-2, depth-3, nodes-100k; floor 1,154).

Bare run without MAKEFUN_ENV_URL first raised KeyError at the exec line;
fixed to exit cleanly with "MAKEFUN_ENV_URL is not set". Determinism:
two fresh-server runs played the identical 32-ply game-1 line.
main.py 4,000 bytes (lzma + base85, allowed by RULES).
