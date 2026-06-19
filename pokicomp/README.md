<div align="center">

# Pokicomp

**Pokémon TCG AI Battle Challenge — Kaggle Entry**
**وكيل ذكاء اصطناعي لمسابقة بطاقات بوكيمون التنافسية**

![Tests](https://img.shields.io/badge/tests-10%2F10-success) ![Strategy](https://img.shields.io/badge/strategy-3%20playstyles-blue) ![Brain](https://img.shields.io/badge/brain-ported%20from%20GZP-purple)

</div>

> ⚠️ Public showcase only. Full agent source, decision-log analysis, and submission package remain private.

---

## Competition

- **Main:** [Pokémon TCG AI Battle Challenge — Strategy](https://www.kaggle.com/competitions/pokemon-tcg-ai-battle-challenge-strategy)
- **Sister:** [Pokémon TCG AI Battle](https://www.kaggle.com/competitions/pokemon-tcg-ai-battle)

## Architecture

```
state_parser.py   ──►  reads kaggle-environments game state
                       ↓
brain/decision.py ──►  scores legal moves through 3 lenses
                       ├─► playstyle: aggro
                       ├─► playstyle: control
                       └─► playstyle: midrange
                       ↓
brain/voting.py   ──►  weighted vote → final action
                       ↓
agent/main.py     ──►  emits action to kaggle-environments
```

The brain is a **port of the GZP reasoning_engine** — the same `D:\project\ai\core` patterns that power the GZP-LLM project, retargeted to a discrete board game.

## Three Playstyle Profiles

Each profile is a weighting vector over the same primitive evaluators:

| Profile | Damage weight | Tempo weight | Card-advantage weight |
|---|---:|---:|---:|
| Aggro | 1.0 | 0.7 | 0.3 |
| Control | 0.3 | 0.5 | 1.0 |
| Midrange | 0.6 | 0.8 | 0.6 |

At each turn all three score every legal move; a weighted vote picks the actual play. This trivially generalizes — adding a fourth profile is a tuple in a config.

## Decision Log

Every action is journaled with:
- Game state hash
- Top-3 scored moves per profile
- Final vote
- Reason (e.g., "aggro overruled control: lethal in 2 turns")

The log makes post-mortem analysis tractable. After each test game we read the log, identify a single bad decision, and decide whether it's a brain bug or a profile-weight issue.

## Testing

10/10 internal scenario tests pass:
1. Lethal-in-1 must be taken
2. Don't waste energy on bench when active can OHKO
3. Retreat when active is about to KO and bench has lethal
4. Bench-call newly-drawn basic when active is low HP
5. Use ability before attaching energy if it draws
6. Knockout priority: active over bench when both lethal
7. Reject illegal move suggestions from voting
8. Tie-break uses pre-declared profile priority
9. Mulligan handling (no basics)
10. Trainer-card sequencing (Supporter once per turn)

## Comparison vs. Reference Deck

In sparring vs. a strong Kyogre/Abomasnow reference deck, the Lightning build the agent currently uses loses ~50 prize-card points across 100 games — a known matchup gap, not an agent bug. The fix is a deck-construction sub-task scheduled separately.

## Status

- ✅ State parser working on real `kaggle-environments` API
- ✅ Brain ported and 10/10 tests passing
- ✅ Three playstyle profiles + voting
- ✅ Submission package built (`submission.tar.gz`)
- 🔄 Awaiting `pip install kaggle-environments` on the evaluation server
- 📋 Next: deck rebalancing for the Kyogre matchup

## Author

**Abd Alrahman Mohamed** — solo entry.
abdarahman10555@gmail.com · github.com/61465
