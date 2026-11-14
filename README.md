# tiny-chess-01

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

The readable source this entry was packed from is `engine.py`
(uncommitted; packed with the kit's `makefun compress`).

## Repetition filter

The search itself has no repetition detection, so left alone the engine
can shuffle a winning position into a threefold draw. At root-move
selection, a move that returns to a position already seen in this game
(2nd occurrence onward) is skipped while the side to move is ahead by
more than 150 centipawns; a draw stays available when behind or level,
and if every legal move is filtered the full list is used.

The 150cp threshold was tuned from 100cp: the 100cp variant converted
trial-1 draws but also disrupted winning shuffles in trial-0 (a win
turned into a draw, another into a loss), while the trial-1 leaks were
all between +145 and +240cp, so 150 keeps most of the gain with less
damage.

## Practice trials (house gauntlet, 6 games each)

| Trial | Score | Elo | Verdict |
|---|---|---|---|
| trial-0 | 3,0/6 | 1.279 | pass (practice floor 1.154) |
| trial-1 | 3,5/6 | 1.364 | pass |
| trial-2 | 2,0/6 | 1.099 | fail — stronger opposition, not a bug |

2 of 3 trials pass the practice floor. Trial-2's losses are pure
strength (opponents at depth-3 and 100k/250k/500k nodes), not engine
errors: no crashes, no illegal moves. The live final grades 6 random
instances and needs a dev-phase grade at or above the floor.
