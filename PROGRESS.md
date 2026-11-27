# OS Process Lifecycle & Task Manager Dashboard — Progress

Marks: `[x]` done and exercised · `[~]` in progress · `[ ]` not started · `[!]` blocked

## 🆕 What changed in this update

**2026-11-25 — simplified on request ("fewer features" + "calmer look").** `index.html` was
rewritten: 669 lines down to about 480. Everything below marked `[x]` was re-exercised in
headless Chrome (56 scripted checks, 0 console errors).

Removed (extras that went beyond the original spec):

- Random I/O and its toggle — Waiting is now reached only through the Request I/O button
- "On the CPU" card, the CPU sparkline, the per-state breakdown, time-slice pips
- Per-row burst progress bar and the "ticks left" countdown on Waiting badges
- Terminal extras: `taskkill /im`, `/t`, wildcards, `tasklist` filters on PID/STATUS/`ne`, `selftest`
- Reset button (reload the page instead) and Clear log button

Calmer look:

- The whole dashboard fits one window at 1440×900 or larger; the table, terminal and log scroll inside their own panels
- Three slim KPI cards instead of four tall ones; plain bars instead of segmented ones
- No glow, pulse or blink; the only motion left is the transition animation the spec asks for
- Kill buttons are neutral until hovered; badges lost their borders; the event log is one line per event

Nothing is blocked.

## How to run

Open `index.html` in a browser. No server, no build, no dependencies.

## 1. Interactive process state machine diagram

- [x] Five states drawn as an SVG: New, Ready, Running, Waiting, Terminated
- [x] Six labelled transitions: Admitted, Dispatched, Interrupt, I/O or Event Wait, I/O or Event Completion, Exit
- [x] States holding processes are highlighted, with a live process count on each
- [x] Transitions animate: the edge flashes in the destination colour and a dot travels along it
- [x] Clicking a state filters the task table; clicking again (or the chip) clears it
- [x] Keyboard: states are focusable and Enter/Space toggles the filter

## 2. Process creation & lifecycle simulation

- [x] Create form: image name (with suggestions), burst time, memory in MB
- [x] Input validation: empty name, burst outside 1–99, memory over the free amount — each rejected with a reason
- [x] "Spawn 5 processes" batch button
- [x] Engine: New → Ready (one admission per tick) → Running; round-robin, 3-tick quantum, one CPU
- [x] Play / Pause, Step (exactly one tick), Speed 0.5× / 1× / 2× / 4×
- [x] Automatic Interrupt on quantum expiry when another process is in Ready
- [x] Row button — Interrupt: Running → back of the Ready queue
- [x] Row button — Request I/O: Running → Waiting for 2–5 ticks, then → Ready
- [x] Row button — Kill: any live process → Terminated immediately
- [x] Burst complete → Exit → Terminated, next Ready process dispatched in the same tick

## 3. Windows `tasklist` & `taskkill` live panel

- [x] Live table: Image Name, PID, State badge, CPU Time, Mem Usage (K), Actions
- [x] PIDs are unique random 4-digit numbers, never reused within a run
- [x] Colour-coded state badges
- [x] Search bar styled as `tasklist /fi "IMAGENAME eq …"`, matches name or PID as you type
- [x] Rows update in place, so a click still lands while the table refreshes at 4× speed
- [x] "Clear terminated" removes finished rows
- [x] Command prompt: `tasklist`, `tasklist /fi "IMAGENAME eq name"`
- [x] Command prompt: `taskkill /pid <PID>`, `taskkill /f /pid <PID>`
- [x] Windows-style feedback: SUCCESS, `ERROR: The process "…" not found.`, invalid option, missing target, unknown command
- [x] `help`, `cls`; Up/Down arrows recall earlier commands

## 4. System resource telemetry

- [x] CPU utilization % over the last 20 ticks, with Busy/Idle status and a bar
- [x] Total memory allocated out of 16,384 MB, with a bar; memory is released on termination
- [x] Active process count (plus how many are in the ready queue and waiting on I/O)
- [x] System event log: every transition with timestamp, process, PID, from → to, and cause (last 200)

## 5. UI / technical constraints

- [x] Single `index.html`, zero external requests
- [x] Dark system-monitor look
- [x] One-window layout at ≥1100px wide and ≥720px tall; single scrolling column below that
- [x] No horizontal page overflow at 390px or 900px wide
- [x] Process names are escaped everywhere they render (HTML-injection attempt checked)
- [x] Visible keyboard focus on every control

## Decisions made where the spec was silent

- **Both `taskkill` forms terminate immediately.** Only the message differs ("Sent termination signal" vs "has been terminated").
- **Terminated rows stay in the table, dimmed,** until "Clear terminated", so the Terminated state has something to show when clicked. `tasklist` in the terminal lists live processes only.
- **The page starts playing** with four seeded processes.
- **1 tick = 1 second of CPU Time.**

## Not built (deferred, not blocked)

- [ ] Other scheduling algorithms (FCFS, SJF, priority) — marked with a `ponytail:` comment in the code
- [ ] Multiple CPU cores
- [ ] Saving state across reloads
- [ ] An automated test kept in the repo — the browser walkthrough ran from a temporary script, and the in-page `selftest` was removed with the other extras

## Known limits

- On phones the Actions column and the right half of the diagram need a sideways swipe.
- Testing used headless Chrome; the Chrome extension was not connected, so nobody has clicked through it in a visible window yet.

## Recommended next step

Open `index.html` and check the simpler version against what you need. If it is still too much, say which panel should go.
