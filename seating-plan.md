# Wedding Seating Plan — Project Context

Read this first in a new conversation instead of re-reading the whole app or chat history.

## What this is

A single-file, client-side wedding seating planner. No backend, no build step, no
dependencies. Drag-and-drop guests onto round or long tables, pan/zoom the canvas,
and optionally sync the plan across devices via a GitHub repo.

- **Live site:** https://thekhenzie.github.io/seating-plan/
- **Repo:** github.com/thekhenzie/seating-plan (branch `main`)

## Files

| File | Purpose |
|---|---|
| `index.html` | Love-themed splash screen (CSS hearts animation), auto-redirects to `seating-plan.html` after ~2.6s. Entry point for GitHub Pages. |
| `seating-plan.html` | The entire app: HTML + CSS + vanilla JS in one file, wrapped in an IIFE. This is the only file with real logic. |
| `data/guestlist.json` | The synced plan data, written by the app itself via GitHub's Contents API when GitHub sync is connected. Treat commits here ("Update guest list") as data, not code changes. |
| `README.md` | User-facing usage docs. |
| `docs/superpowers/specs/2026-07-03-wedding-seating-plan-design.md` | Original design spec from the initial brainstorming pass. |
| `seating-plan - back.html` | A personal manual backup the user keeps; gitignored, not part of the app. |

## Data model (the `state` object, persisted to localStorage and optionally GitHub)

```js
{
  version: 1,
  zoom: 1,              // canvas zoom level, 0.4–2.0
  guests: [
    { id: "g_xxx", name: "Full Name" }
  ],
  tables: [
    {
      id: "t_xxx",
      name: "Table name",
      seats: [guestId | null, ...],  // length = capacityFor(name): 10 normally, 8 if long-table keyword match
      x: 20, y: 20,                  // top-left position on the canvas (world coords, pre-rotation)
      rotation: 0                    // degrees, freely draggable via the rotate handle
    }
  ]
}
```
"Unseated" guests are computed, not stored — any guest not referenced in any table's `seats` array.

## Feature summary

- **Guests:** add one at a time or paste many (newline/comma separated); search/filter the unseated pool; drag a chip onto any empty seat.
- **Tables:** round (10 seats, radial layout) or **long single-row (8 seats, one edge only)** — the shape is *derived automatically* from the table name containing `"nenong"`, `"nenang"`, or `"primary"` (case-insensitive substring match, see `LONG_TABLE_KEYWORDS`). Renaming a table across that boundary resizes it live, with a confirm if it would bump guests off the end.
- **Table rotation:** long tables only. A small **⟳ drag handle** at the table's top-right corner — drag it with the mouse to freely rotate around the table's center at any angle (not stepped). All labels (table name, guest names, seat numbers) **counter-rotate** to stay upright and readable regardless of table angle.
- **Move tables:** drag the table body (not the handle) to reposition anywhere on the canvas.
- **Canvas:** pan by dragging empty space; zoom via the +/− buttons, Ctrl+scroll (desktop), or two-finger pinch (touch — scoped to the canvas only via `touch-action: none`, doesn't zoom the whole page).
- **Find on canvas:** a search box highlights (pulsing red) the seat/table of a matching guest, or reports if they're unseated.
- **Persistence:** auto-save to `localStorage` (per-browser) on every change. **Export/Import** buttons for manual `.json` backup/restore.
- **GitHub sync (optional, currently connected/in use):** a device can connect with a GitHub fine-grained PAT (Contents: read/write on this one repo). Auto-pulls the latest plan on page load, auto-pushes ~3s after each change to `data/guestlist.json`. Uses GitHub's sha-based optimistic concurrency — a real conflict shows "⚠ Reload needed" rather than silently overwriting another device's edit.
- No Reset button (removed on request) — Import or manual seat-by-seat removal are the only ways to clear guests/tables now.

## Implementation notes / gotchas for future changes

- **Everything lives in one `<script>` IIFE** inside `seating-plan.html`. Function *declarations* (`function foo(){}`) are hoisted, but `var` *assignments* are not — `var state = load() || {...}` runs at the very top of the script, so any `var` whose value `load()`/`normalize()` depends on (e.g. `LONG_TABLE_KEYWORDS`) **must be declared with its value before that line**, not just declared anywhere in the file. This exact bug happened once (silently wiped all data via the `load()` try/catch swallowing the resulting exception) — was caught in testing, not shipped. Be careful of this class of bug when adding new module-level config used during initial load.
- **Rotation + upright text:** the whole `.table` wrapper (shape + seats) gets `transform: rotate(Ndeg)`. A nested `.tcontent` div (wrapping the table's name/menu/count) and each seat's `.sname`/`.snum` span get the *inverse* `rotate(-Ndeg)` so they visually cancel out and stay horizontal. When dragging the rotate handle, these counter-transforms are updated live via direct DOM manipulation (not full re-render) for smoothness, then committed to `state`/localStorage/GitHub on pointerup.
- **Seat capacity is per-table, not global:** `SEATS_PER_TABLE = 10` is the round-table default; `capacityFor(name)` returns `8` if the name matches a long-table keyword. Always resize via the `resizeSeats(table)` helper (handles both grow/pad and shrink/truncate) rather than touching `.seats` length directly.
- **Two independent drag systems coexist:** HTML5 drag-and-drop (`draggable=true` + `dragstart`/`dragover`/`drop`) is used for moving *guests* between seats/pool. Pointer Events (`pointerdown`/`pointermove`/`pointerup` + `setPointerCapture`) are used for *table* dragging, canvas panning, pinch-zoom, and rotation. Don't mix the two paradigms for the same interaction.
- **GitHub API config** is hardcoded near the top of the script: `GH_OWNER = "thekhenzie"`, `GH_REPO = "seating-plan"`, `GH_PATH = "data/guestlist.json"`, `GH_BRANCH = "main"`. The PAT itself is never stored in `state` — it lives in a separate localStorage key (`weddingSeatingPlan.githubToken`), per-device.
- Preview/testing tip: use the Claude Preview MCP tools with `.claude/launch.json` (`python -m http.server 8777`) rather than Bash, since this is a static file with no dev server of its own.

## Status

All requested features are implemented and verified (manually tested via browser automation each time, not just code review). No known open bugs or pending work as of the last session. Treat any new request as an incremental addition to this existing app, not a rebuild.
