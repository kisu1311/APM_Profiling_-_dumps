# Handoff — 2026-07-20 14:31

## Read first
See **CLAUDE.md** — especially **Structure** (the 5 deliverables + the shared switcher, now `1·2·3·4 + Notes`) and **Key context** (the doc-grounded data model: what the live product provides vs. what needs a profiler). Two new in-product files were added this session: `apm-profiling-dynatrace.html` and `apm-profiling-datadog.html`.

## What we worked on this session
Restored the user's original `index.html` design, then added **two new in-product reference-design deliverables** (Dynatrace + Datadog) built into the real APM chrome and grounded in the live services, and finally made the Dynatrace option **honest about data availability** using the Motadata APM docs.

## Completed
- **Restored `index.html`** to its original single Motadata-native profiling design. A previous session had replaced it with an inline 3-option "REFERENCE DESIGN" switcher (Datadog/Dynatrace/Motadata-native); that change is safely in a **git stash** (`git stash list` → "3-option REFERENCE DESIGN switcher…") **and** a scratchpad backup. `index.html`'s only tracked change now is the switcher gaining options 3 & 4.
- **`apm-profiling-dynatrace.html`** (switcher option **3**) — in-product **Dynatrace-modelled** flow (process-centric): overview → details (split-by + 4-signal tabs + method hotspots/analyze outliers) → top contributors → analysis view. Grounded in live services. Includes a **feasibility banner** and, added this session, a **doc-grounded data-availability badge system**: green "Live in APM" (Response time=Latency, Failure rate=Error Rate, Throughput; Analyze outliers="Uses existing traces"), orange "Requires profiler" (CPU consumption, CPU-in-GC, Consumed CPU, method hotspots, per-request CPU, analysis-view CPU-avg). The **CPU signal tab shows a locked state** instead of a fake chart. All verified with headless screenshots.
- **`apm-profiling-datadog.html`** (switcher option **4**) — in-product **Datadog-modelled** flow (flame-graph-first): overview = "Select service to view flame graph" (expandable service rows → attached processes + "View Flame Graph") → details with View tabs (Flame Graph · Thread Timeline · Metrics · Profile List · Call Graph) + metric selector + endpoint/function side panel. Reuses `index.html`'s flame machinery; flame data reground to Java (javaDistributed) + Spring endpoints; feasibility banner added. Verified (overview, expand, flame details).
- **Switcher updated in all 5 files** to `1 In-product · 2 Prototype · 3 Dynatrace · 4 Datadog · 📄 Notes`, each highlighting its own entry.
- **Browsed the live APM** (`192.0.2.71/apm/services` — real internal IP in session memory) to confirm current state (build 8.2.6; tabs Services·Explorer·Error Tracker·Compare; real data) and **read the Motadata APM docs** to derive the provided-vs-not data model. Saved both to session memory (`live-apm-has-data`, `motadata-apm-data-model`).

## In progress
Nothing mid-flight. All five files render and are cross-linked.

## Next steps
- Optional: apply the same **live-vs-profiler availability badges** to `apm-profiling-datadog.html` and `index.html` (currently only the Dynatrace option has them).
- Optional: add a **Dumps** pillar (thread/heap/crash) to the in-product flows — none of the three in-product files have it yet (only the standalone prototype does).
- Optional: a "**Open in Profiling**" hop from the Services/Explorer tabs.
- When ready to share, run `/publish` (the two new files deploy automatically on push).

## Decisions made
- **New reference designs are separate in-product files (options 3 & 4), not an inline toggle** inside `index.html` — the user explicitly removed the inline 3-option bar and wanted their original `index.html` preserved. Each paradigm gets its own deliverable in the real chrome.
- **Grounded the new flows in the live services** (javaDistributed, nodeDistributedApp, PHPApp, NodeApp, DotnetApp, pythonDistributedApp) per the user's "ground it in the live product" instruction, rather than the demo names in the source PDFs.
- **Datadog flow reuses `index.html`'s existing flame-graph machinery** (its flame workspace already matched the Datadog View-tabs + metric-selector structure 1:1) rather than rebuilding it.
- **Data honesty via badges** — after reading the docs, marked CPU/method-level data as "Requires profiler · not in 8.2.6" and gave the CPU tab a locked state, instead of presenting invented CPU numbers as if live.

## Gotchas & notes
- **The 3-option reference-design version of `index.html` lives only in a git stash + scratchpad backup** — if the user ever wants it back, `git stash list` / `git stash pop` (or the scratchpad copy). It is NOT in tracked history.
- The Dynatrace/Datadog files were built by **copying `index.html`** then splicing/replacing the `#pfBody` markup + the profiling IIFE. When editing, re-`Read` immediately before `Edit` (Prettier reformats HTML in the IDE).
- **`pf-bar` collapses in table cells** unless given an explicit `width` (it only auto-sizes inside flex contexts) — set `display:inline-block;width:NNpx` on bars placed in `<td>`s.
- Headless Chrome: **do not use `--virtual-time-budget`** here (the agentation dev widget + animations make it hang); plain `--screenshot` works. To capture an in-app view, python-inject a `<script>…click()…</script>` before `</body>` using a **literal** `</script>`.
- Real internal IP is in session memory only — **keep it scrubbed** to `192.0.2.71` in any committed/published file (public repo).
