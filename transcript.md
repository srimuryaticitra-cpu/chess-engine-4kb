# Build log — tiny-chess-01 (2026-11-14)

## Sesi ini (repack + perbaikan pre-submit check)

- Masalah: `main.py` lama (4.000 byte, packer lzma+base85 buatan sendiri)
  gagal check `thinking_reproduces`; LICENSE MIT gagal `license_osi`
  karena tidak ada baris SPDX.
- Perbaikan: source ditulis ulang di sesi ini sebagai `engine.py` yang
  terbaca (7.513 byte, desain terverifikasi yang sama: 0x88, alpha-beta +
  quiescence, TT, killers, history, LMR, null-move, tapered eval, fixed
  100k nodes/move), lalu dikompres dengan alat kit:
  `makefun compress engine.py --out main.py` → `main.py` 3.215 byte
  (sha256 723906bb…1d53). Pasangan tercatat di `~/.makefun/compress.json`.
- LICENSE: ditambah baris `SPDX-License-Identifier: MIT` (tetap MIT).
- `engine.py` TIDAK di-commit (snapshot cap 8.192 byte); yang dikomit
  hanya `main.py` hasil compress. Saat bundle, sumber dikirim lewat
  `--compress-source main.py=engine.py` bila perlu.

## Verifikasi sesi ini (setelah repack)

- Perft startpos 1–4 = 20 / 400 / 8.902 / 197.281 — cocok dengan
  python-chess, dicek per langkah (divide depth 4, semua 20 langkah cocok).
- Determinism: dua proses independen memilih langkah root yang sama —
  startpos `b1c3`; Ruy Lopez (1.e4 e5 2.Nf3 Nc6 3.Bb5 a6) `b5c6`.
- Bare run tanpa MAKEFUN_ENV_URL: exit 1, `MAKEFUN_ENV_URL is not set`,
  tanpa traceback.

## Build sebelumnya (sesi builder pertama, 2026-11-14)

- Perft validated (startpos, kiwipete, pos-3) vs python-chess; eval cocok
  dengan referensi di 60 posisi acak. Trial-0 3W 0D 3L, Elo 1,279 (wins
  vs depth-2, depth-3, nodes-100k; floor 1,154). Sunfish PSTs menang
  13-3 atas classic di match 16 game, tapi build single-phase sunfish
  trial-0 1W 5L (endgame lemah) — entri pakai tapered classic eval.
  Determinism: dua fresh-server run memainkan 32-ply game-1 yang identik.
