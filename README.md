# tiny-chess-01 v2 — "Checkmate in 4KB" (resubmit)

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/jesyeth77/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/jesyeth77/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

Built for the [make.fun bounty "Build the strongest chess engine in 4 KB of Python"](https://make.fun/bounty/tiny-chess-01) ($500 in USDC).

A compact chess engine in a single Python file (`main.py`, at most 4,096
bytes as committed). Written from scratch: a 0x88 board, negamax with
alpha-beta pruning, iterative deepening to a fixed node budget,
quiescence search over captures and check evasions, a transposition
table, killer moves and MVV-LVA move ordering, and an incremental
material + piece-square evaluation. Deterministic: the same position
always gets the same move (no clock, no unseeded randomness).

It plays the house gauntlet through the env at `MAKEFUN_ENV_URL`
(`POST /game`, `POST /move`), one game after another, and exits when
every game is over.

The readable source this entry was packed from is `engine_min.py`
(uncommitted; packed with the kit's `makefun compress`).

## v2 changes vs v1

- **Single-pass search/quiescence:** v1 made and unmade every
  pseudo-legal move once to filter legality, then made it again in the
  search loop — two legality scans per move. v2 orders the pseudo-legal
  moves and skips illegal ones on the single make. Same moves, ~2.25x
  faster (trial wall 2.066s -> 916s at the same 60k budget).
- **Node budget 60k -> 120k:** affordable now that the engine is
  faster; no game lost on time in any test trial.

## Repetition filter

The search itself has no repetition detection, so left alone the engine
can shuffle a winning position into a threefold draw. At root-move
selection, a move that returns to a position already seen in this game
(2nd occurrence onward) is skipped while the side to move is ahead by
more than 150 centipawns; a draw stays available when behind or level,
and if every legal move is filtered the full list is used.

## Practice trials (house gauntlet, 6 games each)

| Trial | Score | Elo | Verdict |
|---|---|---|---|
| trial-0 | 4,5/6 | 1.545 | pass (practice floor 1.154) |
| trial-1 | 4,0/6 | 1.451 | pass |
| trial-2 | 4,5/6 | 1.545 | pass |

3 of 3 trials pass the practice floor (v1: 2 of 3). Total 13,0/18 vs
v1's 8,5/18; mean Elo 1.514 vs v1's 1.247. The live practice grade of
v1 was Elo 1.279; v2's worst local trial beats that, and its mean is
well higher.
