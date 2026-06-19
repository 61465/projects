<div align="center">

# WhatsApp Cafe Bot

**Multi-Tenant WhatsApp Commerce Bot for Local Stores**
**بوت تجاري واتساب متعدد المستأجرين للمتاجر المحلية**

![Stack](https://img.shields.io/badge/stack-Baileys%20%2B%20Node-success) ![Tenancy](https://img.shields.io/badge/tenancy-multi--tenant-blue) ![Port](https://img.shields.io/badge/port-3003-purple)

</div>

> ⚠️ Public showcase only. Production credentials, customer DBs, WhatsApp session files, and the staging environment configuration remain private.

---

## What It Is

A multi-tenant WhatsApp commerce bot built for small Arabic-speaking businesses (cafés, restaurants, retail). Each tenant gets:

- An isolated WhatsApp session (its own QR + business number)
- An isolated database
- A JWT-protected admin panel
- A staging environment for safe menu / hours / promo changes before going live

The bot handles orders, menu inquiries, hours, location, and human-handoff when a question goes beyond its scope.

## Architecture

```
WhatsApp ───► Baileys multi-session bridge ───► Tenant router ───► Tenant handler
                                                    │
                                                    ├─► Menu service
                                                    ├─► Orders service
                                                    ├─► Human-handoff bridge
                                                    └─► Analytics

Admin web ───► JWT auth ───► Tenant API ───► Same services
```

Tenant isolation is enforced at the **session level** (separate Baileys auth dirs) and the **database level** (per-tenant SQLite files). One bad tenant cannot crash or read another tenant.

## Key Engineering Decisions

| Decision | Why |
|---|---|
| Baileys over WhatsApp Cloud API | No business-verification gate; works for any number; free |
| Per-tenant SQLite (not shared Postgres) | Hard tenant isolation; backup = copy file |
| Port 3003 (not 80/443) | Reverse proxy in front (Nginx/Traefik) handles TLS |
| JWT sessions (no server-side store) | Stateless admin API; easy horizontal scale |
| Staging environment | Owner can test "new ramadan menu" without breaking live orders |
| Plain-text WhatsApp UX | Voice notes + Arabic text — no buttons gated behind Business API |

## What It Handles

- **Menu browsing** — `قائمة` → categorized menu with prices
- **Order placement** — guided cart, address capture, total, ETA
- **Hours / location** — auto-replies for repetitive questions
- **Human handoff** — when the bot's confidence drops, owner is paged
- **Owner analytics** — daily orders, top items, peak hours

## Monitoring Stack (private)

The full production deployment has a parallel monitoring layer:

- Bot uptime + Baileys connection health
- Per-tenant order volume + error rates
- Session-watchdog (auto-reconnect on disconnect)
- Backup-cron for tenant DBs

## Status

- ✅ Multi-tenant routing working end-to-end
- ✅ JWT admin panel with role-based access
- ✅ Staging environment + safe-promote scripts
- ✅ Monitoring stack deployed on production VPS
- 📋 Next: payment integration (Mada / STC Pay) for Saudi market

## Author

**Abd Alrahman Mohamed** — solo design, implementation, and deployment.
abdarahman10555@gmail.com · github.com/61465
