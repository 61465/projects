<div align="center">

# sentinel-cli

**Headless Terminal Companion of SENTINEL Enterprise**
**النسخة الطرفية الخفيفة من جناح حماية SENTINEL**

![Mode](https://img.shields.io/badge/mode-headless-blue) ![Platform](https://img.shields.io/badge/platform-Windows%20%2F%20Linux-purple)

</div>

> ⚠️ Public showcase only. Detection rules and packaged binary stay private.

---

## What It Is

A no-GUI counterpart of [SENTINEL Enterprise](../shild/_github_showcase/) for headless servers, VPS instances, and CI runners. Same detection core, exposed as a CLI.

Use cases:
- A VPS that hosts the Thawani / WhatsApp Cafe Bot production stack
- A CI runner that should refuse to execute if compromise indicators appear
- A developer workstation that doesn't need the desktop tray UI

## Commands (selected)

```
sentinel-cli scan            # one-shot scan of the system
sentinel-cli watch           # long-running daemon mode
sentinel-cli rules list      # show active correlation rules
sentinel-cli rules import    # load a JSON rule pack
sentinel-cli playbook test   # dry-run a SOAR playbook
sentinel-cli report --json   # machine-readable findings
```

## Why Separate From the GUI Build

- **No Win32 dependencies** — runs on Linux servers and headless Windows
- **Container-friendly** — single binary, configurable via env vars
- **CI-friendly** — exit code reflects severity for pipeline gates
- **Lower footprint** — same detection power without the UI cost

## Status

- ✅ Daemon mode (`watch`) tested over 30-day uptime windows
- ✅ JSON output schema stable
- ✅ Compatible with SENTINEL Enterprise rule packs
- 📋 Next: native Linux service integration (systemd unit)

## Author

**Abd Alrahman Mohamed** — abdarahman10555@gmail.com · github.com/61465
