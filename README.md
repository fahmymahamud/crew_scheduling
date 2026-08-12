# Crew Scheduling — interactive demo

A single-file, zero-dependency web demo of a weekly scheduling workflow for a small
field-service business (cleaning, maintenance, installs — anything where a handful of
crew members get sent to jobs).

It covers the whole loop that most small operators still run by hand in a group chat:

1. **Crew update availability** — each person ticks the days they can work and sets their hours.
2. **Office posts a job** — client, address, date, time, how many people, which services.
3. **Office assigns crews and sends** — the app shows who is actually free for that job,
   you tick names, and it writes the WhatsApp roster message for you.

Everything in the demo works. Nothing is saved — refresh and it returns to the sample week.

**[▶ Open the demo](https://fahmymahamud.github.io/crew_scheduling/)** · 

---

## What it shows

| Screen | What you can do |
| --- | --- |
| **1 · Crew update availability** | A phone-shaped form. Tick days, set from/until times, add a remark ("must leave by 1pm"). The "Pretend to be" dropdown lets you switch between 10 crew members so you can see both sides of the system. |
| **2 · Office posts a job** | A month calendar colour-coded by staffing state, plus a new-job form with a tick-list of services that totals the price as you go. |
| **3 · Assign crews & send** | Per job, everyone is sorted into **free for the whole job** / **free that day but not the full window** / **not available**, with their submitted hours shown on the chip. Pick a lead, watch the "2 of 3" counter, then generate a copy-ready message. |

### Details worth clicking

- **Availability matching** — a crew member only lands in the green group if a submitted
  window fully covers the job's start *and* end time. Everyone else is still assignable,
  just flagged.
- **Calendar states** — green *fully staffed*, amber *short of crew*, grey *not assigned*,
  struck-through *cancelled*, with a running monthly tally underneath.
- **Cancel vs delete** — a cancelled job stays on the calendar for the record but drops out
  of the generated message; delete removes it for good (with a confirmation).
- **Lead handling** — naming a lead adds them to the crew automatically and lists them
  first, as `[Name]`, in the message.
- **Message generation** — grouped by day, formatted for WhatsApp, and fully editable in
  the textarea before you copy it.

## Running it

No build step, no install, no server required.

```bash
# just open it
start index.html          # Windows
open index.html           # macOS

# or serve it, if you prefer
python -m http.server 8000
```

Then browse to <http://localhost:8000>.

## Publishing it

The whole demo is one static file, so any static host works.

- **GitHub Pages** — push the repo, then Settings → Pages → deploy from `main` / root.
  The demo lands at `https://<user>.github.io/<repo>/`.
- **Netlify / Cloudflare Pages / Vercel** — drag the folder onto the dashboard, or point
  the project at the repo with no build command and the root as the publish directory.

## How it is built

| | |
| --- | --- |
| **Stack** | One HTML file. Vanilla JavaScript, no framework, no bundler, no dependencies. |
| **Size** | ~690 lines / ~39 KB, including CSS and seed data. |
| **State** | A single in-memory object, `S`, holding `staff`, `avail`, `notes`, `jobs` and the current week/month. "Start over" simply calls `seed()` again. |
| **Dates** | The sample week is generated relative to today, so the demo is never stale — jobs always appear in the current week. |
| **Rendering** | Plain template literals into `innerHTML`, with every interpolated value passed through an `esc()` HTML escaper. |
| **Layout** | CSS grid and flexbox; the calendar scrolls horizontally on narrow screens and the two-column roster collapses to one below 980px. |

The interesting logic is small enough to read in one sitting:

- `classify(job)` — splits the roster into matched / partial / off for a given job.
- `stateOf(job)` — the calendar's colour state.
- `renderRoster()` — builds the assignment cards, chips and counters.
- The `#r-generate` handler — turns the week's staffed jobs into the WhatsApp message.

## About the data

Every name, phone number, address, service and price in this demo is **invented sample
data**, written purely to make the screens legible. Crew members are `Crew 1`–`Crew 10`
and clients are `Client A`–`Client E`. Nothing here belongs to a real business or a real
person.

## Status

This is a front-end prototype: state lives in memory and disappears on refresh. A
production build of the same workflow adds a database, one private link per crew member,
saved history, and reporting on top of the identical three screens.
