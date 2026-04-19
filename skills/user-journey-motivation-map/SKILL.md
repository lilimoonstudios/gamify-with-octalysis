---
name: user-journey-motivation-map
description: Map which Octalysis Core Drives should dominate at each phase of a user's journey — Discovery, Onboarding, Scaffolding, Endgame — for a specific product and user. Trigger with "which drives matter at onboarding?", "map motivation across our user journey," "why are users dropping off at week 2?", or when the user is designing or fixing a multi-stage flow and needs to know which motivational levers belong where.
---

# User Journey Motivation Map

Produce a motivation map: which Core Drives should dominate at each phase of a user's journey with a product. Based on the Four Experience Phases described in the Octalysis Framework (Yu-kai Chou, 2015).

## When to use this skill

- A product has weak retention at a specific stage (e.g. week-2 drop-off) and the team needs to know which motivational drive is missing.
- A PM is planning a new product and wants to know which drives to design for at each phase.
- An onboarding works but habits don't form — classic sign the motivation shifts aren't being designed for.

**Do not use** for one-off feature reviews (use `gamification-design-review` instead) or full product audits (use `octalysis-audit`).

## Inputs needed

Ask if not provided:

1. **Product** and **target user** (1–2 sentences each).
2. **The primary user goal** over time (what does "good" look like after 3 months of use?).
3. **Current phase definitions** (how does the team think about user lifecycle today? e.g. Acquisition / Activation / Retention / Referral, or week-1 / month-1 / month-6).
4. **Where is the friction?** Any known drop-off points, numbers if available.

## The Four Experience Phases

Map the product's lifecycle onto Yu-kai's four phases:

### 1. Discovery (pre-signup through first visit)

*"Why should I even try this?"*

- **Primary drives**: CD1 (Epic Meaning), CD5 (Social proof, Relatedness).
- **Secondary**: CD7 (Curiosity — intrigue, mystery).
- **Avoid**: CD8 (Loss) — it has nothing to lose yet.

### 2. Onboarding (first session through first "aha")

*"Can I actually do this?"*

- **Primary drives**: CD2 (Quick wins, early progression), CD4 (Personalization, claiming "my stuff").
- **Secondary**: CD3 (Meaningful first choice), CD1 (reinforce mission).
- **Avoid**: overusing CD5 (social pressure before they've done anything) or CD6 (scarcity before they see value).

### 3. Scaffolding (habit formation — days 3 through month 2)

*"Why should I come back?"*

- **Primary drives**: CD2 (long-arc progression), CD4 (building their territory), CD5 (connection to others).
- **Secondary**: CD6 (appointment dynamics) and CD8 (streak protection) — *sparingly*.
- **Avoid**: heavy CD8 without CD1/CD3 — breeds resentment.

### 4. Endgame (mastery — month 3 onward)

*"Why keep engaging when I already 'got it'?"*

- **Primary drives**: CD3 (Creative self-expression), CD5 (mentorship, community identity), CD7 (ongoing discovery).
- **Secondary**: CD1 (being part of the mission long-term), CD4 (showing off their "collection").
- **Avoid**: resetting CD2 (new XP systems for veterans feels condescending).

## Output format

### 1. Phase map

A table or grid:

```
Phase            Primary drives   Supporting drives   Key techniques
Discovery        CD1, CD5         CD7                 Narrative, social proof, teaser
Onboarding       CD2, CD4         CD3, CD1            Quick win, personalize, mission reinforce
Scaffolding      CD2, CD4, CD5    CD6 (light), CD8    Long progress bar, community, streak
Endgame          CD3, CD5, CD7    CD1, CD4            UGC, mentorship, Easter eggs
```

### 2. Gap analysis

Given what the product currently does, where are the motivational misalignments?

For each phase:

- **What's working** (drives already well-supported).
- **What's missing** (drives absent or weak for this phase).
- **What's misused** (drives present but wrong for the phase — e.g. CD8 during onboarding).

### 3. Top three phase-specific fixes

Rank the three highest-leverage interventions. For each:

- **Phase** (which phase it targets).
- **Drive to add or strengthen**.
- **Concrete mechanic** (a specific feature, copy change, or flow tweak).
- **Expected signal** (what metric should move).

## Worked example

See `skills/user-journey-motivation-map/example-map.md` for a motivation map of a meditation app losing users at week 3. *(To be added.)*

## Attribution

The Four Experience Phases and the Core Drives come from the Octalysis Framework by Yu-kai Chou. See the repo's `ATTRIBUTION.md`.
