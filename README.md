# Wedding Seating Plan

An interactive drag-and-drop seating planner for our wedding — no install, no backend, just open it in a browser.

**Live site:** _add your GitHub Pages URL here once deployed, e.g. `https://<username>.github.io/<repo>/`_

## Features

- **Guest list** — add names one at a time or paste a whole list at once
- **Round tables** — add tables, name them, each seats exactly 10 guests
- **Drag & drop** — drag guests onto seats, between seats, or back to the unseated pool
- **Move tables** — drag whole tables around the canvas to lay out your venue
- **Pan & zoom** — drag empty canvas space to pan, `Ctrl` + scroll to zoom
- **Find a guest** — search box on the canvas highlights where someone is seated
- **Auto-save** — everything saves to your browser automatically
- **Export / Import** — download a `.json` backup and load it back later, or move your plan to another computer

## Usage

Open [`seating-plan.html`](seating-plan.html) in any browser (double-click the file, or visit the live site above).

## Files

| File | Purpose |
|---|---|
| `index.html` | Redirects to `seating-plan.html` (entry point for GitHub Pages) |
| `seating-plan.html` | The seating planner app |
| `docs/superpowers/specs/` | Design notes from when this was built |

## Notes

- Auto-save is tied to the browser/device you're using. Use **Export** to back up your plan or move it to another computer, then **Import** it there.
- This is a static, client-side-only page — your guest list never leaves your browser.
