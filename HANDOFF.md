# Handoff — 2026-07-29 23:11

## Read first
See **CLAUDE.md** — the **`apm-profiling-dynatrace-v2.html`** bullet in *Structure*, which was
rewritten this session and is now much longer than the others. That file grew from "a trimmed
Dynatrace variant" into the richest deliverable: it now has a real **Services** tab, a **per-service
page**, and the profiling flow reachable from **two** entry points. Everything this session happened
in that one file; the other five are untouched.

## What we worked on this session
Built out `apm-profiling-dynatrace-v2.html` in three passes, each from a screenshot of the live
product: (1) filled the empty **APM ▸ Services** tab with the live service card grid, (2) made a
service click open the **live service page** (Overview…Logs) with a new **Profiling** tab, and
(3) replaced that Profiling tab's placeholder content with the **real profiling flow**, starting at
Process details.

## Completed
- **Services tab (`pfv-apmsvc`)** — the live 8.2.6 card grid: 9 services with globe + severity ring +
  inline tech logo (Java / Node / PHP / Python / .NET, hand-drawn SVG), **Response Time** and
  **Throughput** area sparklines, and an **Error Count** bar sparkline that only appears when errors > 0.
  Working **search filter** (name or technology, with an empty state) and a **card ↔ list** view toggle.
  All `pfs-`prefixed; sparklines seeded so they render identically every load.
- **Service page (`pfv-svcdetail`)** — replaces the APM head + tab bar like the real product does
  (`svdChrome()`). Header: back chevron · globe · `Integration | <service>` + logo · `1h / Last 1 Hour`
  + timestamps. Tabs: Overview · Transactions · API Endpoint · Database · JVM · Error Tracker · Logs ·
  **Profiling NEW**. All `svd-`prefixed.
- **Overview tab** — the 6 KPI cards (Response Time, P99, Total Requests, Throughput, Error Count in
  red, Error %), 3 axis charts (Response Time with the `service.trace.duration.us/p99/p50.avg` legend,
  Request Throughput, stacked Requests & Errors) and the **Service Map** (severity-ringed globe nodes,
  arrowed edges, zoom cluster). Per-service data via `SVCDET`; the six live tabs get a short
  "mirrors live current state" panel.
- **Charts scale per service** — `svdNice()` picks 3–5 round axis steps, so javaDistributed reads
  `0 / 10 s / 20 s / 30 s` and PHPApp reads `0 / 100 … 400 ms`; the red share of the stacked bars
  tracks each service's real error percentage (all-teal for the zero-error services).
- **Profiling tab hosts the real flow** — its placeholder content (banner, 4 profiler KPIs, CPU chart,
  hotspot list, dumps row) was removed and replaced with the actual **Process details** screen. One
  copy of the four flow views is **moved** between `#pfBody` and `#svdProf` (`svdProfHome()` /
  `svdProfMount()`), and **`pfGo()`** dispatches every `data-goto` / row click to `goSvcProf()` in-tab
  or `go()` at top level. Full chain works in both: process row → attached services → device monitor →
  back, and → method hotspots.
- **Per-service processes** — `PROCSPEC` (9 services × 2 processes + 4 attached services) drives
  `renderProcs()` / `renderAttached()`, so PHPApp shows `php-fpm · pool www` on `web-fpm-04` instead of
  javaDistributed's. This also fixed the **top-level** flow, which previously showed javaDistributed's
  processes for every service.
- **Small fixes** — the device monitor's "Back to services" link pointed at Process details
  (`data-goto="detail"` → `"services"`); breadcrumbs now show `<service> · <process>`; dead CSS
  (`.svd-pf4`, `.svd-pfk`, `.svd-dump`, `.svd-dc`, `.svd-c2`) removed.

## In progress
Nothing mid-flight. Verified in headless Chrome: **9 services × 8 tabs** render with no console errors,
the flow mounts at Process details in every one, the in-tab drill-down and all back links work, and the
top-level `APM ▸ Profiling` flow (list → details → back) is unaffected.

## Next steps
- **Publish** — run `/publish`. Only `apm-profiling-dynatrace-v2.html` is modified (plus
  `CLAUDE.md` / `HANDOFF.md`). No switcher changes this time, so there's no cross-file publish
  dependency like last session.
- **Decide whether to port any of this to the other deliverables.** The Services tab, service page and
  in-tab profiling now exist **only in v2** — `index`, `dynatrace` and `datadog` still show the
  "No data found" stub on their non-profiling APM tabs. Porting is a real cost (the switcher lesson:
  anything shared has to be applied per file).
- Optional carry-overs from before: the Datadog flow's orphaned `pfv-detail` "Top contributors"
  mislabel; availability badges on `index`; a Dumps pillar in the in-product flows.

## Decisions made
- **The service page replaces the APM chrome** rather than nesting under it — the live product hides
  the APM tab bar on a service page, so `svdChrome(true)` hides `.pagehead` + `#pfApmTabs` and the
  service's own header/tab bar takes over. The back chevron is the only way out, as in the real product.
- **Move the flow views, don't duplicate them.** The Profiling tab needed the same four screens the
  top-level tab uses. Cloning the markup would have doubled ~200 lines and guaranteed drift, so the
  nodes are relocated between two hosts and a dispatcher picks the navigator. All existing
  render functions and delegated handlers kept working unchanged.
- **Made the process/attached-service rows dynamic** even though the ask was only about the tab. Once
  the flow opens per service, hardcoded "javaDistributed · order-service" rows would have looked broken
  on the other 8 — and the same bug already existed in the top-level flow.
- **Cards click through to the service page**, not straight into profiling — matches the live product,
  and the Profiling tab is then the deliberate "here's the new thing" step.

## Gotchas & notes
- **Two navigators now exist.** `go()` = top-level profiling flow; `goSvcProf()` = the same screens
  inside the service page; **`pfGo()`** picks between them. Any *new* `data-goto` button must go through
  `pfGo`, never `go` directly, or it will break in-tab.
- **`renderSvcDetail()` calls `svdProfHome()` first** — it wipes `#svdBody.innerHTML`, which would
  destroy the flow views if they were still mounted there. Same reason `go()` and `goSvc()` call it.
- **Prefix discipline held**: `pfs-` = Services grid, `svd-` = service page, `pf-` = the original
  profiling flow. Keep it — this file is one flat stylesheet + one flat script and duplicate
  class/function names fail silently (see the repo-root CLAUDE.md gotchas).
- **Headless screenshots**: run against a **copy in the scratchpad**, not the file in place — the
  549 KB `agentation-embed.js` sits next to the real file and keeps headless Chrome from exiting, so
  the command hangs until it's killed. Inject a `<script>` before `</body>` that clicks the target
  (`#pfApmTabs .tab[data-apm="services"]` → `#pfsGrid .pfs-card[data-svc="…"]` →
  `#svdTabs button[data-st="profiling"]`), with ~700–900 ms delay and `--virtual-time-budget=3500`.
- **SVG text and `preserveAspectRatio`**: the card sparklines use `preserveAspectRatio="none"` (fine —
  paths only, with `vector-effect="non-scaling-stroke"`), but the axis charts must not, or the labels
  distort. Their apparent text size depends on the viewBox width vs. the panel width, so a wide panel
  needs a wider viewBox (`W:900` on the CPU chart) to match the others.
- Keep internal IPs scrubbed to `192.0.2.x` in committed files (public repo). `gh` is not installed —
  `/publish` uses git + the GitHub REST API. Repo `kisu1311/APM_Profiling_-_dumps`, live at
  https://kisu1311.github.io/APM_Profiling_-_dumps/.
