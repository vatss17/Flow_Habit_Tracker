# Flow — Habit Tracker

**Stop tracking. Start doing.**

Flow is a habit tracker that doesn't ask whether you did it — it works it out. Instead of asking you to remember, open an app and tick a box, Flow reads the signals your devices already produce (browser activity, a watch, a band, smart glasses, your phone), decides which habit is happening, coaches you through it, and verifies completion with evidence.

Built as a hackathon prototype. Everything in the page runs for real; the wearable sensor streams are simulated.

👉 **[Open the live demo](https://vatss17.github.io/Flow_Habit_Tracker/)** *(enable GitHub Pages on the `main` branch to activate this link)*

---

## The problem

Habit apps solve "how do I remember to track my habits?" — not "how do I actually build the habit?"

| | |
|---|---|
| **Manual logging is friction** | You stop, open an app, and record something you just did — every time. |
| **Streaks are all-or-nothing** | Miss one day and it feels like the whole thing is gone. |
| **Fixed goals ignore real life** | 60 minutes is realistic on Tuesday and impossible on a day with three deadlines. |
| **Reminders have no context** | A push notification doesn't know if you're busy, already doing it, or exhausted. |
| **Trackers report, they don't explain** | You see `4 / 7 completed`, never "you succeed more when you start before 7pm". |

## The idea

Four layers, one loop:

```
SENSE  →  UNDERSTAND  →  SUPPORT  →  ADAPT
```

- **Sense** — collect whatever signals are available: tab focus, keyboard activity, heart rate, steps, sleep stages, objects in view, phone pickups.
- **Understand** — score those raw signals as *evidence* and decide which habit is happening, and for how long.
- **Support** — give minimal, contextual feedback *while* it's happening, instead of a notification at a fixed time.
- **Adapt** — feed real outcomes back into targets and insights, so goals follow behaviour instead of wishful thinking.

A completed habit is never a boolean:

```
habit_status: verified
confidence: 0.94
evidence: ["session_duration", "active_window", "minimal_phone_activity"]
verified_by: "laptop + phone"
```

---

## What's implemented

### Devices tab — pair a signal source
Five sources: **this laptop** (always on — the page itself is a sensor), **smartwatch**, **smartband**, **smart glasses**, **phone**. Each pairs with a real handshake sequence, then streams its own sensor events with live metrics, and unlocks the habits it can verify.

### Today tab — habits that track themselves
- Habits progress **on their own** while paired devices stream — no taps.
- A live **session monitor**: detection → evidence chips → mid-session coaching → a gentle nudge when your signals drop → automatic verification with a confidence score.
- Habits whose device isn't paired say so (`needs Smart glasses`) and offer a shortcut.
- Add your own habit, or one-tap nine templates a checkbox app can't verify.

### Insights tab — behaviour, not streaks
- **Recovery score** — of the days you missed, how often did you come back the next day? Computed from your real session history.
- **7-day chart** per habit against its target.
- **Morning vs evening** completion rates.
- **Adaptive goals** — Flow finds the habit that's fighting you and proposes a target you'd actually clear.
- **AI coach** — reads your week and says one useful, quantitative thing.

### Manual tab — for judges
Six steps with "Show me" buttons that drive the app for you, a side-by-side comparison against a typical habit tracker, eight use cases only a sensor-driven tracker can verify, the evidence model, and an honest "what's real vs simulated" box.

### Guided tour
A 7-step tour that opens on first visit and actually *performs* each step — pairs the band, triggers a detection, runs a session, opens your insights.

---

## Things only Flow can verify

A checkbox app can only record what you claim. With signals, these become measurable:

| Habit | Evidence |
|---|---|
| Drinking water | glasses · `object_detect` — a glass reaching your mouth |
| Sitting upright | glasses · `gaze_dwell` — head angle over a work block |
| Reading paper | glasses · `visual_context` — reading with no screen in view |
| Actually calming down | watch · `hrv` — HRV rising, not a timer running out |
| Sleeping on time | band · `sleep_stage` — sleep onset, not a bedtime promise |
| Phone-free meals | phone · `pickup_count` — 30 minutes, zero pickups, at home |
| Training hard enough | watch · `heart_rate` — heart-rate zones say it was real |
| Walking meetings | band + laptop — steps rising while a call is active |

---

## Flow vs. a typical habit tracker

| What matters | Typical habit tracker | Flow |
|---|---|---|
| Getting a habit recorded | You open the app and tap a checkbox | A paired device notices and verifies it — zero taps |
| What a completion means | You said you did it | Evidence + a confidence score you can audit |
| Missing a day | 🔥 Streak resets to zero | Recovery score: did you come back within 24h? |
| Unrealistic targets | Stays 60 min until you edit it | Flow proposes one you'll actually clear |
| Coaching | A reminder at a fixed time | A nudge when it sees you drift mid-session |
| Habits it can handle | Anything you promise to self-report | Anything a sensor can witness |
| New hardware | A separate integration, or nothing | One more evidence source into the same engine |

---

## Running it

No build step, no dependencies, no server required.

```bash
git clone https://github.com/vatss17/Flow_Habit_Tracker.git
cd Flow_Habit_Tracker
open index.html          # macOS — or just double-click the file
```

Or serve it locally:

```bash
python3 -m http.server 8000    # then visit http://localhost:8000
```

To host it: enable **Settings → Pages → Deploy from branch → `main` / root**.

### Tech notes

- **One file.** `index.html` — vanilla HTML, CSS and JavaScript, no framework, no bundler.
- **State** lives in `localStorage`, so your habits survive a reload. The ↺ button in the header resets to a clean demo state.
- **Theming** is token-driven and follows your system light/dark preference.
- **Optional AI.** When the page is opened inside Claude it asks a real model for the weekly insight; anywhere else it falls back to Flow's own analysis of the same numbers. Both paths work.
- **Fonts** come from Google Fonts (Quicksand, Nunito Sans, DM Mono) and degrade to system fonts offline.

---

## What's real, what's simulated

| | |
|---|---|
| ✅ **Real** | The detection engine, evidence scoring, recovery maths, adaptive goals, the AI coach, persistence |
| 🟡 **Simulated** | Watch / band / glasses / phone sensor streams and the pairing handshake |
| ❌ **Not collected** | No camera, microphone, location or health data is read. Nothing leaves your browser. |

Phase 2 swaps the simulated stream for real device APIs. Nothing else in the architecture has to change — that's the whole point of an evidence engine.

## Roadmap

| Phase | Scope |
|---|---|
| 1 — now | Browser signals, simulated device streams, full app loop |
| 2 | Phone integrations: screen time, motion, location |
| 3 | Apple Health / Google Health Connect / wearable APIs |
| 4 | Smartwatch + ring: heart rate, motion, workouts |
| 5 | AI glasses: visual context, voice, environment |
| 6 | Cross-device evidence engine verifying complex real-world behaviour |

Eventually: your environment *is* the tracker.

---

## Success metrics we care about

- **Manual logging eliminated** — % of completions that needed no input at all *(the headline number)*
- **Detection accuracy** — % of detected sessions correctly identified
- **Recovery rate** — % of missed habits followed by a return within 24h
- **Completion rate** — % of planned habits completed

## Stack

`HTML` · `CSS` · `JavaScript` — deliberately dependency-free so it runs anywhere, instantly. Intended production stack: Next.js + React + Tailwind on Supabase (Postgres, auth, realtime), with an LLM API for coaching and insight generation.

---

Built for a hackathon by [@vatss17](https://github.com/vatss17).
