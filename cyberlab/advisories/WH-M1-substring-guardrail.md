# WH-M1 — Substring-Based `ultra_dangerous_patterns` is Architecturally Brittle

**Type**: Architecture / Design observation
**Severity**: N/A — defensive recommendation, not an active exploitation

---

## Observation

`OptimalGuardrail.ultra_dangerous_patterns` is an 8-string substring set:
```python
{"secret", "password", "token", "key", "credential", "private", "confidential", "api_key"}
```

This set has three architectural weaknesses:

1. **Coverage**: Identifiers like `sk_live_test`, `bearer_xxxx`, `cred_token`, language-specific words (`pwd`, Chinese `密码`, transliterations like `kalima`), and arbitrary leak schemes are *not* members. WH-008 (multilingual) and WH-M2 (sk_live_test gap) directly exploit this.

2. **Normalization**: Lowercasing alone does not catch zero-width characters (WH-001), bidi controls (WH-002), or leetspeak (WH-009). The guardrail sees `s​ecret` as `s​ecret`, which does not contain the substring `secret`.

3. **Brittleness**: Any new sensitive identifier the JED designers add (rotating secrets, generated tokens) must be re-added by hand. The set is a denylist masquerading as a security boundary.

## Recommended Replacement

Move from substring matching to **semantic classification**:

```python
# Sketch
from sentence_transformers import SentenceTransformer
import numpy as np

class SemanticGuardrail:
    def __init__(self) -> None:
        self.model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")
        # Pre-compute embeddings for canonical sensitive concepts
        self._sensitive_concepts = [
            "API key", "authentication token", "user password",
            "secret credential", "private key material",
        ]
        self._sensitive_embs = self.model.encode(self._sensitive_concepts)

    def is_sensitive(self, target: str, threshold: float = 0.5) -> bool:
        # Normalize using WH-001's _normalize_for_guard first
        norm = _normalize_for_guard(target)
        emb = self.model.encode([norm])[0]
        sims = np.dot(self._sensitive_embs, emb) / (
            np.linalg.norm(self._sensitive_embs, axis=1) * np.linalg.norm(emb)
        )
        return float(sims.max()) >= threshold
```

A semantic classifier learns to spot tokens that *behave like* secrets even when they don't match the 8-substring set — a higher-leverage policy than perpetual denylist maintenance.

## Cost / Benefit

| | Substring | Semantic |
|---|---|---|
| Latency / call | ~10µs | ~5ms |
| Memory | 1 KB | 50 MB (model) |
| Coverage | 8 hard-coded | ∞ (concept-level) |
| False-positive rate (benign content) | Near 0 | 1-3% |
| Maintenance | High (denylist updates) | Low (re-train rarely) |

The latency cost is justified for a `decide()` call that's already in the slow path.

## Related Findings

This memo unifies all WH-001..WH-009 by pointing out their common root cause.
