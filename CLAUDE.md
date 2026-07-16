**On session start:** If `HANDOFF.md` exists in this directory, read it before anything else for the latest state of the work.

# APM · Profiling & Dumps

## What this is
A set of **three connected, self-contained HTML deliverables** proposing and demonstrating a new **Profiling & Dumps** capability for the **Motadata ObserveOps APM** module (code-level CPU/memory/IO profiling, flame graphs, method hotspots, thread/heap/crash dumps). One is a research/strategy document, two are clickable UI prototypes. They cross-link via a shared switcher so a reviewer can move between the "why" (notes) and the "what" (interfaces).

## Tech stack
- Plain **HTML + inline CSS + inline vanilla JS** — no build system, no dependencies except Google Fonts (Inter + JetBrains Mono).
- Charts, flame graphs, donuts, and timelines are **hand-rolled** (seeded PRNG `rng()`, SVG chart renderer, recursive flame-graph renderer). No chart libraries.
- Dark theme matching the live product tokens: bg `#07101f`, card `#0b1627`/`#172336`, border `#1d2a3e`, teal accent `#14b8a6`/`#0d9488`, severity `#ec5b5b/#f47c22/#e8b407/#14b053`.

## Structure
Three files in this folder (all open directly in a browser):
- **`index.html`** — the **primary** deliverable. The Profiling module built *into* the real live-8.2.6 Motadata chrome (this file was a copy of the **Side_bar_menu** shell). Boots into `APM ▸ Profiling`; real APM tab bar (Services · Explorer · Error Tracker · Compare · **Profiling NEW**); full flow: continuous-profile list → service/process detail → method hotspots → interactive flame-graph workspace → compare. All profiling additions are `pf-`prefixed (styles + an isolated IIFE exposing `window.initProfiling`) so they don't collide with the shell.
- **`apm-profiling-dumps-prototype.html`** — standalone hi-fi clickable prototype (own dark theme + pillar nav). Same flow **plus a Dumps pillar** (thread/heap/crash) and a differential Compare. Has a floating "Flow" guided-tour map.
- **`apm-profiling-dumps-redesign.html`** — the **Notes**: a 12-section research/strategy design doc (current Motadata flow + problems, Datadog/Dynatrace analysis with embedded PDF screenshots, gap analysis, new IA, user/screen/nav flows, dashboard strategy, multi-pane design, low-fi wireframes, final recommendation).

All three share a fixed bottom-center **deliverable switcher** (`#deliv-switch`): two numbered options **1 In‑product** (index.html) · **2 Prototype**, plus a separate **📄 Notes** link (the redesign doc). Each file highlights its own entry.

## How to run
No build. Open any file in a browser, or from this folder:
```bash
open index.html
```
Screenshot-verify with headless Chrome (paths have spaces — %20-encode):
```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu --hide-scrollbars --window-size=1500,950 \
  --screenshot=/tmp/out.png \
  "file:///Users/kishanpatel/ObseverOps/APM%20Profiling%20&%20dumps/index.html"
```
To screenshot a specific in-app view, python-inject a `<script>` before `</body>` that `.click()`s the target (e.g. `#pfListBody tr` then `#pfv-detail [data-goto='flame']`). Use a **literal** `</script>` — a `<\/script>` escape silently breaks the injected block.

## Key context
- **Grounded in real research** (not invented): the live Motadata APM docs, the Datadog Continuous Profiler docs, the Dynatrace Profiling & Optimization docs, and the user's own annotated "APM Profiling & dumps" PDF spec. Current real Motadata APM has **no** code-level profiler, flame graphs, dumps, or deployment tracking — that's the gap these fill.
- **Live product facts** were sampled by browsing `https://192.0.2.71/apm/services` (needs the user's authed Chrome session): current APM tabs = Services · Explorer · Error Tracker · Compare; instance currently shows "No data found"; product uses Inter font + the token palette above.
- Audience is **designers/PMs**, not only engineers — keep prototypes visual and self-explanatory.
- Repo convention (see repo-root `../CLAUDE.md`): every deliverable is one self-contained `.html`; dark + teal Motadata theme; tokens redeclared per file.

## Handoff
Latest session state is in [HANDOFF.md](HANDOFF.md) — read it first.
