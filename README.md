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
