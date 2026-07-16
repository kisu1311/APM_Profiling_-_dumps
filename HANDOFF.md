# Handoff — 2026-07-16 18:00

## Read first
See [CLAUDE.md](CLAUDE.md) for full project context — especially **Structure** (what each of the 3 files is) and **Key context** (the research grounding + live-product facts). `index.html` is the primary deliverable.

## What we worked on this session
Built and connected the three **APM · Profiling & Dumps** deliverables: researched the topic (Motadata docs + Datadog/Dynatrace + the user's PDF spec + live instance), produced a strategy doc, a standalone hi-fi prototype, and — the main ask — integrated the Profiling module into the real live Motadata chrome (`index.html`). Then wired all three together with a shared navigation switcher and refined it per feedback.

## Completed
- **`apm-profiling-dumps-redesign.html`** — 12-section research/strategy doc with embedded competitor screenshots (from the PDF), comparison tables, IA tree, flow diagrams, and low-fi wireframes. Verified rendering.
- **`apm-profiling-dumps-prototype.html`** — standalone clickable prototype: Settings toggle → Profiling Explorer → detail → method hotspots → interactive flame-graph workspace (7-metric switcher, hover + click-to-zoom, inspector, synced thread timeline, sub-tabs) → Dumps (thread/heap/crash) → Compare. Verified.
- **`index.html`** — the Profiling module built into the live Side_bar_menu chrome. Boots into `APM ▸ Profiling`; real APM tab bar + **Profiling NEW**; other tabs mirror the live "No data found" state; full profiling flow (all `pf-`scoped). Verified list, drill-down, flame graph, method hotspots, and the Services stub.
- **Live research** — browsed `192.0.2.71/apm/services`, confirmed current APM tabs and sampled the product's exact CSS-variable palette.
- **Cross-linking** — added a shared bottom-center **deliverable switcher** to all three files, then per user feedback: reordered so **In‑product is #1**, and made **Research & Design a "📄 Notes" link** (not a numbered option). Two numbered options now = In‑product, Prototype.

## In progress
Nothing mid-flight. All three files render and are cross-linked. (Note: `index.html` was also opened/edited in the user's IDE — Prettier may reformat it single-line → multi-line; re-Read before any edit that depends on surrounding formatting.)

## Next steps
- If desired, **publish** the folder to GitHub Pages so the three files share live URLs (run `/publish`).
- Optional enhancements the user hasn't asked for yet: add a clickable **Traces → "Open in Profiling"** hop in `index.html`; add the **Dumps** pillar to `index.html` (currently profiling-only there, per the "profiling module only" request); populate a real Services→detail flow if the live instance ever has APM data.

## Decisions made
- **`index.html` = the Side_bar_menu shell, extended non-destructively** (added a `view-profiling` section + `pf-`prefixed CSS/JS, special-cased `selectModule` for APM, changed boot to `selectModule(10)`), rather than a fresh file — so it uses the user's real measured chrome and doesn't clobber their work.
- **Profiling-only in `index.html`** (no Dumps pillar there) per the explicit "profiling module only" request; Dumps lives in the standalone prototype.
- **Switcher = 2 options + a Notes link**, In‑product first — Research & Design is documentation, not an interactive UI, so it's flagged as Notes rather than an equal option (per user feedback across two iterations).
- Matched the **live product palette exactly** (sampled from its CSS variables) so the additions read as native.

## Gotchas & notes
- Paths contain spaces — always `%20`-encode `file://` URLs and quote in shell.
- When script-injecting to screenshot an in-app view, use a **literal `</script>`**; a `<\/script>` escape silently breaks the block (cost debugging time this session).
- Rendering the PDF spec required `brew install poppler` (pdftoppm) — done this session.
- The live instance (`192.0.2.71`) needs the user's authenticated Chrome session and currently has **no APM data**, so a populated Services list / service-detail flow couldn't be observed directly; prototype data is representative, grounded in docs + the PDF.
- The repo-root `../CLAUDE.md` is the broader design-prototype repo guide — left untouched this session.
