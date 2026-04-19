---
name: octalysis-audit
description: Score a product, feature, or flow across all 8 Octalysis Core Drives (0–10 each), produce the resulting octagon shape, and flag imbalances. Trigger when the user says "audit with Octalysis," "score this against the 8 core drives," "how motivating is this product," or when they share a product/feature description and ask for a gamification assessment.
---

# Octalysis Audit

Score a product against the 8 Core Drives of the Octalysis Gamification Framework (Yu-kai Chou, 2015) and produce an actionable assessment.

## When to use this skill

- A PM, designer, or founder describes a product or feature and asks how engaging it is.
- Someone shares a competitor they want to learn from.
- Someone shares their own product and asks where to invest next in motivation design.

**Do not use** for full product strategy, pricing, or go-to-market questions — this skill is narrowly focused on motivation design.

## Inputs needed

Ask if not provided:

1. **Product/feature name** and a one-paragraph description of what it does.
2. **Target user** and **the primary job** they're using it for.
3. **Current stage**: exploration, onboarding, habit, mastery, or endgame.
4. **Optional**: screenshots, URL, or a list of the main user actions.

## How to score

For each of the 8 Core Drives, give a score from 0–10 based on how strongly the product activates that drive *for its target user in its current stage*. Use these anchors:

- **0–2** — absent or barely touched
- **3–5** — present but weak; the drive is implied, not designed for
- **6–8** — deliberately designed for; a core part of the experience
- **9–10** — the product's defining motivational loop

For each score, cite the specific feature, copy, or mechanic that earned the score. If there isn't one, say "not found."

## Output format

Produce three sections:

### 1. Scorecard

```
CD1 Epic Meaning & Calling           [X/10]   evidence: ...
CD2 Development & Accomplishment     [X/10]   evidence: ...
CD3 Empowerment of Creativity        [X/10]   evidence: ...
CD4 Ownership & Possession           [X/10]   evidence: ...
CD5 Social Influence & Relatedness   [X/10]   evidence: ...
CD6 Scarcity & Impatience            [X/10]   evidence: ...
CD7 Unpredictability & Curiosity     [X/10]   evidence: ...
CD8 Loss & Avoidance                 [X/10]   evidence: ...

White Hat total: ___/50    Black Hat total: ___/30
Left Brain total: ___/40   Right Brain total: ___/40
```

### 2. Shape reading

Describe the resulting octagon shape in one paragraph:

- Which drives dominate? (the "big sides")
- Which are missing? (the "flat sides")
- Is the product White Hat-heavy, Black Hat-heavy, or balanced?
- Is it Left Brain (analytical/transactional) or Right Brain (emotional/creative)?

Flag specifically: **Black Hat totals above 15/30 without compensating White Hat strength are a design warning.**

### 3. Top three recommendations

Rank the three highest-leverage moves. For each:

- **What to add or change** (one sentence).
- **Which drive it strengthens** and why that drive matters for this user at this stage.
- **Concrete example technique** from the framework (e.g. "add a milestone ceremony at Level 5 to deepen CD2").

End with a one-line verdict: *"This product is motivated primarily by [drives] and should next invest in [drive]."*

## Worked example

See `skills/octalysis-audit/example-audit.md` for a complete walkthrough of auditing a habit-tracker app. *(To be added.)*

## Attribution

This skill applies the Octalysis Framework by Yu-kai Chou. For the authoritative treatment, read *Actionable Gamification* (2015) and visit yukaichou.com. This skill is an educational tool, not a substitute for the book or the Octalysis Group's professional training.
