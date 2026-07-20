**On session start:** If `HANDOFF.md` exists in this directory, read it before anything else for the latest state of the work.

# APM · Profiling & Dumps

## What this is
A set of **five connected, self-contained HTML deliverables** proposing and demonstrating a new **Profiling & Dumps** capability for the **Motadata ObserveOps APM** module (code-level CPU/memory/IO profiling, flame graphs, method hotspots, thread/heap/crash dumps). One is a research/strategy document; the rest are clickable UI prototypes — including **three in-product reference designs** (Motadata-native, Dynatrace-modelled, Datadog-modelled) each built into the real APM chrome. They cross-link via a shared switcher so a reviewer can move between the "why" (notes) and the "what" (interfaces).

## Tech stack
- Plain **HTML + inline CSS + inline vanilla JS** — no build system, no dependencies except Google Fonts (Inter + JetBrains Mono).
- Charts, flame graphs, donuts, and timelines are **hand-rolled** (seeded PRNG `rng()`, SVG chart renderer, recursive flame-graph renderer). No chart libraries.
- Dark theme matching the live product tokens: bg `#07101f`, card `#0b1627`/`#172336`, border `#1d2a3e`, teal accent `#14b8a6`/`#0d9488`, severity `#ec5b5b/#f47c22/#e8b407/#14b053`.

## Structure
Five files in this folder (all open directly in a browser). The three in-product files (`index`, `dynatrace`, `datadog`) all reuse the **same real APM chrome** — each is a copy of `index.html`'s live-8.2.6 Side_bar_menu shell, boots into `APM ▸ Profiling`, keeps the real APM tab bar (Services · Explorer · Error Tracker · Compare · **Profiling NEW**), and swaps only the `#pfBody` profiling flow (all `pf-`prefixed styles + an isolated IIFE exposing `window.initProfiling`, so nothing collides with the shell). Non-profiling APM tabs show a "mirrors live current state" stub.
- **`index.html`** — the **primary** deliverable. The **Motadata-native** profiling design: continuous-profile list → service/process detail (metric tabs + a **"Child processes & attached services"** grid keyed to the selected service via `PROCMAP` — shows which process/PID runs the service and which service it's attached to) → method hotspots → interactive flame-graph workspace → compare.
- **`apm-profiling-dynatrace.html`** — **in-product Dynatrace-modelled** flow (process-centric): overview (CPU chart + process grid) → details (split-by chart + child processes + 4-signal tabs Response time/Failure rate/CPU/Throughput) → method hotspots & analyze outliers → top contributors → create-analysis-view. Has a **doc-grounded data-availability badge system** (green "Live in APM" = Latency/Error Rate/Throughput/traces; orange "Requires profiler" = CPU consumption/GC/method hotspots/per-request CPU; the CPU signal tab shows a *locked* state, not a fake chart).
- **`apm-profiling-datadog.html`** — **in-product Datadog-modelled** flow (flame-graph-first): overview = "Select service to view flame graph" (expandable service list + View Flame Graph) → details with View tabs (Flame Graph · Thread Timeline · Metrics · Profile List · Call Graph) + metric selector (CPU Time · Wall Time · Allocated Memory · Live Heap · Lock · Socket I/O · Exceptions) + endpoint/function side panel. Reuses `index.html`'s flame machinery wholesale.
- **`apm-profiling-dumps-prototype.html`** — standalone hi-fi clickable prototype (own dark theme + pillar nav). Same flow **plus a Dumps pillar** (thread/heap/crash) and a differential Compare. Its process detail shows three grids — **Child processes & attached services**, **Top services**, and **Top contributors** (methods/endpoints). Has a floating "Flow" guided-tour map.
- **`apm-profiling-dumps-redesign.html`** — the **Notes**: a 12-section research/strategy design doc (current Motadata flow + problems, Datadog/Dynatrace analysis with embedded PDF screenshots, gap analysis, new IA, user/screen/nav flows, dashboard strategy, multi-pane design, low-fi wireframes, final recommendation).

All five share a fixed bottom-center **deliverable switcher** (`#deliv-switch`): four numbered options **1 In‑product** · **2 Prototype** · **3 Dynatrace** · **4 Datadog**, plus a separate **📄 Notes** link (the redesign doc). Each file highlights its own entry — to add/reorder, edit the switcher block in **every** file (it's duplicated per file).

The Dynatrace & Datadog in-product flows are grounded in the **live services** (javaDistributed, nodeDistributedApp, PHPApp, NodeApp, DotnetApp, pythonDistributedApp); each carries a top-of-page **feasibility banner** stating what fits the current product vs. what needs a new profiler backend.

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
- **Live product facts** were sampled by browsing the live APM instance (needs the user's authed Chrome session; real internal IP is in the session memory, scrubbed to `192.0.2.71` for this public repo): build **8.2.6**, APM tabs = Services · Explorer · Error Tracker · Compare, and the instance now has **real data** (6 services above). Product uses Inter font + the token palette above.
- **Doc-grounded data model** (per `https://docs.motadata.com/motadata-aiops-docs/apm/overview`): the current product provides **Latency, Error Rate, Throughput, Trace Duration, Span Count, Dependency Health** + Explorer traces/spans, Error Tracker, Compare. It has **no** CPU/process profiling, memory/GC, method-level data, flame graphs, or dumps — so prototypes mark data as **Live in APM** vs. **Requires profiler** (the `apm-profiling-dynatrace.html` badge system). Apply this live-vs-profiler split when grounding any option.
- Audience is **designers/PMs**, not only engineers — keep prototypes visual and self-explanatory.
- Repo convention (see repo-root `../CLAUDE.md`): every deliverable is one self-contained `.html`; dark + teal Motadata theme; tokens redeclared per file.

## Deployment
Repo: https://github.com/kisu1311/APM_Profiling_-_dumps
Live URL: https://kisu1311.github.io/APM_Profiling_-_dumps/
Static site (index.html at root) → GitHub Pages via `.github/workflows/deploy.yml` (source = GitHub Actions). Redeploys on every push to `main`. Published without `gh` (not installed) — used git + the GitHub REST API with the osxkeychain token; a future `/publish` can just commit & push.
Note: internal IPs/hostnames are scrubbed to RFC5737 documentation ranges (`192.0.2.x` / `198.51.100.x`) and `example.net` before publishing — keep it that way (this is a public repo).

## Handoff
Latest session state is in [HANDOFF.md](HANDOFF.md) — read it first.
