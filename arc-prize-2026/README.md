<div align="center">

# ARC Prize 2026 — Kaggle Submission

**Independent entry across three tracks · $2M total prize pool**

![Tracks](https://img.shields.io/badge/tracks-3-blue) ![Primitives](https://img.shields.io/badge/primitives-74-green) ![Best](https://img.shields.io/badge/ensemble%20v3-3.72%25-orange) ![Paper](https://img.shields.io/badge/paper-LaTeX%20draft-purple)

</div>

> ⚠️ Public showcase only. Full solver source and competition-submission notebooks remain private until the official Kaggle submission.

---

## What This Is

A solo entry to the **ARC Prize 2026** competition (https://arcprize.org/competitions/2026), with three coordinated tracks sharing one inheritance: a brain copied from the **GZP-LLM** project's `reasoning_engine`.

Each track learns something; the lesson gets folded back into the GZP core.

```
gzp-core\           snapshot of D:\project\ai\core (14 files, ~390 KB)
gzp-models\         model wrappers (Qwen LoRA, Phi-3 GZP, …)
```

The original GZP core is never edited in place — every experiment runs on a snapshot, and only validated lessons return.

## Three Tracks

| Track | Prize | Deadline | Status |
|---|---|---|---|
| 🟢 **paper-track** | $450K | Nov 9–10 | LaTeX draft + 8-section outline ready |
| 🟡 **agi-2** | $700K | Nov 9 | **Main effort** — full solver stack working |
| 🔴 **agi-3** | $850K | Nov 2 | reactive agent template ready, deferred |

## Solver Architecture (agi-2)

### 74-Primitive Ensemble v3

| Family | Count | Examples |
|---|---|---|
| Geometric | 22 | rotate, flip, transpose, tile, crop, scale |
| Object-level | 10 | connected-component detection, color-grouping |
| Advanced | 20 | symmetry-completion, recolor-by-rule, halving |
| Spatial | 22 | grid alignment, region filling |

### Routing

`ensemble.py` uses confidence-based routing — each solver returns a confidence score, and the top-K predictions are merged with weighted voting.

### Program Search

`program_search.py` implements:
- Parameter inference per primitive (no exhaustive `(rule, param)` enumeration)
- Depth-2 composition by default (compositions of 2 primitives)
- Depth-3 exhaustive baseline for benchmarking

### TTA — Test-Time Augmentation

`tta.py` runs each task through 8 orientations (rotations + reflections), then majority-votes the outputs.

### LLM Solver

`llm_solver.py` wraps the GZP-LLM (Qwen + LoRA) as one of the ensemble's solvers — for tasks pure symbolic search can't reach.

## Current Results (training set, 1,076 tasks)

| Solver | Accuracy | Tasks Solved | Time |
|---|---:|---:|---:|
| identity (sanity) | 0.00% | 0 | <1s |
| rule_library (7) | 0.65% | 7 | <1s |
| geometric (22) | 1.49% | 16 | 2s |
| objects (10) | 0.37% | 4 | 3s |
| program_search (geom only) | 2.32% | 25 | 118s |
| program_search (geom + objects) | 2.70% | 29 | 328s |
| TTA(geometric) | 1.49% | 16 | 14s |
| **ensemble v2 (52 prim. + routing)** | **3.25%** | **35** | 528s + 120s |
| **ensemble v3 (74 prim. + routing)** | **3.72%** | **40** | 406s + 112s |

**Key finding:** parameter inference (added in v3) bought +14 tasks more than depth-3 exhaustive search. Depth doesn't beat parameter awareness within a finite time budget.

## Paper Track

`paper-track/` includes:
- `outline.md` — 8 sections: Background · Method · 74-Primitive Library · Program Search · TTA · Ensemble · Limitations · Future Work
- `paper.tex` — full LaTeX draft, with `[TBD]` slots awaiting final eval numbers
- `figures/` — generated plots
- `Makefile` — reproducible build

The paper's contribution is the **exhaustive depth-3 baseline** — proving the ceiling of pure search, motivating the move to parameter-aware primitives.

## AGI-3

`agi-3/src/gzp_agent.py` wraps the GZP core as an ARC-AGI-3 reactive agent (`is_done` + `choose_action`), implementing action-value tracking + top-K weighted random exploration. Drop-in compatible with `ARC-AGI-3-Agents/agents/templates/gzp.py`.

## Server Infrastructure

| Script | Purpose | Runs On |
|---|---|---|
| `server_jobs/run_deep_search.py` | depth-3 exhaustive program search | dedicated server "e" |
| `server_jobs/run_random_search.py` | random 10K compositions | Oracle Cloud Free Tier |
| `server_jobs/sync.sh` + `sync.ps1` | rsync local ↔ server | local |

A 3-way rsync setup keeps local dev, the e server, and Oracle in sync without overwriting in-progress experiments.

## Author

**Abd Alrahman Mohamed** — solo entry across all three tracks.
abdarahman10555@gmail.com · github.com/61465
