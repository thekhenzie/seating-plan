# Wedding Seating Plan — Design Spec

**Date:** 2026-07-03
**Deliverable:** A single, self-contained `seating-plan.html` file (HTML/CSS/JS, no external dependencies, works offline via `file://`).

## Purpose
Let the user plan wedding seating by dragging guest names onto round tables, with the arrangement persisting between sessions.

## Layout
- **Left sidebar:** Unseated guests pool — add-name box, paste-many support, search filter, live counters.
- **Main area:** Canvas of round-table graphics.

## Guests
- Add one name via input + Enter; paste multiple newline-separated names to bulk-add.
- Each guest renders as a draggable chip.
- Counters shown: Total, Seated, Unseated.
- Empty/whitespace names ignored. Duplicate names allowed.

## Tables (round graphic)
- "Add table" button; user names each table.
- Rendered as a circle with **exactly 10 seat slots** around the rim; center shows table name + `X/10` counter.
- Drag guest from pool onto an empty seat.
- Drag between seats/tables to move; drag back to pool or click a seat's ✕ to unseat.
- A full table (10/10) rejects further drops with a subtle visual nudge.
- Rename table; delete table (with confirm) returns its guests to the unseated pool.

## Persistence
- **Auto-save** to `localStorage` after every mutation; reopening the file restores state.
- **Export** button downloads a `.json` backup of the full state.
- **Import** button loads a `.json` backup, validating structure, with a confirm before overwriting current state.

## Data Model
```
{
  guests:  [{ id, name }],          // all guests
  tables:  [{ id, name, seats:[guestId|null x10] }],
  version: 1
}
```
Unseated = guests not referenced in any table seat (derived).

## Edge Cases
- Import validates JSON shape; malformed file rejected with a message.
- Delete-table confirms and reassigns guests to pool (no data loss).
- Deleting a guest removes them from their seat too.

## Non-goals (YAGNI)
- No multi-user/cloud sync, no printing layout, no seat-shape variants, no per-guest metadata (meal, +1) in v1.
