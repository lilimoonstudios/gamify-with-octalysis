---
name: ethical-gamification-check
description: Flag dark patterns, manufactured scarcity, and Black Hat Core Drive overuse in a product, feature, or flow. Trigger with "is this ethical?", "check this for dark patterns," "are we being manipulative?", "audit for Black Hat overuse," or when a designer/PM is concerned their gamification is crossing into coercion. The "don't be evil" checker for motivation design.
---

# Ethical Gamification Check

Audit a product, feature, or flow for coercive gamification patterns. Based on the Black Hat drives in the Octalysis Framework (Yu-kai Chou, 2015) plus common dark pattern taxonomies.

## When to use this skill

- A feature relies heavily on urgency, scarcity, or loss aversion and someone internal is uncomfortable.
- A user has complained that the product feels manipulative, addictive, or anxiety-inducing.
- A team wants a pre-ship ethical review before launching a retention mechanic.
- A designer is personally unsure whether a technique crosses a line.

**This skill is about ethical design, not legal compliance.** For dark-pattern law compliance (GDPR, CCPA, FTC), consult a lawyer.

## Inputs needed

Ask if not provided:

1. **What is the feature or flow?** Description, screenshots, or copy.
2. **What is the user outcome it's trying to drive?** (e.g. retention, purchase, referral)
3. **What's the user's likely emotional state** when they hit it? (Rushed? Relaxed? In a decision moment?)
4. **Is anything being used or created under a deadline?** Real or manufactured?

## What we look for

### 1. Black Hat overuse

Tally how many of the three Black Hat drives are activated:

- **CD6 Scarcity & Impatience** — countdown timers, "only 3 left," invite-only, limited editions.
- **CD7 Unpredictability & Curiosity** — variable rewards, loot boxes, gacha, infinite scroll.
- **CD8 Loss & Avoidance** — streak loss warnings, "you'll miss out," profile incompleteness nags.

**Score the intensity** of each (0 = absent, 10 = core mechanic). A score of 15+ across Black Hat without a corresponding 25+ across White Hat (CD1–5) is a warning.

### 2. Specific dark patterns

Check for each of these. Flag severity: 🟢 fine, 🟡 caution, 🔴 dark pattern.

- **Fake scarcity** — timers that reset, "limited" items that aren't.
- **Manufactured loss** — creating progress the user didn't want, then threatening to remove it (e.g. pseudo-streaks for non-essential actions).
- **Confirmshaming** — "No thanks, I don't want to save money."
- **Roach motel** — easy to sign up, hard to cancel.
- **Forced continuity** — free trial with hidden auto-charge.
- **Bait and switch** — promised reward at action X, delivered reward Y.
- **Privacy zuckering** — guilt-tripping users into sharing more than they want.
- **Disguised ads** — content that looks like feed items.
- **Aggressive re-engagement** — push notifications designed to generate anxiety.
- **Sunk cost exploitation** — reminding users how much they've "invested" to prevent churn.
- **Loot box mechanics aimed at minors** — random-reward purchases targeting young users.

### 3. Population-specific risk

Some drives are riskier for some users. Flag if:

- The product is used by **children or teens** and relies on CD7 (variable reward), CD5 (social comparison), or CD8 (loss).
- Users are in a **vulnerable context** (financial stress, health anxiety, grief, addiction recovery) and the product uses urgency or loss mechanics.
- The product treats **essential services** (banking, healthcare, government) as a game — usually inappropriate.

## Output format

### 1. Black Hat tally

```
CD6 Scarcity       intensity: X/10   evidence: ...
CD7 Curiosity      intensity: X/10   evidence: ...
CD8 Loss           intensity: X/10   evidence: ...
Black Hat total: X/30    White Hat counterbalance: X/50
```

### 2. Dark pattern scan

A table of each pattern checked, the verdict (🟢/🟡/🔴), and one-sentence evidence if flagged.

### 3. Risk populations

Explicit callout if the feature poses elevated risk to a specific user population.

### 4. Verdict and fixes

One of:

- ✅ **Ethically sound** — Black Hat drives used with real-world justification and White Hat counterbalance.
- 🟡 **Review recommended** — one or two patterns worth reconsidering, listed with fixes.
- 🔴 **Do not ship as-is** — at least one red-flag dark pattern or uncompensated Black Hat overuse. Provide specific rewrites.

### 5. Positive alternatives

For each flagged issue, propose a White Hat alternative that achieves the same business goal. The point is to improve the feature, not just block it.

Example:

> 🔴 *"Cart expires in 3:00"* with a fake resetting timer is manufactured loss.
> → Replace with: *"Your saved cart is here whenever you want it"* paired with CD4 (Ownership — "your cart" framing) and CD2 (a gentle reminder of free shipping at $X).

## Designer principles (for reference)

From Yu-kai Chou's own guidance on ethical Octalysis:

1. **Lead with White Hat, accent with Black Hat.** Urgency without meaning breeds resentment.
2. **Use only genuine scarcity.** Real deadlines and real limits are trust-builders. Fake ones are credibility-destroyers.
3. **Never manufacture losses.** Only protect losses the user genuinely cares about.
4. **Protect vulnerable populations.** Children and anxious users deserve extra guardrails.
5. **Make the off-ramp easy.** If a user wants to disengage, let them — gracefully.

## Attribution

Grounded in the Octalysis Framework by Yu-kai Chou and informed by general dark pattern taxonomies (Harry Brignull's deceptive.design, FTC guidance, etc.). See the repo's `ATTRIBUTION.md`.
