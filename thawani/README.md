<div align="center">

# Thawani — WhatsApp Commerce Platform (Showcase)

**Multi-Tenant AI-Powered WhatsApp SaaS for MENA SMBs**
**منصة تجارة واتساب متعددة المستأجرين بالذكاء الاصطناعي للشركات الصغيرة**

![Version](https://img.shields.io/badge/version-v5.2-blue) ![Region](https://img.shields.io/badge/market-Saudi%20%2F%20MENA-success) ![Hardened](https://img.shields.io/badge/security-hardened-orange)

</div>

> ⚠️ Production source, customer data, and live deployment remain private. This folder is the **landing page** + a sanitized writeup. Live demo: https://61465.github.io/thawanidemo/

---

## What It Is

Thawani is a multi-tenant WhatsApp commerce platform deployed for Arabic-speaking businesses across MENA. A single business can run its full storefront — menu, orders, invoicing, customers, analytics — through a WhatsApp bot plus an Arabic-first admin SPA delivered inside WhatsApp's in-app browser.

The platform adapts: a café tenant sees "menu items", a salon tenant sees "services", a dev shop sees "packages" — the data model is the same; the terminology auto-reconfigures.

## Stack

- **Backend:** Node.js + Express + Baileys + SQLite (WAL mode + maintenance cron)
- **Frontend:** Arabic-first SPA, delivered through WhatsApp's in-app browser, PWA-installable
- **AI:** Claude API for intent parsing (order extraction, FAQ matching)
- **Auth:** JWT sessions, replay-proof 2FA
- **Notifications:** Web Push + WhatsApp message-back

## Production Hardening (v5.2)

| Pass | Result |
|---|---|
| Critical vulns closed | **6** |
| High vulns closed | **13** |
| 2FA flow | replay-proof (one-time code binding + timing-safe compare) |
| Backup cadence | hourly DB snapshot, daily offsite |
| Monitoring layers | **8** (bot-monitor, backup-cron, error-monitor, session-watchdog, health-monitor, maintenance-alerts, PM2 logs, audit) |

## Deployment Topology

```
Local lab (port 3004) ──► VPS staging (`whatsapp-bot-staging`) ──► Production
                                                                       │
                                                                       └─► promote-prod scripts
                                                                            with auto-rollback
```

Every change gets exercised on staging first; the `promote-prod` script enforces a green health check + DB backup before flipping traffic, and rolls back automatically on health regression.

## UX Highlights

- **Owner onboarding** — scan QR, choose business type, menu auto-templated. Live in 5 minutes.
- **Customer cart inside chat** — no leaving WhatsApp, no app install.
- **Print-ready invoice** — for owners who still use thermal printers.
- **Mobile-first admin** — rebuilt 2026-06 (deleted 2 legacy CSS layers) for one-thumb operation.
- **Plans CRUD + auto-save** — owners edit pricing live without losing draft state.

## Subscription Tiers

| Plan | Price | Audience |
|---|---|---|
| 🌱 Basic | 80 SAR | Single-line stores |
| ⭐ Pro | 150 SAR | Most cafés / salons |
| 👑 Advanced | 250 SAR | Multi-location / high-volume |

Feature gating is centralized in `src/plans.js` with a `hasFeature()` predicate — no scattered if-else.

## Status

- ✅ v5.2 in production with real Saudi customers
- ✅ Security hardening pass complete (2026-06-12)
- ✅ Jun-15 session: 30+ commits — locking, cache, maintenance, plans CRUD, auto-save, invoice print, mobile rebuild
- ✅ Jun-17 session: 29 fixes + final cleanup, snapshot `STABLE-20260617-052047`
- 📋 Roadmap: payment integration (Mada / STC Pay), Cloud API migration, multi-language

## Live

- Landing: this repo, deployed via GitHub Pages
- Demo:    https://61465.github.io/thawanidemo/

## Author

**Abd Alrahman Mohamed** — solo founder, architect, developer, security engineer, operator.
abdarahman10555@gmail.com · github.com/61465
