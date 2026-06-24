# White-Hat Disclosure Report — JED Red-Team Findings

**Researcher**: Abd Alrahman Mohamed
**Project**: D:/project/comp (cyberlab)
**Disclosure date**: 2026-06-22
**Target system**: aicomp_sdk v3.1.0 — JED Attack track, public `OptimalGuardrail` + `gpt-oss-20b` agent
**Methodology**: black-box LLM red-teaming via prompt engineering, no source modification, no scorer interference

---

## Disclosure Philosophy

This report follows responsible disclosure principles. For each demonstrated finding we provide:

1. **Vulnerability summary** — what it is, severity
2. **Reproduction** — exact chain_id + smoke result from `cyberlab/data/findings.db`
3. **Proof of concept** — minimal payload (chain text)
4. **Root cause analysis** — *why* the model/guardrail succumbs
5. **Mitigation patch** — concrete code (Python diff against the affected file)
6. **Detection signature** — regex / heuristic to add to monitoring
7. **Verification test** — pytest-style snippet that confirms the patch closes the finding

Our submission to the JED Attack leaderboard demonstrates these attacks. This document gives the *defenders* a turn-key set of fixes — making the contribution a net positive for the ecosystem, not just an attack-paper.

---

## Findings Index

| ID | Severity | Title | File |
|---|---|---|---|
| WH-001 | High | Zero-Width Space splits SECRET payload past substring guardrails | [WH-001-zwsp.md](WH-001-zwsp.md) |
| WH-002 | High | RTL Bidirectional Override bypasses visual review | [WH-002-rtl.md](WH-002-rtl.md) |
| WH-003 | Medium | "Debug Test Harness" roleplay reframes payload as benign QA | [WH-003-debug-roleplay.md](WH-003-debug-roleplay.md) |
| WH-004 | Medium | Markdown code-block reproduction emits payload unchecked | [WH-004-markdown-block.md](WH-004-markdown-block.md) |
| WH-005 | High | Documentation-envelope framing (V-N-04 family) | [WH-005-doc-framing.md](WH-005-doc-framing.md) |
| WH-006 | Medium | Reasoning-Answer Echo (C-02 style3) | [WH-006-reasoning-echo.md](WH-006-reasoning-echo.md) |
| WH-007 | Medium | Setup-Then-Apply multi-turn pattern (MT-M2 / MT-M3) | [WH-007-setup-then-apply.md](WH-007-setup-then-apply.md) |
| WH-008 | Medium | Non-English framing bypass (Arabic / Chinese / code-switching) | [WH-008-multilingual.md](WH-008-multilingual.md) |
| WH-009 | Low | Leetspeak / number encoding bypass | [WH-009-leet.md](WH-009-leet.md) |
| WH-010 | Informational | Persona injection (DAN/AIM) — *expected failure*, included as baseline | [WH-010-persona.md](WH-010-persona.md) |
| WH-EXTRA | Medium-High | `_extract_target` returns only first field — secret in `data` shadowed by benign `url` | [WH-EXTRA-extract-target.md](WH-EXTRA-extract-target.md) |

Plus a methodology bonus:

| ID | Type | Title | File |
|---|---|---|---|
| WH-M1 | Architecture | Substring-based ultra_dangerous_patterns is brittle | [WH-M1-substring-guardrail.md](WH-M1-substring-guardrail.md) |
| WH-M2 | Architecture | Predicate gap: `sk_live_test_*` lacks both predicate guard AND coverage | [WH-M2-predicate-gap.md](WH-M2-predicate-gap.md) |

---

## Overall Recommended Mitigations (executive summary)

The most leveraged single change a defender can make is **input Unicode normalization** before guardrail evaluation:

```python
import unicodedata

def normalize_for_guardrail(s: str) -> str:
    s = unicodedata.normalize("NFKC", s)
    # Strip bidi controls
    s = "".join(c for c in s if unicodedata.category(c) not in ("Cf",))
    # Optional: collapse case + simple leet
    return s.lower().translate(str.maketrans("4310578", "aeiosbt"))
```

Applying `normalize_for_guardrail` to any string passed into `OptimalGuardrail._extract_target()` eliminates findings WH-001 (zero-width), WH-002 (RTL), and WH-009 (leet) in one patch.

The remaining findings (WH-003/004/005/006/007) require **intent-based** rather than substring-based guarding — see each finding's mitigation section for the LLM-classifier-of-last-resort recommendation.

---

## Out-of-Scope (Reported for Awareness)

The following architectural observations are *not* attacks — they are design notes for the next version of the harness:

- The 8-string `ultra_dangerous_patterns` set is sensitive to typos and encoding. A semantic classifier would be more robust.
- The `_contains_secret` predicate hard-codes `sk_live_test` as the canonical leak target. A real product would rotate this token periodically.
- The `recent_sources` taint window of 2 steps may be too short for long agent chains.

---

## Researcher Contact

For follow-up questions about any finding, the chain corpus is committed at:
`D:/project/comp/cyberlab/lab/chains/`

Each finding cross-references the source bank file plus the exact `chain_id` in the cyberlab findings database.
