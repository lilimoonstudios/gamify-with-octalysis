---
name: gamification-design-review
description: Review a specific feature, flow, or screen through the Octalysis motivation lens before it ships. Trigger with "review this feature for motivation," "does this onboarding work?", "is this feature engaging?", or when sharing a Figma link, screenshot, PRD, or flow description for feedback from a gamification/motivation-design angle. Sibling to a general design critique — this one focuses purely on why users will (or won't) want to engage.
---

# Gamification Design Review

A focused design review that asks one question of a feature: **will users actually want to do this?** Uses the 8 Core Drives of the Octalysis Framework (Yu-kai Chou, 2015) as the evaluation lens.

## When to use this skill

- A feature is designed but not shipped and the team wants a motivation check.
- An onboarding flow feels flat and no one can articulate why.
- A PRD reads well but the designer suspects no one will actually do the thing it proposes.

**Do not use** for visual design, accessibility, copy, or general usability — use the sibling `design:design-critique`, `design:accessibility-review`, or `design:ux-copy` skills for those.

## Inputs needed

Ask if not provided:

1. **What is the feature?** One paragraph, plus the user action it's trying to drive.
2. **Who is the user** and what do they already know/feel about this product?
3. **What's the stage?** Onboarding first-time user? Active user? Lapsed user?
4. **What does success look like?** Completion rate? Return visit? Sharing?
5. **Screenshots, Figma link, or a step-by-step of the flow** if available.

## Review structure

Walk through the feature in three passes:

### Pass 1: The "why would they" test

For each step of the flow, ask: *At this moment, which Core Drive is motivating the user to take the next action?*

Good features have a clear answer at every step. Dead-feeling features have gaps — the user is expected to act on willpower alone.

Report as a step-by-step table:

```
Step 1 → [Action] → Primary drive: CD_ | Why it works (or doesn't)
Step 2 → [Action] → Primary drive: CD_ | ...
```

Flag any step where the answer is "none" or "CD6/CD8 only."

### Pass 2: The hat balance check

Count drives activated across the flow:

- Which White Hat drives (CD1–5) are present?
- Which Black Hat drives (CD6–8) are present?

**Warning signs:**

- Flow relies primarily on CD8 (loss) or CD6 (scarcity) without balancing CD1, CD3, or CD5.
- No CD1 (meaning) or CD3 (creativity) anywhere — the feature is purely transactional.
- Heavy CD2 (points/progress) with no intrinsic motivation behind it.

### Pass 3: The journey-phase check

Is the dominant drive appropriate for the user's stage?

- **New user (exploration)** → should feel CD1 (meaning) and CD5 (social proof).
- **Onboarding** → CD2 (quick wins) and CD4 (early personalization).
- **Habit** → CD6/CD8 *carefully*, paired with CD2.
- **Mastery** → CD3 (creative expression) and CD7 (discovery).

Mismatches — e.g. CD8 urgency aimed at a brand-new user — are design bugs.

## Output format

### Summary

One paragraph: what the feature is, what drives it relies on, and the top issue.

### Step-by-step motivation map

The table from Pass 1.

### Strengths (what's working)

2–3 bullets of what the design does well, with the drive it leverages.

### Risks (what to fix before shipping)

2–4 ranked risks. Each includes:

- The issue (one sentence).
- Which drive is missing or misused.
- Concrete suggestion (a technique from the framework, not just "make it better").

### Ship-readiness verdict

One of:

- ✅ **Ship it** — well-motivated, drives match the stage.
- 🟡 **Ship with a tweak** — one fix needed, noted above.
- 🔴 **Rework before shipping** — the motivational foundation is off.

## Worked example

See `skills/gamification-design-review/example-review.md` for a review of a mobile-app onboarding flow. *(To be added.)*

## Attribution

Built on the Octalysis Framework by Yu-kai Chou. See the repo's `ATTRIBUTION.md` for full credit.
