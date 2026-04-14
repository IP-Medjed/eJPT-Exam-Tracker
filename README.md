# eJPT Exam Tracker v4

A zero-dependency, single-file tactical dashboard built for the [INE eJPT](https://security.ine.com/certifications/ejpt-certification/) penetration testing certification exam. Runs entirely in your browser — no install, no build step, no server. Open the HTML file and go.

Built for speed under pressure. Every feature exists to reduce cognitive load and eliminate mechanical overhead during a timed 48-hour exam.

---

## Quick Start

```bash
# Clone it
git clone https://github.com/IP-Medjed/ejpt-exam-tracker.git

# Open it
# Double-click ejpt-exam-tracker-v4.html in any modern browser
# Or:
xdg-open ejpt-exam-tracker-v4.html        # Linux
open ejpt-exam-tracker-v4.html             # macOS
start ejpt-exam-tracker-v4.html            # Windows
```

No dependencies. No npm. No build. One file, 105KB, works offline.

---

## Screenshots

> *Dark mode (default) and Red/Night mode for late-night sessions*

---

## Features

### Core Tracking

| Tab | What It Does |
|-----|-------------|
| **MAP** | Interactive canvas network map with drag-to-move nodes, click-to-connect edges, subnet grouping, role tags (WEB/DB/DC/PIVOT), privilege indicators (USR/ADM/ROOT/SYS), and full zoom/pan support |
| **⬇ IMPORT** | Paste raw Nmap (standard + grepable `-oG`), ARP (`arp -a`, `ip neigh`), and route table output → auto-populates targets, map nodes, and subnets in one click |
| **TARGETS** | IP/hostname/OS/status per target, open ports with service hints, per-target enum checklist, time-on-target timer, and kill chain phase tracker |
| **🔫 SPRAY** | Credential × target spray matrix with ✓/✗/? toggles, coverage percentages, and bidirectional sync to credential "Works on" fields |
| **CREDS** | Credential cards with auto hash identification (20+ hash types) and instant hashcat `-m` mode display |
| **FLAGS** | Question/answer tracking with confidence levels and auto-lock on confirmed answers |
| **PIVOTS** | Pivot session tracker with method-specific warnings (autoroute vs portfwd vs socks_proxy) and a persistent session health check reminder |
| **CMD LOG** | Timestamped command + result journal |
| **⚡ CMDS** | 34 command templates with placeholder auto-fill from selected targets/creds, plus live-generated pivot commands from your current map state |
| **NOTES** | Freeform scratchpad |
| **CHECKS** | Pre-action decision framework + reference checklists (Linux post-ex, HTTP enum order, SQLMap stages, pivot rules) |

### Exam-Day Features

- **48-Hour Exam Timer** — Countdown with start/pause/reset. Pulses red below 25% remaining.
- **Progress Bar** — Header bar showing owned/in-progress/enumerated ratio at a glance.
- **Kill Chain Tracker** — Per-target phase progression: Recon → Initial Access → Foothold → Priv Esc → Loot. Click to cycle states.
- **Live Pivot Commands** — Auto-generates `autoroute`, `portfwd`, and OS-specific `bind_tcp` payloads from your map subnets and active pivot sessions. One-click copy.
- **Recon Parser** — Paste Nmap/ARP/route output, get structured targets with port hints, service color tags, and enum checklists. Supports both standard (`-oN`) and grepable (`-oG`) Nmap formats.
- **Hash Auto-ID** — Paste a hash into a credential card and instantly see the type + hashcat mode: phpass, sha512crypt, bcrypt, NTLM, NTLMv2, MySQL-SHA1, Drupal7, yescrypt, argon2, MD5, SHA1, SHA256, SHA512, and more.
- **Credential Spray Matrix** — Track every credential against every target. ✓ auto-populates "Works on" in the cred card. Changing to ✗ or ? removes it. Event-delegated for performance at scale.

### Quality of Life

- **Dark + Red/Night Theme** — Toggle between cool dark and warm red mode. Map canvas colors adapt per theme for eye comfort during extended sessions.
- **Global Search** — Ctrl+F highlights matching cards across all tabs.
- **Keyboard Shortcuts** — `1-0` switch tabs, `N` quick-add, `Ctrl+S` save, `Ctrl+Z` undo, `?` shortcuts overlay.
- **Undo Delete** — Ctrl+Z restores accidentally deleted cards. 20-deep stack with 8-second toast notification.
- **Markdown Report Export** — One-click `.md` report with summary, targets (including kill chain), credentials table, flags, pivots, and full command log.
- **JSON Import/Export** — Full state backup and restore. Auto-saves to localStorage every 30 seconds.
- **Reset for New Exam** — Double-confirmed full data wipe. Preserves theme preference.
- **Debounced Inputs** — Text fields update state instantly but defer save/render operations to prevent lag on large datasets.
- **Storage Quota Protection** — Alerts on localStorage limit (5MB) with one-per-minute rate limiting to prevent alert loops.

### Map Features

- **Zoom/Pan** — Scroll to zoom (0.2x–5x), middle-click/right-click/shift+drag to pan
- **Fit All** — Auto-zoom to fit all nodes after import or sync
- **Subnet Grouping** — Dashed boundary boxes auto-drawn around nodes sharing a subnet
- **Role Tags** — KALI, PIVOT, WEB, DB, DC, FILE, MAIL, GW, WS
- **Privilege Indicators** — USR, ADM, ROOT, SYS badges on nodes
- **Hover Tooltips** — Full port list with service hints, rendered in screen space (readable at any zoom)
- **Edge Labels** — Connection method labels (autoroute, portfwd, exploit, credentials, lateral)
- **Port Count Badge** — Per-node indicator of open port count
- **Sync from Targets** — One-click sync of target list to map nodes

---

## In-Scope Tools (eJPT Curriculum)

This tracker is scoped to the INE eJPT course curriculum. The command templates and checklists cover:

Nmap, Metasploit/Meterpreter, SQLMap, Hydra, dirb/gobuster, enum4linux, smbclient, Burp Suite, FTP, SSH, MySQL client, John the Ripper, hashcat, netcat, curl, WPScan, Nikto, msfvenom, xfreerdp

Buffer overflows are out of scope for the eJPT.

---

## Architecture

| Property | Value |
|----------|-------|
| **Dependencies** | Zero. Pure HTML/CSS/JS. |
| **File count** | 1 |
| **Size** | 105KB |
| **Storage** | localStorage (auto-save) + JSON export |
| **Framework** | None. Vanilla JS with manual DOM rendering. |
| **Font** | JetBrains Mono (Google Fonts CDN, graceful fallback) |
| **Browser support** | Any modern browser with Canvas + localStorage |
| **XSS protection** | All user input escaped via `esc()` before HTML injection |
| **Event handling** | Delegated listeners on spray matrix; debounced text inputs |

### Design Decisions

- **Single file** — Opens anywhere. No build, no install, no server. Drag into a VM, open in a browser, done.
- **localStorage + JSON export** — Survives page refresh. JSON export survives VM crashes. No network dependency.
- **Canvas map** — Infinite pan/zoom without DOM overhead. Nodes can go anywhere, no layout constraints.
- **No framework** — React/Vue would add build complexity for zero gain in a single-page local tool.
- **Debounced saves** — State updates instantly on keystroke, but localStorage writes and stat recalculations are debounced 300ms to prevent jank.
- **`fiChange` helper** — Fields that trigger re-render (ports, hashes, flag answers) bind to `change` event instead of `input` to prevent focus loss during typing.

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `1` – `9`, `0` | Switch tabs (MAP through NOTES) |
| `N` | Quick-add entry to current tab |
| `Ctrl+S` | Manual save |
| `Ctrl+F` | Focus search bar |
| `Ctrl+Z` | Undo last delete |
| `?` | Show shortcuts overlay |
| `Esc` | Close overlays/modals |

---

## Data Flow

```
Nmap/ARP/Route output
        │
        ▼
   ⬇ IMPORT tab ──→ parseNmap() / parseNmapGrep() / parseArp() / parseRoute()
        │
        ▼
   TARGETS + MAP auto-populated
        │
        ├──→ Enum checklist per target (port-aware)
        ├──→ Kill chain tracker per target
        ├──→ Service tags + port hints
        │
        ▼
   CREDS tab ──→ Hash auto-ID ──→ hashcat mode badge
        │
        ▼
   🔫 SPRAY matrix ──→ ✓ auto-syncs to cred "Works on"
        │                 ✗/? auto-removes from "Works on"
        │
        ▼
   ⚡ CMDS tab ──→ Live pivot commands from map subnets + active sessions
        │            Static templates with target/cred auto-fill
        │
        ▼
   MD RPT export ──→ Structured markdown report
```

---

## Credits

**Built by:**
- **[awcl](https://github.com/awcl)**
- **[IP-Medjed](https://github.com/IP-Medjed)** 
- **[Architect Claude](https://claude.ai)** (Anthropic)
  
**Code reviewed by:**
- **[awcl](https://github.com/awcl)**
- **Gemini** (Google)
  
---

## Version History

| Version | Highlights |
|---------|-----------|
| **v1** | Original exam tracker — targets, creds, flags, cmd log, notes, basic canvas map |
| **v2** | Added spray matrix, recon parser, per-target enum checklist, hash auto-ID, service tags |
| **v3** | Canvas map overhaul (roles, privilege levels, subnet grouping, tooltips), decision framework checklists |
| **v4** | Exam timer, kill chain tracker, command templates with live pivot gen, zoom/pan map, dark/red themes, global search, markdown export, undo system, keyboard shortcuts, progress bar, grepable Nmap, debounced inputs, event delegation, storage quota protection, fiChange focus-loss fix, parse failure diagnostics, bidirectional spray sync |

---

## License

BSD 3-Clause

---

*Built for the exam. Tested under pressure. Zero dependencies, zero excuses.*
