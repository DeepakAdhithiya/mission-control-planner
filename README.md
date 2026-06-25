# 🛰️ Mission Control & Daily Planner

A single-file, HUD-styled daily planner with a smart reminder engine — missions, deadlines, and an auto-sorting timetable, all running entirely in the browser with zero backend.

**[Live Demo →](#)** *(https://deepakadhithiya.github.io/mission-control-planner/)*

![Tech](https://img.shields.io/badge/JavaScript-Vanilla-F7DF1E?logo=javascript&logoColor=black)
![Tech](https://img.shields.io/badge/Tailwind_CSS-CDN-38BDF8?logo=tailwindcss&logoColor=white)
![Tech](https://img.shields.io/badge/Storage-localStorage_%2B_IndexedDB-2563EB)
![Tech](https://img.shields.io/badge/Build_Step-None-22D3A8)

---

## Why this exists

I wanted a daily planner that actually reminded me of things on time, sorted my tasks the way my brain expects, and didn't require installing yet another app. So I built one — as a single `index.html` file, no framework, no build tools, no server.

## Features

- **Missions (to-dos)** — create, complete, and delete tasks, each optionally tagged with a deadline date.
- **Folders / collections** — organize missions into groups with a dropdown and pill-filter UI.
- **Smart reminder engine**
  - Checks every second for near-instant alert firing.
  - Real OS-level browser push notifications, backed by a Service Worker.
  - Three sound options (Chime / Alarm / Silent) via the Web Audio API.
  - Correctly gates date+time reminders so they only fire on the actual deadline date — not every day at that time.
- **Auto-sorting daily timetable** — editable schedule that sorts missions in a chronologically sensible order: time-only tasks first, then dated tasks, then undated tasks last.
- **Backup & restore** — export all data to a JSON file, or import a backup to restore it — useful since everything lives in the browser only.
- **Persistent storage** — `localStorage` for missions/schedule, `IndexedDB` as a mirror for Service Worker access.
- **Live clock**, 12-hour format, and a distinctive HUD aesthetic (IBM Plex Sans/Mono + Orbitron numerals, orange accent on a deep navy background).

## Tech stack

- **Vanilla JavaScript** — no framework, no dependencies beyond Tailwind's CDN script.
- **Tailwind CSS (CDN)** — utility-first styling, no build step.
- **Web Audio API** — for the in-app reminder sounds.
- **Service Workers** (registered via a Blob URL, so the whole app stays one file) — for OS-level push notifications.
- **localStorage + IndexedDB** — for persistence, including a mirror store the Service Worker can read independently of the page.

## Running it locally

No install, no build step — just open the file:

```bash
git clone https://github.com/yourusername/your-repo.git
cd your-repo
open index.html   # or just double-click it
```

For full notification support, browser security policies require serving over `http://` or `https://` rather than `file://`. To test that locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deploying

This is a static file — [GitHub Pages](https://pages.github.com/) is the natural fit:

1. Push this repo to GitHub.
2. Repo → **Settings** → **Pages** → Source → `main` branch → `/ (root)`.
3. Your app is live at `https://yourusername.github.io/your-repo/`.

## Engineering notes

A few decisions worth calling out, since they weren't obvious going in:

- **Tailwind's CDN script needs a retry guard.** If it fails to load (slow network, blocked CDN), it fails silently and every downstream script — including all button event listeners — breaks with no visible error. Wrapping the config in a load-check/retry avoids a fully dead app on a bad connection.
- **Hidden elements with conditional display logic are a common source of "the button doesn't work" bugs.** An early version had the notification "Enable" button living inside a `hidden` div, so its click handler never fired. Always-visible UI for critical actions avoids this entire class of bug.
- **Reminder state needs careful tracking.** Missions that have already fired a notification must be excluded from "next reminder" calculations, or the UI stalls on a reminder that already happened.
- **Date-gating matters for combined date+time reminders.** Without checking the actual date alongside the time, a deadline reminder set for "3:00 PM on the 15th" would fire at 3:00 PM *every single day*, not just the 15th.

## License

MIT — use it, fork it, learn from it.
