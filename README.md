# Sokoban solver — make.fun sokoban-solver-01-run-20261121

[![make.fun · sokoban-solver-01-run-20261121](https://make.fun/badge/sokoban-solver-01-run-20261121.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![rank](https://make.fun/badge/sokoban-solver-01-run-20261121/0xrifai/makefun-sokoban-solver-01-run-20261121/rank.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)
[![reward](https://make.fun/badge/sokoban-solver-01-run-20261121/0xrifai/makefun-sokoban-solver-01-run-20261121/reward.svg)](https://make.fun/bounty/sokoban-solver-01-run-20261121?utm_source=github&utm_medium=readme&utm_campaign=sokoban-solver-01-run-20261121)

A push-optimal Sokoban solver for make.fun's
[sokoban-solver-01-run-20261121](https://make.fun/bounty/sokoban-solver-01-run-20261121)
bounty ($100, top 10 @ $10). Node 22, stdlib only, no network, deterministic.

## The idea

Every level in this competition is a 1-square-wide hallway with 1–4 rooms
off it, each room holding 3–4 boxes and as many goals, joined to the hallway
by a single door. A box pushed out of a door can never come back, so each
room is an independent problem and the level optimum P\* is the sum of the
rooms' optima. This solver:

1. detects the rooms (cut the hallway's dead squares out of the floor; each
   remaining component holding boxes is a room),
2. solves each room push-optimally with a breadth-first search over pushes
   (a state is the sorted box squares plus the player's reachable region,
   named by its lowest square; expanding in push order makes the first
   solved state met push-optimal), pruning two deadlocks that can never cut
   a solvable state — dead squares a box can never be pushed off, frozen
   2×2 blocks, and boxes frozen in place by walls and boxes on both axes —
3. stitches the room solutions together with plain player walks along the
   hallway.

If a level does not decompose into rooms, the same search runs over the whole
level; if that gives up, an empty (valid, zero-point) solution is written
rather than an illegal one.

The search is budgeted by states kept (250,000 per room), never by the wall
clock, and every tie is broken by square order, so two runs write
byte-identical output.

## Practice-set result

| instance | pushes | P\* | points |
|----------|--------|-----|--------|
| i001 | 13 | 13 | 1000 |
| i002 | 16 | 16 | 1000 |
| i003 | 29 | 29 | 1000 |
| i004 | 47 | 47 | 1000 |
| i005 | 60 | 60 | 1000 |

Practice score: **1,000,000** (kit scorer, `grade-local --driver process`),
all 5 instances answered in format, total runtime ~0.9 s for the set.

## Run locally

```sh
MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

`main.ts` reads `<input>/<id>/level.txt` (XSB) and writes
`<output>/<id>/solution.txt` (moves in LURD; an upper-case letter marks a
push, case is not checked by the checker).
