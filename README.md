# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/exc-ana/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/exc-ana/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in a single 3,840-byte `main.py` (limit 4,096), Python stdlib only, for the make.fun bounty "Build the strongest chess engine in 4 KB of Python". It talks HTTP to the match environment (`MAKEFUN_ENV_URL`): `POST /game`, `POST /move`, `POST /resign`.

## Run it

```sh
MAKEFUN_ENV_URL=<env-url> python3 main.py
```

## How it works

`main.py` is the kit-compressed payload of the readable source (`makefun compress`, LZMA + latin-1 stub). The readable source is a 0x88 bytearray board: negamax + alpha-beta, PVS at the root, iterative deepening to a fixed 50,000-node budget per move, quiescence over captures/promotions (all evasions in check), transposition table, one killer per ply, MVV-LVA ordering, in-search repetition = 0, and material + piece-square tables with bishop pair (+40), rook 7th-rank (+15) and tapered king tables. Deterministic: fixed node budget, no clock, no randomness.

## Verification

- Perft vs python-chess, all OK (startpos d3 8902, Kiwipete d3 97862, pos3-ep d4 43238, pos4 d3 9467, pos5 d3 62379, pos6 d3 89890, promo d3 575, castle-check d3 13744, ep2 d3 24988). Re-verified 21 Nov 2026 after the in-session source rewrite (`dev/verify_kit.py` on the re-compressed `main.py`): ALL PERFT OK, payload identical to source (11,080 bytes). Re-verified again 21 Nov 2026 after the genuine core-function refactor (`dev/verify_kit.py`): ALL PERFT OK, DETERMINISM OK, payload identical to source (11,343 bytes).
- Deterministic: fixed 50,000-node budget; root moves identical across 2 runs (startpos d2d4, Kiwipete e2a6).
- Local practice trial 0 (6 games vs the kit's house gauntlet): **3W / 1D / 2L, Elo 1,364**, above the 1,154 floor. The 30-game mean 1,278 was measured on the bishop-pair +30 predecessor (identical trial-0 result). No crashes or timeouts (~1.7 s/move, limit 10 s). Agreement with the reference engine 92.2%. Re-run 21 Nov 2026 on the re-compressed `main.py` (tag `v8-50k-rewrite`, wall 371 s, engine exit 0): identical **3W / 1D / 2L, Elo 1,364** (checkmate 5, threefold_repetition 1). Re-run again 21 Nov 2026 after the genuine refactor of the core functions for `thinking_reproduces` (tag `v8-50k-refactor`, wall 376 s, engine exit 0): identical **3W / 1D / 2L, Elo 1,364** (checkmate 5, threefold_repetition 1, spare_plies 348) — behaviour unchanged.

---

<sub>Entered on [make.fun](https://make.fun/?ref=twejymd7&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
