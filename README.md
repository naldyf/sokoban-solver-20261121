# Sokoban Solver — 21 November 2026

[![make.fun · sokoban-solver-01-run-20261121](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/naldyf/sokoban-solver-20261121/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/naldyf/sokoban-solver-20261121/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)

A push-optimal Sokoban solver, entered in the [make.fun](https://make.fun) bounty
[sokoban-solver-01-run-20261121](https://make.fun/bounty/sokoban-solver-01-run-20261121).

## The idea

Each level is a hallway with 1–4 rooms off it, 3–4 boxes per room, and a box
pushed out of a room into the hallway can never come back — so the rooms are
independent problems joined only by the player's walk, and the level's
push-optimal count is the sum of its rooms' optima. Solving each room on its
own (3–4 boxes in a 6×6 slot) is far cheaper than searching the whole level
(up to 16 boxes), whose state space is the product of the rooms' positions.

A state is the boxes' squares plus the player's reachable region (named by
its lowest square). The search expands states in order of pushes made, so the
first solved state it meets is push-optimal. Two deadlocks are pruned: a dead
square (one from which no push brings a box to any goal) and a frozen 2×2
block (walls and boxes with a box among them off its goal). Neither prunes a
solvable state.

## Results on the practice set

| level | boxes | rooms | pushes | optimal | points |
|-------|-------|-------|--------|---------|--------|
| i001  | 3     | 1     | 13     | 13      | 1000   |
| i002  | 4     | 1     | 16     | 16      | 1000   |
| i003  | 8     | 2     | 29     | 29      | 1000   |
| i004  | 12    | 3     | 47     | 47      | 1000   |
| i005  | 16    | 4     | 60     | 60      | 1000   |

**Score 1,000,000** (the maximum) in 0.9 seconds for the whole set — the house
baseline (plain A*, no deadlock detection) scores 200,000, solving only i001.

## How it runs

`node main.ts` (Node 22, standard library only, no network, no dependencies).
It reads every `level.txt` under `$MAKEFUN_INPUT_DIR` (default `/input`) and
writes `solution.txt` (LURD moves, an upper letter marks a push) under
`$MAKEFUN_OUTPUT_DIR` (default `/output`). Deterministic: no clock, no
randomness, a state-count budget only, so two runs write the same bytes. If a
room's search gives up, it falls back to a search over the whole level rather
than zeroing the level, and one level's failure never aborts the run.
