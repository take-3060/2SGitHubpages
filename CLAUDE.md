# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single static HTML page (`index.html`) published via GitHub Pages. There is no build system, package manager, linter, or test suite — the entire site is one self-contained file (inline `<style>` and `<script>`, no external dependencies).

The page ("個人棚廃止についての対応状況") tracks internal staff responses to a notice, showing a countdown to a deadline and each person's done/pending status. Clicking a name opens a modal with their photo.

## Local preview

Open `index.html` directly in a browser, or serve it locally:

```sh
python3 -m http.server
```

Then visit `http://localhost:8000`.

## Deployment

Push to the `main` branch — GitHub Pages serves the repo root directly (Settings → Pages → source: `main` branch, root). There is no build/deploy step; whatever is committed to `index.html` is what goes live.

## Architecture (single-file structure)

`index.html` has three parts in one file:

- **`<style>`**: theme via CSS custom properties on `:root`, with a `prefers-color-scheme: dark` media query and a `[data-theme="dark"/"light"]` override for manual theme toggling.
- **Body markup**: a countdown tile, a progress bar, two `.button-grid` sections (`done-grid` / `pending-grid`) populated at runtime, and a hidden modal overlay for the photo view.
- **`<script>`**: a single `people` array is the entire data model — each entry is `{ name, status: "done"|"pending", photo: "data:image/jpeg;base64,..." }`. Photos are embedded directly as base64 data URIs (no separate image files/assets folder), which is why `index.html` is large. `renderPeople()` builds the grids and progress/nudge text from this array; `openModal`/`closeModal` drive the photo modal; `updateCountdown()` runs on a 1s `setInterval` against a hardcoded `deadline` timestamp.

To add/update a person or their status, edit the `people` array literal directly — there is no separate data file.

Note: `people[].photo` entries contain real staff photos/names — treat this data as sensitive when handling or sharing the file.
