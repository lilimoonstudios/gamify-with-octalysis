# Playground: Technique Tabs — Design Spec

**Date:** 2026-04-20
**Status:** Approved

## Overview

Extend the Octalysis Playground (`playground.html`) so each Core Drive's demo panel shows multiple technique demos via a tab bar. The existing single demo per drive becomes Tab 1. 24 new interactive demos are added in the first batch (3 per drive). A browse-all Technique Library section is a separate future step.

## Structure

### Tab bar in the demo panel
- Appears at the top of the right-hand demo panel when a drive is selected
- Tab 1 is always the existing demo — nothing is removed
- Drive-colored underline on the active tab (uses existing `--cd1` through `--cd8` CSS vars)
- Tabs scroll horizontally on mobile; panel width and overall 3-panel layout unchanged
- Smooth fade transition (opacity + translateY) between tab content — matches existing `panel-content` animation
- Tab names ≤12 chars to stay compact

### Demo panel structure change
Current:
```
[Live Demo label]
[demo-content div]  ← single demo rendered here
```

After:
```
[Live Demo label]
[tab-bar div]       ← new: drive-colored tabs
[demo-content div]  ← same div, now renders active tab's demo
```

## First batch — 24 new demos

| Drive | Color | Existing (Tab 1) | New Tab 2 | New Tab 3 | New Tab 4 |
|---|---|---|---|---|---|
| CD1 Epic Meaning | #F59E0B | Mission Counter | Narrative | Humanity Hero | Co-creationist |
| CD2 Accomplishment | #3B82F6 | XP Level-Up | Leaderboard | Boss Fight | Quest List |
| CD3 Creativity | #8B5CF6 | Combo Builder | Chain Combos | Milestone Unlock | Dynamic Feedback |
| CD4 Ownership | #10B981 | Passport Stamps | Avatar Builder | Virtual Goods | Build from Scratch |
| CD5 Social Influence | #EC4899 | Live Pulse | Trophy Shelf | Group Quest | Social Gifting |
| CD6 Scarcity | #F97316 | Event Ticket | Appointment Dynamics | Last Mile Drive | Prize Pacing |
| CD7 Curiosity | #06B6D4 | Scratch Card | Easter Egg | Glowing Choice | Rolling Rewards |
| CD8 Loss Avoidance | #EF4444 | Streak Tracker | Progress Loss | FOMO Punch | Sunk Cost Prison |

## Demo concepts

### CD1
- **Narrative** — A story unfolds in steps as the user clicks "next". Each step reveals a new chapter line, showing how narrative pulls you forward.
- **Humanity Hero** — "You are helping X people" framing. A counter shows the real-world impact of the user's action (e.g. "Your contribution teaches 3,200 students").
- **Co-creationist** — A feature vote widget. User picks between two product directions; the bar shows live "community votes" shifting in response.

### CD2
- **Leaderboard** — Ranked list with the user highlighted. A "nudge" link shows how many XP away the next rank is.
- **Boss Fight** — A quiz where each correct answer depletes the boss's HP bar. Wrong answers flash red. Boss defeated at 0 HP triggers a reward.
- **Quest List** — A checklist of 4 tasks with a progress bar. Completing all reveals a "Chapter Complete" reward state.

### CD3
- **Chain Combos** — Rapid-tap buttons that build a multiplier. Each tap within 1 second adds to the chain; breaking it resets to ×1.
- **Milestone Unlock** — A locked panel that reveals new content when a threshold (e.g. 500 XP) is crossed. User earns XP by clicking tasks.
- **Dynamic Feedback** — A slider or input that gives instant animated feedback — color, score, and label change as the user adjusts it.

### CD4
- **Avatar Builder** — Pick skin tone, hair, accessory. Avatar preview updates live. Selection is saved in localStorage.
- **Virtual Goods** — A shop with 3 items. User has a coin balance; clicking "buy" deducts coins and moves item to inventory.
- **Build from Scratch** — Click-to-place building blocks to assemble a simple structure. Completion triggers a "built!" state. (Click-only for cross-device compatibility.)

### CD5
- **Trophy Shelf** — A display shelf showing earned badges. Hovering reveals who else earned each badge and when.
- **Group Quest** — A shared progress bar labeled "Community Goal". Each user click advances it. Shows how many people have contributed.
- **Social Gifting** — Send a "boost" to a simulated teammate. An animated gift flies across the panel; a "recipient" XP bar increments. (Simulated — no real multiplayer.)

### CD6
- **Appointment Dynamics** — A daily reward with a countdown to next availability. Shows a 7-day check-in calendar; today's reward is claimable.
- **Last Mile Drive** — Progress bar pre-filled to 85%. Small tasks push it to 100%. Near-completion feeling drives urgency.
- **Prize Pacing** — Tiered reward reveal. Each milestone unlocks the next prize preview, making the end goal feel closer.

### CD7
- **Easter Egg** — A hidden element (small icon tucked in a corner). Clicking it triggers a surprise reveal with a fun message.
- **Glowing Choice** — Three options; one pulses with a glow. Clicking the glowing one gives a bonus; others give a normal result.
- **Rolling Rewards** — A reward meter that fills over time. When full, it pays out and resets. User watches it accumulate passively.

### CD8
- **Progress Loss** — A progress bar slowly draining. A button stops the drain. Shows the anxiety of watching work disappear.
- **FOMO Punch** — A live feed of others taking action ("Carlos just unlocked Expert tier — 2m ago"). Counter shows actions taken while you waited.
- **Sunk Cost Prison** — Shows accumulated investment ("You've spent 4 hours, earned 3 badges, reached Level 5"). A "quit?" button triggers a warning summary.

## Implementation order

One drive per PR, CD1 through CD8. Each PR:
1. Adds the tab bar to `activateDrive()` for that drive
2. Adds `render*` and `init*` functions for the 3 new techniques
3. Wires them into the `renderDemo()` and `initDemo()` dispatch tables

## Future: Technique Library section

A scrollable grid of all techniques below the octagon, filterable by drive. Reuses the same render functions. Out of scope for this batch.
