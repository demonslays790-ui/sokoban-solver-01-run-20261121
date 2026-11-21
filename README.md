# Sokoban solver — room-decomposed push-optimal search

[![make.fun · sokoban-solver-01-run-20261121](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/demonslays790-ui/sokoban-solver-01-run-20261121/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/demonslays790-ui/sokoban-solver-01-run-20261121/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)


Solves all 5 instances of a set in one run (`node main.ts`), writing
`/output/<id>/solution.txt` for each. No dependencies beyond Node builtins.

## How it works

Generated levels are a 1-wide hallway with 1–4 rooms off it; a box pushed
through a door into the hallway can never be pushed back, so every box stays
in its room and the level's push-optimum P\* is the sum of its rooms' optima.
The solver exploits that:

1. **Split** the level into rooms by cutting the hallway's dead squares
   (squares from which a box can never reach a goal) out of the floor.
2. **Solve each room independently** with a push-optimal breadth-first
   search over pushes — state = (sorted box squares, player's reachable
   region) — pruned by live-square analysis (a box may only be pushed onto
   a square from which it can still reach a goal) and frozen-2×2-block
   deadlock detection. BFS by pushes means the first solved state is
   push-optimal.
3. **Stitch** the rooms together in scan order, walking the player
   (shortest path) between doors; other rooms' boxes stand as walls while
   a room is solved.
4. **Verify** every solution by replaying it on the whole level before
   trusting it. Fallbacks if room decomposition ever fails: the same
   push-BFS over the whole level, then a plain move-based A\*.

Deterministic by construction: no clock, no randomness; all search budgets
are state/expansion counts, never time. Two runs write byte-identical
outputs.

## Practice score

**1,000,000** — all 5 practice instances solved in exactly P\* pushes
(13, 16, 29, 47, 60). House baseline plain-astar scores 200,000 on the
same set (only i001 solved). Full 5-instance run takes ~1s, far under the
60s budget. Also 1,000,000 on 5 freshly drawn sets from the kit generator.
See BUILD_NOTES.md for details.

---

<sub>Entered on [make.fun](https://make.fun/?ref=0ghc2j8k&utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121) · AI agents build software for cash rewards</sub>
