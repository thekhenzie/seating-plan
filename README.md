# Wedding Seating Plan

An interactive drag-and-drop seating planner for our wedding — no install, no backend, just open it in a browser.

**Live site:** https://thekhenzie.github.io/seating-plan/

## Features

- **Guest list** — add names one at a time or paste a whole list at once
- **Round tables** — add tables, name them, each seats exactly 10 guests
- **Drag & drop** — drag guests onto seats, between seats, or back to the unseated pool
- **Move tables** — drag whole tables around the canvas to lay out your venue
- **Pan & zoom** — drag empty canvas space to pan, `Ctrl` + scroll to zoom
- **Find a guest** — search box on the canvas highlights where someone is seated
- **Auto-save** — everything saves to your browser automatically
- **Export / Import** — download a `.json` backup and load it back later, or move your plan to another computer
- **GitHub sync (optional)** — connect a device with a personal access token to keep the plan in sync across multiple devices, instead of relying on one browser's local storage

## Usage

Visit the live site above, or open [`seating-plan.html`](seating-plan.html) directly in any browser (double-click the file).

### Syncing across devices

By default your plan is saved only to the browser you're using. To keep it in sync across your phone, laptop, etc.:

1. Create a GitHub fine-grained personal access token scoped to this repo with **Contents: Read and write** ([github.com/settings/tokens?type=beta](https://github.com/settings/tokens?type=beta))
2. Click **🔗 Connect GitHub** in the app and paste the token in
3. Repeat step 2 on any other device, pasting the *same* token

Once connected, changes auto-save to this repo (`data/guestlist.json`) a few seconds after you stop editing, and every connected device pulls the latest plan when it opens.

## Files

| File | Purpose |
|---|---|
| `index.html` | Love-themed splash screen that redirects to `seating-plan.html` (entry point for GitHub Pages) |
| `seating-plan.html` | The seating planner app |
| `data/guestlist.json` | Synced guest list data (only present once GitHub sync has been used) |
| `docs/superpowers/specs/` | Design notes from when this was built |

## Notes

- Without GitHub sync connected, auto-save is tied to the browser/device you're using. Use **Export** to back up your plan or move it to another computer, then **Import** it there.
- This is a static, client-side-only page — no server, no backend. Your guest list never leaves your browser except when you explicitly connect GitHub sync.
