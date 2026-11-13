# tiny-chess-01

A compact chess engine in 4,096 bytes of Python.

## How it plays

- 0x88 board, negamax alpha-beta with iterative deepening to a fixed
  node budget (30,000 nodes/move — deterministic, no clock, so replays
  match move for move).
- Transposition table, killer-move ordering, MVV-LVA capture ordering,
  null-move pruning (R=2), check extensions, check-evasion quiescence
  (capture/promotion quiescence, all legal moves when in check), and
  repetition awareness (a position seen twice already scores 0).
- Evaluation: material (100/320/330/500/900) plus real piece-square
  tables (classic simplified-evaluation tables, stored as 32 mirrored
  5-centipawn characters per piece).
- Talks to the env over HTTP (`POST /game`, `POST /move`) and sends
  moves in UCI. About 2–2.5 s/move, ~22 min per 8-game trial — inside
  the 1,800 s wall budget.

The source is zlib/base85-packed inside `main.py` (the payload and the
unpacker both count toward the 4,096-byte cap, as the rules allow).

## Measured strength (kit practice trials, local env server)

- Trial 0: Elo 1087 (2 wins / 0 draws / 6 losses) — beats the three
  depth-limited house engines, loses to the four node-count engines.
- Trial 1 (colours swapped): Elo 1326 (3 wins / 1 draw / 4 losses).
- Mean over the two trials: ~1207. An earlier 40,000-node version
  scored 1326 on trial 0 but took ~1,800 s wall, too close to the cap,
  so the shipped budget is 30,000 nodes.

## Files

- `main.py` — the whole engine (3,381 bytes)
- `LICENSE` — MIT
- `README.md` — this file
