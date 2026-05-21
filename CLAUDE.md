# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-file family shared notes web app built for demo purposes. No build step, no server, no dependencies — just open `index.html` in a browser.

## Running the App

```bash
open index.html
```

Or drag `index.html` into any browser tab. There is nothing to install or build.

## Architecture

Everything lives in one file: `index.html`. It is structured in three sections:

1. **`<style>`** — all CSS, using CSS custom properties defined in `:root` for colours
2. **`<body>`** — static HTML shell (header, tab buttons, three panel divs, modal overlay)
3. **`<script>`** — all application logic, vanilla JS, no frameworks

### Data model

All state is stored in `localStorage` under three keys:

| Key | Type | Contents |
|---|---|---|
| `yusuf_notes` | `Note[]` | User-created family notes |
| `yusuf_likes` | `{[key: string]: boolean}` | Like state; keys are `"idea_y1"` or `"note_<id>"` |
| `yusuf_activity` | `ActivityEntry[]` | Event log, capped at 100 entries |

`YUSUF_IDEAS` is a hardcoded constant array — it is seed/demo data and is never written to storage.

### Note schema

```js
{ id: number, title: string, body: string, category: string, pinned: boolean, author: string, date: string, likes: number }
```

`id` is `Date.now()` at creation time. `author` is whichever member is selected in `#user-select` at save time.

### Render cycle

`render()` calls all three panel renderers. Each renderer (`renderIdeas`, `renderFamily`, `renderActivity`) clears its grid and rebuilds it from scratch from the in-memory arrays. There is no virtual DOM or diffing — full re-render on every state change.

### Tab system

Three panels (`#panel-ideas`, `#panel-family`, `#panel-activity`) are toggled via `display:none/block` using the `.active` class. `switchTab(tab)` manages both the panel and tab button active states.

### User system

There is no authentication. The active user is just the value of `#user-select` stored in `currentUser`. Only a note's `author` can see Edit/Delete buttons on their own cards in the Family tab.

### Members

The four family members are defined in the `MEMBERS` constant:
- `Yusuf` (indigo, 👑)
- `Mum` (pink, 🌸)
- `Dad` (blue, 🛠️)
- `Sister` (green, ✨)

To add a new member: add an `<option>` to `#user-select` in the HTML and an entry to the `MEMBERS` object in the script.

### Categories

`CAT_COLORS` maps category names to hex colours. Categories are: General, Shopping, Reminder, Idea, Important. To add one, update both `CAT_COLORS` and the `<select id="note-category">` options, and add an emoji mapping in `catEmoji()`.

## Deploy

```bash
git add .
git commit -m "description"
git push
```

Hosted on GitHub at `aadillyas/yusuf-app`. No CI, no build pipeline — the repo serves the raw file.
