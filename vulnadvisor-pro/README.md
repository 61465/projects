<div align="center">

# VulnAdvisor Pro v3

**Local Vulnerability Triage & Advisory Engine**
**محرك تحليل وتقييم الثغرات الأمنية محلياً**

![Version](https://img.shields.io/badge/version-v3-blue) ![Mode](https://img.shields.io/badge/mode-local%2Foffline-success)

</div>

> ⚠️ Public showcase only. Vulnerability database, scoring weights, and advisory templates remain private.

---

## What It Is

VulnAdvisor Pro is a local-first tool that turns a raw vulnerability list into an actionable triage plan. It is **not** a scanner — it ingests output from real scanners (Nessus, OpenVAS, custom feeds) and produces:

- A prioritized fix queue, scored by exploitability × asset-criticality × exposure
- An advisory document per vulnerability — what it is, why it matters here, how to fix
- A diff between two scans (regressions vs. progress)
- A risk-trend chart over time

## Why Local

- Vuln data is sensitive. Many orgs cannot send it to a SaaS triage tool.
- Offline analysis is required during incident response when egress is restricted.
- One purchase, no per-asset subscription.

## Inputs

- Nessus `.nessus` XML
- OpenVAS XML
- Generic CSV with `cve, host, severity, port, service`
- Manual additions via web UI

## Output

- HTML advisory bundle (printable)
- JSON for downstream automation
- CSV for management review
- Markdown for ticket creation

## Scoring Model

`Risk = Base_CVSS × Exploitability_Multiplier × Asset_Criticality × Exposure_Multiplier`

- **Exploitability** rewards public PoCs, KEV catalog presence, and recent in-the-wild activity
- **Asset criticality** comes from a per-asset config (DB server > intern's laptop)
- **Exposure** rewards internet-facing assets, demotes air-gapped ones

The final ranking is what an analyst should fix **first**, not just what's CVSS-highest.

## Status

- ✅ v3 packaged for Windows
- ✅ Multi-scanner ingest tested on real Nessus/OpenVAS outputs
- ✅ HTML / JSON / CSV / Markdown exporters
- 📋 Next: KEV auto-sync, Jira/Linear ticket creation, Linux build

## Author

**Abd Alrahman Mohamed** — abdarahman10555@gmail.com · github.com/61465
