<div align="center">

# SENTINEL Enterprise v2.0

**Windows Desktop Security Suite — SIEM + SOAR + ML Anomaly Detection**
**جناح حماية مكتبي للويندوز يدمج SIEM و SOAR وكشف الشذوذ بالتعلم الآلي**

![Platform](https://img.shields.io/badge/platform-Windows-blue) ![Edition](https://img.shields.io/badge/edition-Enterprise%20v2.0-orange) ![FP](https://img.shields.io/badge/internal%20FP-%3C2%25-success)

</div>

> ⚠️ Public showcase only. Detection rules, response playbooks, ML model weights, and the installer remain private.

---

## What It Is

SENTINEL is a desktop-resident security suite for Windows that consolidates three capabilities normally sold as separate enterprise products:

1. **SIEM** — log aggregation, search, correlation across Event Log, Sysmon, browser, and custom hooks
2. **SOAR** — automated response playbooks (kill process, quarantine file, revoke token, alert user)
3. **ML Anomaly Detection** — unsupervised baseline of normal user/process behavior; deviations trigger triage

The goal: give a single endpoint the same defensive depth a SOC analyst would build manually.

## Architecture

```
Event sources ─► Collector ─► Normalizer ─► Storage (SQLite + rotating logs)
                                                │
                                                ├─► Correlator (rule engine)
                                                ├─► ML Anomaly Detector
                                                └─► SOAR Dispatcher
                                                       │
                                                       ├─► kill / quarantine / revoke
                                                       └─► UI alert + user prompt
```

## Why a Desktop Suite (not cloud-only)

| Concern | Cloud-only EDR | SENTINEL |
|---|---|---|
| Works offline | ❌ | ✅ |
| Data leaves machine | ✅ | ❌ (local-first) |
| Per-seat pricing | Expensive | One install |
| Custom rules in Arabic | ❌ | ✅ |
| Sovereign deployment | ❌ | ✅ |

This makes SENTINEL especially relevant for **regional clients** uncomfortable with US/EU cloud SIEM.

## Detection Surface

- **Process tree anomalies** — child of a child of a normally-leaf process
- **Persistence attempts** — new autorun, scheduled task, service install
- **Credential access** — LSASS handle requests, browser credential reads
- **Lateral movement** — unusual SMB/WMI/PSExec patterns
- **Exfiltration** — large outbound on unusual ports / unusual destinations
- **Browser hijack** — extension install, homepage / search-engine change

## ML Layer

Unsupervised baseline learned from the first 7 days of operation:

- Process-graph embeddings (which processes spawn which)
- Inter-arrival time of user actions
- File-write rate distribution per app
- Network 5-tuple frequency

Deviations score against learned distributions. Internal tests: **<2% false-positive rate** on a normal developer workstation over a 30-day window.

## SOAR Playbooks

Response is configurable per rule:

- `alert` — UI toast only
- `prompt` — ask user before action
- `kill-process` — terminate offending process tree
- `quarantine` — move file to vault + revoke handles
- `revoke-token` — clear browser auth for matched origin
- `freeze-network` — drop all egress except whitelist

## Companion Project

A separate terminal-only counterpart exists as **sentinel-cli** — same detection core, no GUI, designed for headless servers.

## Status

- ✅ v2.0 Enterprise build packaged and tested on Windows 10/11
- ✅ 30+ correlation rules shipped, customizable via JSON
- ✅ ML baseline + drift detection
- ✅ SOAR playbook engine with safe-mode (alert-only)
- 📋 Next: Linux companion daemon, fleet management view

## Author

**Abd Alrahman Mohamed** — solo design, implementation, and detection-engineering.
abdarahman10555@gmail.com · github.com/61465
