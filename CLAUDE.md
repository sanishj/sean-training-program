# Sean's Middle Blocker Training Tracker

A 12-week gym program tracker for Sean John (Middle Blocker, USA Volleyball U17). Sean logs workouts and vertical jump numbers; his dad Sanish views progress read-only.

- Live: https://sanishj.github.io/sean-training-program/ (GitHub Pages, `main` branch, repo root)
- Repo: `sanishj/sean-training-program`
- Firebase project: `sean-mb-dev-program` (Spark/free tier; keep it that way, no billing)

## Who I'm working with
Sanish is not a developer. Spell out steps, explain what a command does before running it, and keep changes small and easy to verify.

## How it's built
- One file, `index.html`: vanilla HTML/CSS/JS, inline CSS and JS. No framework, no build step, no bundler. Keep it that way unless Sanish asks.
- Firebase **compat** SDK v10.12.2 via `<script>` tags from gstatic (namespaced `firebase.*` API, not ES modules).
- The Firebase `apiKey` is a public project identifier, not a secret. GitHub secret scanning flagged it once; that was a false positive. Access control lives in `firestore.rules`.
- Design: CSS custom properties for light/dark (`prefers-color-scheme` + `data-theme` override), Oswald + Work Sans, mobile-first with iOS safe-area insets.

## Access model (change client and rule together)
- `OWNER_EMAIL = "onlyseanjohn@gmail.com"`: read/write. `VIEWER_EMAILS = ["sanishj@gmail.com"]`: read-only.
- Enforced in `index.html` **and** `firestore.rules`. If an email changes, update both, then `npm run deploy:rules`.

## Data: `trackerData/sean`
```js
{
  completions: { "<isoWeekKey>-day<N>": { done: true, at: "<ISO timestamp>", approx?: true } },
  phase: "1" | "2" | "3",
  vj: { vj1, vj4, vj8, vj12 },          // inches, as strings
  startDate: "YYYY-MM-DD",             // backfilled from earliest completion if missing
  dismissedPhaseSuggestion: "2" | "3" | null
}
```
- Legacy entries may be a bare `true`. `isDone()` and `normalizeCompletions()` handle both forms; don't break that.
- Entries with `approx: true` get their date recomputed on every load (`approxDateForKey` / `mondayOfIsoWeek`). Build dates in **local time anchored at local noon**, never `Date.UTC`-then-display; an earlier UTC version showed dates one day early in US timezones.
- Local dev (localhost / 127.0.0.1) reads and writes `trackerData/sean-dev` instead, and shows a red TEST DATA badge. Never point local testing at `sean`.

## Product rules (deliberate, don't "fix")
- **Phase advance is only a suggestion.** `checkPhaseSuggestion()` shows a banner based on weeks since start. Only the owner can switch phases, so a kid who missed sessions isn't pushed into harder plyometrics.
- **Rotation, not weekdays.** Workouts go Day1→2→3→4→1 (`nextUpDay()`); club practice moves around, so nothing is tied to Tue/Thu/Fri/Sat.
- **Calendar shows the real date** from completion timestamps (`buildCompletedDateSet()`), not a nominal weekday.
- Day icons are the same everywhere: day1 🦵 lower, day2 💪 upper, day3 ⚡ power/conditioning, day4 🔄 full body/mobility.
- Exercise demos link to YouTube **search results**, not embedded videos or GIFs.
- Offline: `db.enablePersistence()` is required. Every write shows Saving/Saved/Failed. Missing persistence once made "Mark complete" fail silently on gym wifi.
- Vertical jump: Planet Fitness has no wall to mark, so it's measured with phone flight-time apps.

## Workflow
- Run locally: `npm run serve`, open http://localhost:5500 (Google sign-in works on localhost).
- Deploy the site: commit and `git push` to `main`; Pages updates in about a minute.
- Deploy rules: edit `firestore.rules`, then `npm run deploy:rules`.

## Backlog
- Printable one-page plan backup.
- Nutrition/recovery companion sheet.
- Gym-day reminders: needs service worker + manifest + FCM + a scheduler. Cloud Functions need the paid Blaze plan, so look at a free scheduler (e.g. GitHub Actions cron) first.
