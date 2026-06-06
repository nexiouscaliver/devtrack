# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DevTrack is a client-side developer time tracking SPA. No backend — all data persists in `localStorage` under key `"devtrack_data_v1"`. First run seeds 14 days of demo data via `seedData()`.

## Commands

```bash
npm run dev       # Start dev server on port 9000
npm run build     # Production build to dist/
npm run lint      # ESLint across all .js/.jsx files
npm run preview   # Preview production build
```

No test framework is configured.

## Architecture

**Single-file architecture**: Nearly all application logic lives in `src/App.jsx` (~1950 lines). The file contains:

- **`App()`** — root component with all state (`useState`) and navigation. State auto-saves to localStorage on every change via `useEffect`.
- **Six view components**, each in a clearly marked section:
  - `Dashboard` — stats cards, weekly chart, recent activity
  - `TimerView` — work/break timer, session start/stop, idle detection
  - `SessionsView` — session history list with edit/delete
  - `GitView` — GitHub API integration to fetch and display commits
  - `AnalyticsView` — charts (daily hours, tag distribution, hourly activity, peak hours)
  - `ExportView` — Excel/CSV export with multiple sheets (uses `xlsx` library)
- **Supporting components**: `StatCard`, `SettingsModal`, `Toast`
- **Utilities**: `Icon` (SVG component), `ICONS` map, `formatDuration`, `formatTime`, `formatDate`, `isToday`, `dayName`, `startOfDay`
- **Data layer**: `load()`, `save()`, `seedData()` — all operate on a single JSON blob in localStorage

**Entry points**: `index.html` → `src/main.jsx` → `src/App.jsx`

## Key Libraries

| Library | Purpose |
|---------|---------|
| React 19 | UI framework (JSX, no TypeScript) |
| Vite 8 | Build tool, dev server (port 9000) |
| Tailwind CSS 3 | Styling (dark theme with slate palette) |
| Framer Motion | Animations (`motion`, `AnimatePresence`) |
| Recharts 3 | Charts (Area, Line, Bar, Pie) |
| xlsx | Excel/CSV export |

## Data Model

The entire app state is one object persisted as JSON:

```
{
  sessions: [{ id, type, start, end, duration, tags, notes, status }],
  commits: [{ sha, message, repo, timestamp }],
  settings: { dailyGoal, githubToken, githubUser, idleMinutes },
  manualCommits: [...]
}
```

## Styling Conventions

- Dark theme only — Tailwind classes with `bg-slate-*`, `text-slate-*` palette
- All icons are inline SVG via the `Icon` component and `ICONS` map (no icon library dependency)
- Responsive layout using Tailwind's responsive prefixes

## Important Notes

- GitHub API calls in `GitView` use a personal access token stored in `settings.githubToken` (saved in localStorage — never sent to a backend)
- No environment variables or `.env` files needed — this is a pure client-side app
- ESLint config uses the flat config format (`eslint.config.js`) with React hooks and React Refresh plugins
