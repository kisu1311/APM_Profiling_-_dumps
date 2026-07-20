# Handoff — 2026-07-20 15:10

## Read first
See **CLAUDE.md** — **Structure** (the 5 deliverables + shared switcher) and **Key context** (the doc-grounded data model). This session only refined the **service-detail grids** in `index.html` and `apm-profiling-dumps-prototype.html`; no new files, no structural changes.

## What we worked on this session
Fixed the mislabeled **"Top contributors · attached services + child processes"** grid — which showed methods/endpoints but never actual services or processes — in both the in-product `index.html` and the standalone prototype, so the service-detail view now properly shows **which process runs the service and which service it's attached to**.

## Completed
- **`index.html`** — replaced the mislabeled contributors grid with a proper **"Child processes & attached services"** grid (columns: Process · PID | Attached service | Technology | Host · Container | CPU · Cores | Throughput). It's **keyed to the selected service** via a new `PROCMAP` object (subtitle + rows change per service; the attached service the row belongs to is a green pill, others blue). Added a "How to read" note. Rows still click through to the flame graph. Verified with a screenshot (store-backend → puma ×2 + sidekiq).
- **`apm-profiling-dumps-prototype.html`** — same fix, plus completed the flow it had only promised: the process-detail view now renders **three clean grids** — (1) Child processes & attached services, (2) **Top services** (the "N services ▸ + top services" case: store-backend / store-backend-jobs with total-time / median-RT / requests), (3) **Top contributors** (methods/endpoints/queries, relabeled). Added `CHILDPROCS` + `TOPSVC` data and rewrote `renderContrib()`. Removed the out-of-place `Easytrade.longBuy · Service` contributor row (a cross-paradigm leftover) and the misleading subtitle. Fixed the collapsing Contribution bar (`.mbar` needed an explicit width). Verified with a screenshot.

## In progress
Nothing mid-flight. Both files render correctly.

## Next steps
- These two changes are **unpushed** — run `/publish` when ready to put them live (auto-redeploys on push to `main`).
- Optional (unchanged from last session): apply the live-vs-profiler availability badges to `datadog` + `index`; add a Dumps pillar to the in-product flows; add an "Open in Profiling" hop from Services/Explorer.

## Decisions made
- **Grid keyed to the selected service** (via `PROCMAP` in `index.html`) rather than one static list, so the detail actually reflects the service you clicked. Prototype uses fixed representative data (`CHILDPROCS`/`TOPSVC`) since its detail is a single walkthrough.
- **Prototype detail split into three separate grids** (processes+services / top services / contributors) instead of one mixed grid — matches the flow's own description ("1 service ▸ contributors only · N services ▸ + top services") and reads clearly.
- **Ruby-consistent demo data** — used store-backend's real process shapes (puma web workers + sidekiq worker), dropped the Dynatrace-flavored `Easytrade` row that didn't belong in this Ruby context.

## Gotchas & notes
- **Inline progress bars collapse in table cells** — both `pf-bar` (index/in-product files) and `.mbar` (prototype) are inline with only `min-width`, so a child `width:%` resolves to 0 unless the bar is inside a flex row OR given an explicit `display:inline-block;width:NNpx`. Hit this again this session on the prototype's Contribution column.
- When editing these HTML files, **re-`Read` immediately before `Edit`** (Prettier reformats them in the IDE).
- Headless-screenshot recipe unchanged: plain `--screenshot` (NOT `--virtual-time-budget`, it hangs here); python-inject a `<script>` before `</body>` using a **literal** `</script>`. Prototype nav is the global `nav('prof-detail')`; in-product files click `#pfListBody tr`.
- Keep the internal IP scrubbed to `192.0.2.71` in any committed file (public repo); real IP lives in session memory only.
