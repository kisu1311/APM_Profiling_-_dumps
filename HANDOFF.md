# Handoff — 2026-07-27 14:10

## Read first
See **CLAUDE.md** — **Structure** (the 5 deliverables + shared switcher) and **Key context** (the doc-grounded live-vs-profiler data model). This session worked only on the two in-product reference designs: `apm-profiling-datadog.html` (most of the work) and `apm-profiling-dynatrace.html`.

## What we worked on this session
Made the **Datadog** flow's flame-graph detail show child processes and behave like the real Datadog profiler: a per-service **Processes panel** plus an **interactive, collapsible flame graph** (default-collapsed, click-to-expand, expand-all/collapse-all icons). Also added a **dynamic breadcrumb** to the **Dynatrace** flow's APM header.

## Completed
- **`apm-profiling-dynatrace.html`** — dynamic breadcrumb in the APM header (`APM ▸ Profiling ▸ <service> ▸ <screen>`), updates on every nav via `setHead()` (called from `go()` and `setApm()`). Fixed an undefined `--muted2` token (→ `--muted`) so the separators render dimmed. **Committed + LIVE** (commit `8b76ab3`).
- **`apm-profiling-datadog.html` — Processes panel** on the flame view: keyed to the selected service from `SVCS[].procs`, shows "N processes · M hosts", chip row (All processes + one per runtime `host · pid`), a green **`scoped · pid NN @ host`** pill when scoped, and a "Show runtimes" table (Runtime · Host · Container/Pool · Process ID · Version). Functions: `svcByName`, `procMeta`, `renderProcScope`, `scopeProc`, `updateScopeNote`; called from `go('flame')`. **Committed + LIVE** (commit `8b76ab3`).
- **`apm-profiling-datadog.html` — interactive collapsible flame graph** (this is the **UNCOMMITTED** part): each node gets a stable `_id`; a `COLLAPSED` set folds subtrees; `lay()` skips collapsed subtrees (but always expands the focused node); `flame()` renders a **▾/▸ caret** on frames with children; **defaults to fully collapsed** (root + one level) via `collapseAll()` seeded at load; **click a frame body to expand it one level in place**, click the caret to toggle, click an already-expanded frame to zoom. Added **Expand-all / Collapse-all icon buttons** (`#pfExpandAll` / `#pfCollapseAll`) in the flame toolbar (`reflameKeep()` preserves zoom focus); Reset-zoom re-collapses to default. All verified with headless screenshots.

## In progress
Nothing mid-flight. The uncommitted flame-graph changes in `apm-profiling-datadog.html` are complete and verified — they just haven't been pushed.

## Next steps
- **Publish the uncommitted flame-graph work**: run `/publish` (commits `apm-profiling-datadog.html` and auto-redeploys on push to `main`). `git status` shows one modified file (~18-line diff).
- Optional (raised, not done): the datadog flow's `pfv-detail` view is **orphaned** (list jumps straight to the flame graph) and still carries the old "Top contributors · attached services + child processes" **mislabel** that was fixed in `index.html` — clean it up or delete the dead view.
- Optional carry-overs from before: apply the live-vs-profiler availability badges to `index`; add a Dumps pillar to the in-product flows; add an "Open in Profiling" hop from Services/Explorer.

## Decisions made
- **Datadog = "Processes/runtimes", not "attached services."** Datadog's profiler aggregates a service's profile across runtimes tagged by host/container/process_id/version — that's the correct Datadog analog of "child processes", and it's deliberately different from the Dynatrace/native "attached services" framing.
- **Flame graph defaults to collapsed**, one level at a time — matches the reference the user gave (Datadog frames with ▾/▸ carets, collapsed subtrees). Body-click expands a collapsed frame *in place* (keeps focus) and only zooms once expanded, so "click to expand" and "click to zoom" coexist without a mode toggle.
- **Collapse state is session-persistent** (a global `COLLAPSED` set, seeded once). Metric switches don't re-collapse (new tree = new `_id`s); Expand-all/Collapse-all and Reset-zoom are the explicit resets.
- Kept the change **isolated to the datadog file** — index/dynatrace flame graphs stay fully-expanded, so this doesn't regress the other deliverables even though they share the flame code's ancestry.

## Gotchas & notes
- **Inline `.pf-bar` progress bars collapse in table cells** unless given an explicit `display:inline-block;width:NNpx` (or placed in a flex row) — recurring issue across these files.
- **Re-`Read` an HTML file immediately before `Edit`** — Prettier reformats single-line → multi-line when the IDE opens it, breaking `old_string` matches.
- **Headless-screenshot recipe**: plain `--screenshot` (NOT `--virtual-time-budget`, it hangs here); python-inject a `<script>` before `</body>` using a **literal** `</script>`. Datadog nav to the flame view = click `#pfListBody tr.ddsvc[data-svc='<name>']`. Keep the injected click delay **short (~120–150ms)** — a long `setTimeout` sometimes fires *after* the headless capture (saw list-view instead of flame at 500ms).
- **`gh` is not installed** — `/publish` uses git + the GitHub REST API (osxkeychain token). Repo `kisu1311/APM_Profiling_-_dumps`, live at https://kisu1311.github.io/APM_Profiling_-_dumps/.
- Keep internal IPs scrubbed to `192.0.2.x` in committed files (public repo); the only IP literals in these files are `127.0.0.1` (localhost), which is fine.
