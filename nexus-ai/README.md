<div align="center">

# NEXUS-AI

**46-Agent Multi-Tier AI Orchestration Framework**
**نظام تنظيم 46 وكيل ذكاء اصطناعي عبر 10 مستويات**

![Agents](https://img.shields.io/badge/agents-46-blue) ![Tiers](https://img.shields.io/badge/tiers-10-green) ![Interface](https://img.shields.io/badge/interface-MCP%20server-purple) ![Uptime](https://img.shields.io/badge/uptime-24%2F7%20fallback-success)

</div>

> ⚠️ Public showcase only. Full orchestrator, prompts, vault, and proprietary tier-routing logic are private.

---

## What It Is

NEXUS-AI is a hierarchical orchestration layer that turns a single Claude Code conversation into a 46-agent virtual company. It exposes a Model Context Protocol (MCP) server so any MCP-aware client (Claude Code, Claude Desktop, custom apps) can delegate tasks to the appropriate agent automatically.

The design treats LLMs as employees, not endpoints:

- **Maestro** (Tier 1) decides what kind of work this is and which tier to engage.
- **Architect / PM** (Tier 1) plan the work before any code-writing tier starts.
- **Specialized devs / designers / writers / marketers / security agents** (Tier 2–10) execute.
- **Memory Manager** persists what was learned across sessions.

## 10 Tiers, 46 Roles

| Tier | Theme | Sample Roles |
|---|---|---|
| 1 | Command & Control | Maestro · Architect · Project Manager |
| 2 | Development | Frontend · Backend · Mobile · Database · DevOps · API Connector |
| 3 | Quality & Security | Code Reviewer · Test Engineer · Debugger · Pen Tester · DevSecOps · Vulnerability Hunter · Guardian · Prompt Guardian |
| 4 | AI / Data | RAG Specialist · Algorithm Expert · Model Optimizer · Data Pipeline · Explainability · Performance Optimizer · Memory Manager |
| 5 | Research & Refactor | Research Agent · Refactor King · Documentation Writer · Crypto Specialist |
| 6 | Brand & UX | Brand Designer · Graphic Designer · 3D Designer · Illustrator · Motion Designer |
| 7 | Long-Form Writing | Book Author · Content Writer · Copywriter · Scriptwriter · PDF Writer |
| 8 | Marketing & Growth | Marketing Strategist · Growth Hacker · SEO Specialist · Social Media Manager · Community Manager |
| 9 | Media Production | Video Producer · Alert Filter |
| 10 | Meta | Prompt Engineer · Maestro feedback loop |

## Multi-Model Routing

Different agents call different models. Routing is **role-aware**, not round-robin.

| Provider | Model | Used For |
|---|---|---|
| Groq | `openai/gpt-oss-120b` | Maestro (high-level planning) |
| Groq | `meta-llama/llama-4-scout-17b-16e-instruct` | Architect (system design) |
| Groq | `llama-3.3-70b-versatile` | PM, generalists |
| Groq | smaller models | high-frequency low-stakes roles |

On quota exhaustion / HTTP 429 the dispatcher falls back to the nearest-capable model automatically — the user never sees the company go offline.

## MCP Server

```
nexus_mcp_server.py  ──►  exposes 46 tools (one per agent) over MCP
nexus_cli.py         ──►  human REPL for direct calls
ai_orchestrator.py   ──►  Maestro's task distributor
core/vault.py        ──►  encrypted secret storage for tools
core/runner.py       ──►  parallel execution sandbox
core/news.py         ──►  external-information gathering
```

Any MCP-capable client (Claude Code, Claude Desktop) can register NEXUS-AI and immediately get 46 specialized assistants.

## Doctor / Nurses Model

A core design principle (from internal feedback memory):

> **NEXUS-AI agents are nurses. The human operator is the doctor.**
> Agents gather, summarize, and propose. The human decides.

This is enforced by:
- Agents return *findings + proposals*, not unilateral commits.
- Maestro never auto-executes destructive tools without an explicit `approve` step.
- Memory writes are advisory and require an `accept` from the operator.

## Why This Matters

- **Token economy** — heavy synthesis is offloaded to cheaper agents in parallel, keeping the main conversation small.
- **Trust-but-verify by construction** — every agent output is reviewable in isolation.
- **Tier separation** — bug fix in code-writing tier doesn't touch the planning tier.
- **24/7 fallback** — multi-provider routing means a single API outage doesn't take the company down.

## Status

- ✅ 46 agent definitions complete with model + prompt + capabilities
- ✅ MCP server registered and callable from Claude Code
- ✅ Vault + runner + parallel execution working
- 🔄 Tier 7–10 (writers, designers, media, marketing) recently added
- 📋 Next: web dashboard for visualizing the company's live workload

## Author

**Abd Alrahman Mohamed** — solo design and implementation.
abdarahman10555@gmail.com · github.com/61465
