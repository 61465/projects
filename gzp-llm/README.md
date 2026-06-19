<div align="center">

# GZP-LLM

**The First Independent Arabic Language Model from Egypt**
**أول نموذج لغوي عربي مستقل من مصر**

![Base](https://img.shields.io/badge/base-Qwen2.5--7B-blue) ![LoRA](https://img.shields.io/badge/LoRA-r%3D64-green) ![Align](https://img.shields.io/badge/alignment-identity--DPO-orange) ![Training](https://img.shields.io/badge/training-Kaggle%20GPU-purple)

</div>

> ⚠️ Public showcase only. Datasets, checkpoints, training scripts, and proprietary evaluation harness remain private.

---

## Vision

The Arabic language has 400+ million speakers and a civilizational weight that deserves more than a translated wrapper around Western models. **GZP-LLM** is the first step toward digital sovereignty for Arabic AI — built by Arab hands, on Arab soil, with Arab cultural understanding baked in.

This is not a fine-tune. This is a re-alignment.

## What Makes It Different

| | GZP-LLM | Translated GPT-style models |
|---|---|---|
| Cultural context | Arab daily life, idioms, dialect awareness | Generic, often US-centric |
| Identity | Self-identifies as ARIA (Arab AI companion) via DPO | Self-identifies as the underlying foundation model |
| Memory | Persistent SQLite per-user memory across sessions | Stateless or vendor-controlled memory |
| Curiosity | Active curiosity engine — asks back, explores | Reactive only |
| Reasoning surface | Uses GZP `reasoning_engine` (object/geometric/program-search primitives) | Native CoT only |
| Deployability | Runs on a single consumer GPU via Ollama | Closed API or 70B+ requirement |

## Technical Stack

- **Base model:** Qwen2.5-7B (chosen for strong Arabic baseline + permissive license)
- **Adapter:** LoRA r=64, α=16 — full fine-tune impractical on Kaggle GPU budget
- **Alignment:** Identity DPO — teaches the model "you are ARIA" not "I am Qwen"
- **Fallback cascade:** Phi-3-mini for low-resource environments
- **Memory:** SQLite per-user (`aria_brain.db`) with 3D evolution visualization
- **Training infra:** Kaggle (T4 / P100), 6-step dataset audit pipeline before each run

## Dataset Pipeline (6-step audit)

```
_audit_step1.py   ──►  schema validation
_audit_step2.py   ──►  duplicate detection
_audit_step3.py   ──►  language purity (Arabic ratio per sample)
_audit_step4.py   ──►  content safety filtering
_audit_step5.py   ──►  ChatML format conformance
_audit_step6.py   ──►  final sample-distribution report
```

A dataset only enters training after passing all 6 gates. This caught silent corruption that would otherwise burn 12+ GPU-hours.

## Current Capabilities

- **Conversational Arabic** — modern Standard + Egyptian dialect handling
- **Identity stability** — answers "من أنت؟" with ARIA persona, not Qwen
- **Memory recall** — remembers user preferences across sessions
- **Curiosity loop** — asks clarifying questions when input is ambiguous
- **Multi-turn coherence** — verified over 50+ turn dialogues

## What This Folder Contains

This is a **showcase**, not the model. It exists to prove the project's scope, methodology, and seriousness. The actual model weights, full training scripts, and evaluation datasets stay private until the official release.

## Roadmap

- **v1.0** — current: Qwen2.5-7B + LoRA + Identity DPO, single-user memory
- **v1.5** — multi-user concurrent memory, RAG over user notes
- **v2.0** — released base model with full Arabic pretraining corpus (not just LoRA)
- **v3.0** — open weights for the Arabic research community

## Why It Matters

Most "Arabic AI" today is OpenAI/Anthropic/Google translating for the Arab world. The data centers, the weights, the IP — all live elsewhere. GZP-LLM is the foundation of a different stack: **Arabic AI that doesn't ask permission**.

## Author

**Abd Alrahman Mohamed** — solo researcher and engineer.
abdarahman10555@gmail.com · github.com/61465

---

*For research collaboration or pilot inquiries, please contact the author. The model is not yet publicly hosted.*
