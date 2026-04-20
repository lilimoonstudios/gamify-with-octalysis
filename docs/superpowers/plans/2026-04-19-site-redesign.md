# Site Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refactor the single-page site into two self-contained pages — a light editorial Learn page (`index.html`) and a dark interactive Playground page (`playground.html`).

**Architecture:** Both pages are vanilla HTML/CSS/JS with no build step, served from `site/`. The Learn page keeps all educational content and replaces the segmented-pie octagon with a spoke-wheel hero. The Playground page is dark-themed with a 3-panel octagon hub and 8 live interactive demos.

**Tech Stack:** Vanilla HTML5, CSS custom properties, inline SVG, vanilla JS (IntersectionObserver, localStorage). GitHub Pages deploys from `./site`.

---

## File Map

| File | Status | Responsibility |
|------|--------|---------------|
| `site/index.html` | Modify | Learn page — nav, spoke-wheel hero, drive cards (richer), apply, about, footer |
| `site/playground.html` | Create | Playground page — dark shell, 3-panel octagon hub, 8 demos |
| `.gitignore` | Modify | Add `.superpowers/` entry |

---

## Task 1: Housekeeping

**Files:**
- Modify: `.gitignore`
- Create: `site/playground.html` (empty skeleton)

- [ ] **Step 1: Add `.superpowers/` to `.gitignore`**

Open `.gitignore` and append:
```
.superpowers/
```

- [ ] **Step 2: Create `site/playground.html` skeleton**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Octalysis Playground — Experience the Drives</title>
  <style>
    :root {
      --bg: #0d1117; --surface: #161b22; --border: #30363d;
      --text: #e6edf3; --muted: #8b949e; --dim: #6b7280;
      --cd1:#F59E0B; --cd2:#3B82F6; --cd3:#8B5CF6; --cd4:#10B981;
      --cd5:#EC4899; --cd6:#F97316; --cd7:#06B6D4; --cd8:#EF4444;
    }
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }
    body { background: var(--bg); color: var(--text); font-family: system-ui, -apple-system, sans-serif; }
  </style>
</head>
<body>
  <p style="color:#8b949e;padding:40px;text-align:center">Playground — coming soon</p>
</body>
</html>
```

- [ ] **Step 3: Verify in browser**

Open `site/playground.html` in a browser. Expect: dark background, grey placeholder text.

- [ ] **Step 4: Commit**

```bash
git add .gitignore site/playground.html
git commit -m "chore: add playground skeleton and ignore .superpowers/"
```

---

## Task 2: Learn Page — Updated Navigation

**Files:**
- Modify: `site/index.html` (nav section, roughly lines 44–101)

The current nav has hat/brain filter badges. Replace with: logo pill (purple) · Core Drives / Apply / About links · `🎮 Playground` dark pill.

- [ ] **Step 1: Replace `.nav-bar` CSS and HTML**

Find the `.nav-bar` style block and the `<nav>` HTML. Replace the nav CSS with:

```css
.nav-bar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 10px 32px; background: #fff; position: sticky; top: 0;
  z-index: 300; border-bottom: 1px solid #E5E7EB;
  box-shadow: 0 2px 8px rgba(0,0,0,.06); gap: 16px;
}
.nav-logo {
  font-size: .7rem; font-weight: 800; letter-spacing: 1px;
  text-transform: uppercase; color: #8B5CF6; background: #F5F3FF;
  border: 1px solid #DDD6FE; border-radius: 20px; padding: 5px 14px;
  white-space: nowrap; text-decoration: none;
}
.nav-links { display: flex; align-items: center; gap: 4px; }
.nav-link {
  padding: 7px 14px; font-size: .84rem; font-weight: 600;
  color: #6B7280; text-decoration: none; border-radius: 8px;
  transition: color .15s;
}
.nav-link:hover { color: #111827; }
.nav-link.active { color: #111827; box-shadow: inset 0 -2px 0 #8B5CF6; }
.nav-playground {
  font-size: .75rem; font-weight: 700; padding: 6px 14px;
  border-radius: 20px; background: #0d1117; color: #e6edf3;
  border: 1px solid #30363d; text-decoration: none; white-space: nowrap;
}
.nav-playground:hover { background: #161b22; }
```

Replace the nav HTML with:

```html
<nav class="nav-bar">
  <a href="#" class="nav-logo">Octalysis</a>
  <div class="nav-links">
    <a href="#core-drives" class="nav-link active">Core Drives</a>
    <a href="#apply" class="nav-link">Apply</a>
    <a href="#about" class="nav-link">About</a>
  </div>
  <a href="playground.html" class="nav-playground">🎮 Playground</a>
</nav>
```

- [ ] **Step 2: Verify**

Open `site/index.html`. Nav should show: purple "Octalysis" pill left · three text links center · dark "🎮 Playground" pill right. No hat/brain filter badges.

- [ ] **Step 3: Commit**

```bash
git add site/index.html
git commit -m "feat(learn): update nav — logo pill, section links, playground pill"
```

---

## Task 3: Learn Page — Spoke-Wheel Octagon Hero

**Files:**
- Modify: `site/index.html` (hero section + hero CSS + `buildOctSVG` JS)

Replace the current two-column hero (text left + segmented octagon right) with a centered spoke-wheel layout. The wheel SVG is drawn by JS; 8 cards are absolutely-positioned HTML divs.

- [ ] **Step 1: Replace hero CSS**

Remove the existing `.hero`, `.hero-inner`, `.hero-badge`, `.hero-oct`, `#hero-svg` rules. Add:

```css
/* ── HERO ──────────────────────────────────────────── */
.hero {
  background: #fff; border-bottom: 1px solid #E5E7EB;
  padding: 64px 24px 72px; text-align: center;
}
.hero-eyebrow {
  font-size: .72rem; font-weight: 700; letter-spacing: .8px;
  text-transform: uppercase; color: #8B5CF6; margin-bottom: 12px;
}
.hero h1 {
  font-size: 2.4rem; font-weight: 900; letter-spacing: -.04em;
  line-height: 1.1; color: #111827; margin-bottom: 10px;
}
.hero h1 .grad {
  background: linear-gradient(90deg,#F59E0B,#EC4899,#8B5CF6,#06B6D4);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
}
.hero-sub {
  font-size: 1rem; color: #6B7280; max-width: 520px;
  margin: 0 auto 48px; line-height: 1.7;
}

/* Spoke-wheel container */
.oct-hero {
  position: relative; width: 800px; height: 800px;
  margin: 0 auto;
}
#ring-svg {
  position: absolute; top: 50%; left: 50%;
  transform: translate(-50%,-50%);
  width: 800px; height: 800px;
}
.spoke-card {
  position: absolute; transform: translate(-50%,-50%);
  background: #fff; border-radius: 14px; padding: 12px 14px;
  border: 1.5px solid #E5E7EB;
  box-shadow: 0 2px 8px rgba(0,0,0,.06), 0 8px 24px rgba(0,0,0,.04);
  width: 148px; cursor: pointer; transition: all .2s;
  text-decoration: none; color: inherit;
}
.spoke-card:hover {
  border-color: #2563EB;
  box-shadow: 0 4px 16px rgba(37,99,235,.15), 0 0 0 3px rgba(37,99,235,.1);
  transform: translate(-50%,-50%) scale(1.04);
}
.spoke-card-top { display: flex; align-items: center; gap: 8px; margin-bottom: 6px; }
.spoke-num {
  width: 22px; height: 22px; border-radius: 50%;
  background: #2563EB; color: #fff; font-size: .68rem; font-weight: 800;
  display: flex; align-items: center; justify-content: center; flex-shrink: 0;
}
.spoke-icon-box {
  width: 32px; height: 32px; border-radius: 8px; background: #EFF6FF;
  display: flex; align-items: center; justify-content: center;
  font-size: .95rem; flex-shrink: 0; margin-left: auto;
}
.spoke-name { font-size: .78rem; font-weight: 800; color: #2563EB; line-height: 1.25; margin-bottom: 4px; }
.spoke-desc { font-size: .67rem; color: #6B7280; line-height: 1.45; }

@media (max-width: 900px) {
  .oct-hero { width: 100%; height: auto; aspect-ratio: 1; max-width: 500px; }
  #ring-svg { width: 100%; height: 100%; }
  .spoke-card { width: 100px; padding: 8px 10px; font-size: .7rem; }
  .spoke-name { font-size: .65rem; }
  .spoke-desc { display: none; }
}
```

- [ ] **Step 2: Replace hero HTML**

Remove the existing `.hero` block (everything from `<section class="hero">` to its closing `</section>`). Replace with:

```html
<section class="hero" id="core-drives">
  <div class="hero-eyebrow">The Octalysis Framework</div>
  <h1>The <span class="grad">8 Core Drives</span><br>of Human Motivation</h1>
  <p class="hero-sub">A framework by Yu-kai Chou for designing genuinely engaging experiences. Click any drive to read its full description.</p>

  <div class="oct-hero">
    <svg id="ring-svg" viewBox="0 0 800 800" xmlns="http://www.w3.org/2000/svg"></svg>

    <a href="#drive-1" class="spoke-card" style="left:400px;top:66px">
      <div class="spoke-card-top"><div class="spoke-num">1</div><div class="spoke-icon-box">🌐</div></div>
      <div class="spoke-name">Epic Meaning &amp; Calling</div>
      <div class="spoke-desc">The drive to become part of something bigger than ourselves.</div>
    </a>
    <a href="#drive-2" class="spoke-card" style="left:655px;top:170px">
      <div class="spoke-card-top"><div class="spoke-num">2</div><div class="spoke-icon-box">🏆</div></div>
      <div class="spoke-name">Development &amp; Accomplishment</div>
      <div class="spoke-desc">The drive to level up, improve, and achieve progress.</div>
    </a>
    <a href="#drive-3" class="spoke-card" style="left:726px;top:400px">
      <div class="spoke-card-top"><div class="spoke-num">3</div><div class="spoke-icon-box">🧩</div></div>
      <div class="spoke-name">Empowerment of Creativity</div>
      <div class="spoke-desc">The drive to create, experiment, and get real feedback.</div>
    </a>
    <a href="#drive-4" class="spoke-card" style="left:655px;top:630px">
      <div class="spoke-card-top"><div class="spoke-num">4</div><div class="spoke-icon-box">📦</div></div>
      <div class="spoke-name">Ownership &amp; Possession</div>
      <div class="spoke-desc">The drive to collect, accumulate, and organise.</div>
    </a>
    <a href="#drive-5" class="spoke-card" style="left:400px;top:734px">
      <div class="spoke-card-top"><div class="spoke-num">5</div><div class="spoke-icon-box">👥</div></div>
      <div class="spoke-name">Social Influence &amp; Relatedness</div>
      <div class="spoke-desc">The drive to collaborate, compete, and express social identity.</div>
    </a>
    <a href="#drive-6" class="spoke-card" style="left:145px;top:630px">
      <div class="spoke-card-top"><div class="spoke-num">6</div><div class="spoke-icon-box">⏳</div></div>
      <div class="spoke-name">Scarcity &amp; Impatience</div>
      <div class="spoke-desc">The drive to obsess over what we cannot have yet.</div>
    </a>
    <a href="#drive-7" class="spoke-card" style="left:74px;top:400px">
      <div class="spoke-card-top"><div class="spoke-num">7</div><div class="spoke-icon-box">🎲</div></div>
      <div class="spoke-name">Unpredictability &amp; Curiosity</div>
      <div class="spoke-desc">The drive to know what is unpredictable and new.</div>
    </a>
    <a href="#drive-8" class="spoke-card" style="left:145px;top:170px">
      <div class="spoke-card-top"><div class="spoke-num">8</div><div class="spoke-icon-box">🚩</div></div>
      <div class="spoke-name">Loss &amp; Avoidance</div>
      <div class="spoke-desc">The drive to avoid undesirable outcomes and protect current state.</div>
    </a>
  </div>
</section>
```

- [ ] **Step 3: Replace `buildOctSVG` with spoke-wheel JS**

Remove the entire `buildOctSVG` function and its two call sites. Add this JS (before `</script>` or in a new `<script>` block at end of `<body>`):

```js
(function buildSpokeRing() {
  const SVG_NS = 'http://www.w3.org/2000/svg';
  const svg = document.getElementById('ring-svg');
  if (!svg) return;
  const CX = 400, CY = 400, R_OUTER = 200, R_INNER = 130;

  function vtx(i, r) {
    const a = (-90 + i * 45) * Math.PI / 180;
    return { x: CX + r * Math.cos(a), y: CY + r * Math.sin(a) };
  }
  function octPts(r) {
    return Array.from({length:8}, (_,i) => { const v=vtx(i,r); return `${v.x},${v.y}`; }).join(' ');
  }

  // Glow shadow
  const shadow = document.createElementNS(SVG_NS, 'polygon');
  shadow.setAttribute('points', octPts(R_OUTER + 6));
  shadow.setAttribute('fill', 'none');
  shadow.setAttribute('stroke', 'rgba(37,99,235,.12)');
  shadow.setAttribute('stroke-width', '14');
  shadow.setAttribute('stroke-linejoin', 'round');
  svg.appendChild(shadow);

  // Spokes (behind ring)
  const cardPos = [
    {x:400,y:66},{x:655,y:170},{x:726,y:400},{x:655,y:630},
    {x:400,y:734},{x:145,y:630},{x:74,y:400},{x:145,y:170}
  ];
  cardPos.forEach((pos, i) => {
    const v = vtx(i, R_OUTER);
    const line = document.createElementNS(SVG_NS, 'line');
    line.setAttribute('x1', v.x); line.setAttribute('y1', v.y);
    line.setAttribute('x2', pos.x); line.setAttribute('y2', pos.y);
    line.setAttribute('stroke', '#93c5fd');
    line.setAttribute('stroke-width', '1.5');
    line.setAttribute('stroke-dasharray', '4,3');
    svg.appendChild(line);
  });

  // Outer ring
  const outer = document.createElementNS(SVG_NS, 'polygon');
  outer.setAttribute('points', octPts(R_OUTER));
  outer.setAttribute('fill', '#2563EB');
  outer.setAttribute('stroke', '#1d4ed8');
  outer.setAttribute('stroke-width', '1');
  outer.setAttribute('stroke-linejoin', 'round');
  svg.appendChild(outer);

  // Inner cutout
  const inner = document.createElementNS(SVG_NS, 'circle');
  inner.setAttribute('cx', CX); inner.setAttribute('cy', CY);
  inner.setAttribute('r', R_INNER);
  inner.setAttribute('fill', '#F2F5F9');
  svg.appendChild(inner);

  // Inner edge border
  const innerEdge = document.createElementNS(SVG_NS, 'polygon');
  innerEdge.setAttribute('points', octPts(R_INNER + 4));
  innerEdge.setAttribute('fill', 'none');
  innerEdge.setAttribute('stroke', '#1d4ed8');
  innerEdge.setAttribute('stroke-width', '1.5');
  innerEdge.setAttribute('stroke-linejoin', 'round');
  svg.appendChild(innerEdge);

  // Number nodes on ring
  for (let i = 0; i < 8; i++) {
    const mid = vtx(i + 0.5, (R_OUTER + R_INNER) / 2);
    const c = document.createElementNS(SVG_NS, 'circle');
    c.setAttribute('cx', mid.x); c.setAttribute('cy', mid.y);
    c.setAttribute('r', '16'); c.setAttribute('fill', '#1d4ed8');
    svg.appendChild(c);
    const t = document.createElementNS(SVG_NS, 'text');
    t.setAttribute('x', mid.x); t.setAttribute('y', mid.y + 1);
    t.setAttribute('text-anchor', 'middle');
    t.setAttribute('dominant-baseline', 'middle');
    t.setAttribute('font-size', '11'); t.setAttribute('font-weight', '900');
    t.setAttribute('fill', 'white');
    t.setAttribute('font-family', 'system-ui, sans-serif');
    t.setAttribute('pointer-events', 'none');
    t.textContent = i + 1;
    svg.appendChild(t);
  }

  // Center label
  const ct = document.createElementNS(SVG_NS, 'text');
  ct.setAttribute('x', CX); ct.setAttribute('y', CY - 7);
  ct.setAttribute('text-anchor', 'middle'); ct.setAttribute('dominant-baseline', 'middle');
  ct.setAttribute('font-size', '13'); ct.setAttribute('font-weight', '900');
  ct.setAttribute('fill', '#1e40af'); ct.setAttribute('font-family', 'system-ui, sans-serif');
  ct.setAttribute('pointer-events', 'none');
  ct.textContent = 'OCTALYSIS';
  svg.appendChild(ct);

  const cs = document.createElementNS(SVG_NS, 'text');
  cs.setAttribute('x', CX); cs.setAttribute('y', CY + 13);
  cs.setAttribute('text-anchor', 'middle'); cs.setAttribute('dominant-baseline', 'middle');
  cs.setAttribute('font-size', '9'); cs.setAttribute('fill', '#93c5fd');
  cs.setAttribute('font-family', 'system-ui, sans-serif');
  cs.setAttribute('pointer-events', 'none');
  cs.textContent = 'Yu-kai Chou';
  svg.appendChild(cs);
})();
```

- [ ] **Step 4: Verify**

Open `site/index.html`. Hero shows: title, subtitle, centered blue spoke-wheel octagon with 8 white cards around it. Clicking a card scrolls to that drive section.

- [ ] **Step 5: Commit**

```bash
git add site/index.html
git commit -m "feat(learn): replace hero with spoke-wheel octagon"
```

---

## Task 4: Learn Page — Remove Hat/Brain Separate Sections

**Files:**
- Modify: `site/index.html`

The existing page has standalone "White Hat / Black Hat" and "Left Brain / Right Brain" sections. Remove them — the hat/brain axes will live as inline tags on each drive card header (Task 5).

- [ ] **Step 1: Find and delete hat/brain sections**

Search for and remove the entire HTML blocks for these sections. They will be identified by headings/wrapper elements with text like "White Hat", "Black Hat", "Left Brain", "Right Brain". Also remove their associated nav filter buttons if any remain.

- [ ] **Step 2: Update nav `#apply` anchor**

Make sure the `Apply` nav link still finds the `#apply` section correctly after removal.

- [ ] **Step 3: Commit**

```bash
git add site/index.html
git commit -m "feat(learn): remove standalone hat/brain sections"
```

---

## Task 5: Learn Page — Drive Cards (Richer Examples + Inline Tags)

**Files:**
- Modify: `site/index.html` (all 8 `.drive-card` blocks + their CSS)

Each drive card gets:
1. Inline hat + brain tags in the header (replacing separate sections)
2. 6 product examples in a 3-column grid (up from 4 in 2×2)
3. UI technique demo panels (dark, 3-column)
4. Full technique chip list per drive

### Card CSS additions

- [ ] **Step 1: Add/update card CSS**

After the existing `.examples-grid` styles, add:

```css
/* Inline hat/brain tags */
.drive-tags { display: flex; gap: 5px; margin-top: 7px; flex-wrap: wrap; }
.drive-tag {
  font-size: .62rem; font-weight: 700; padding: 2px 7px;
  border-radius: 8px;
}
.drive-tag.white-hat { background: #F0FDF4; color: #16A34A; border: 1px solid #86EFAC; }
.drive-tag.black-hat { background: #F9FAFB; color: #374151; border: 1px solid #D1D5DB; }
.drive-tag.left-brain { background: #EFF6FF; color: #1D4ED8; border: 1px solid #BFDBFE; }
.drive-tag.right-brain { background: #F5F3FF; color: #7C3AED; border: 1px solid #DDD6FE; }

/* 3-col examples grid */
.examples-grid-3col {
  display: grid; grid-template-columns: repeat(3, 1fr);
  gap: 10px; margin-bottom: 20px;
}
.example-card {
  background: #F9FAFB; border-radius: 10px;
  padding: 12px 14px; border: 1px solid #E5E7EB;
}
.example-product { font-weight: 700; font-size: .84rem; margin-bottom: 3px; }

/* UI demo panels (dark) */
.ui-demos-row {
  display: grid; grid-template-columns: repeat(3, 1fr);
  gap: 10px; margin-bottom: 20px;
}
.ui-demo-panel {
  background: #0d1117; border-radius: 10px;
  padding: 12px; border: 1px solid #21262d; min-height: 90px;
  display: flex; flex-direction: column; gap: 6px;
}
.ui-demo-label {
  font-size: .6rem; font-weight: 700; letter-spacing: .5px;
  text-transform: uppercase; color: #6b7280;
}

/* Technique chips */
.techniques-row { display: flex; flex-wrap: wrap; gap: 5px; margin-bottom: 16px; }
.technique-chip {
  font-size: .72rem; font-weight: 600; padding: 4px 12px;
  border-radius: 20px; border: 1.5px solid #E5E7EB;
  background: #F9FAFB; color: #374151; cursor: default;
}
```

### CD1: Epic Meaning & Calling

- [ ] **Step 2: Replace CD1 card body**

Find `<div class="drive-card" id="drive-1"` and update its interior to:

```html
<div class="drive-header">
  <div class="drive-header-left">
    <div class="drive-num" style="background:#F59E0B">CD1</div>
    <div class="drive-name" style="color:#92400E">Epic Meaning &amp; Calling</div>
    <div class="drive-tags">
      <span class="drive-tag white-hat">⬜ White Hat</span>
      <span class="drive-tag right-brain">🧠 Right Brain</span>
    </div>
  </div>
  <div class="drive-icon-big">🌐</div>
</div>
<div class="drive-body">
  <p class="drive-desc">The belief that you are doing something greater than yourself. This drive powers open-source contributors, Wikipedia editors, and religious communities alike — the sense of a chosen mission makes effort feel meaningful, not costly.</p>
  <div class="examples-label">Real-World Examples</div>
  <div class="examples-grid-3col">
    <div class="example-card">
      <div class="example-product">🌍 Wikipedia</div>
      <span class="example-technique" style="background:#F59E0B">Humanity Hero</span>
      <p class="example-body">Editors contribute without pay because they believe in free knowledge for all humanity.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🐧 Linux / Open Source</div>
      <span class="example-technique" style="background:#F59E0B">Co-Creator</span>
      <p class="example-body">Developers contribute to codebases they'll never fully own — the collective mission is the reward.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🦸 Superhero Narratives</div>
      <span class="example-technique" style="background:#F59E0B">Narrative</span>
      <p class="example-body">Games and apps that cast the user as "the chosen one" create immediate emotional investment.</p>
    </div>
    <div class="example-card">
      <div class="example-product">💉 Blood Donation</div>
      <span class="example-technique" style="background:#F59E0B">Elitism</span>
      <p class="example-body">"You are one of the few people with Type O— who can help." Scarcity of the gift elevates the giver.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🎮 World of Warcraft</div>
      <span class="example-technique" style="background:#F59E0B">World-Defining</span>
      <p class="example-body">Rich lore and faction identity make players feel part of a living history, not just playing a game.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🌱 Ecosia Browser</div>
      <span class="example-technique" style="background:#F59E0B">Inspiring Meaning</span>
      <p class="example-body">Each search plants trees. The counter makes your daily web browsing feel purposeful.</p>
    </div>
  </div>
  <div class="examples-label">Technique UI Examples</div>
  <div class="ui-demos-row">
    <div class="ui-demo-panel">
      <div class="ui-demo-label">Mission Counter</div>
      <div style="text-align:center;padding:6px 0">
        <div style="font-size:.65rem;color:#6b7280;margin-bottom:4px">Community Members</div>
        <div style="font-size:1.8rem;font-weight:900;color:#F59E0B;letter-spacing:-.03em">84,271</div>
        <div style="font-size:.62rem;color:#6b7280">designers, developers &amp; educators</div>
      </div>
    </div>
    <div class="ui-demo-panel">
      <div class="ui-demo-label">Chosen Role Badge</div>
      <div style="display:flex;align-items:center;gap:8px;padding:4px 0">
        <div style="width:36px;height:36px;border-radius:50%;background:#78350f;display:flex;align-items:center;justify-content:center;font-size:1.1rem;border:2px solid #F59E0B">🌐</div>
        <div>
          <div style="font-size:.72rem;font-weight:700;color:#e6edf3">Guardian of Knowledge</div>
          <div style="font-size:.62rem;color:#F59E0B">Chosen Role · Epic Tier</div>
        </div>
      </div>
    </div>
    <div class="ui-demo-panel">
      <div class="ui-demo-label">World Narrative Banner</div>
      <div style="background:linear-gradient(135deg,#78350f,#92400e);border-radius:6px;padding:8px;text-align:center">
        <div style="font-size:.65rem;color:#fde68a;font-weight:700;letter-spacing:.5px">YOUR MISSION</div>
        <div style="font-size:.78rem;color:#fff;line-height:1.4;margin-top:3px">Help 100,000 learners discover gamification</div>
      </div>
    </div>
  </div>
  <div class="examples-label">All Techniques</div>
  <div class="techniques-row">
    <span class="technique-chip">Narrative / Storytelling</span>
    <span class="technique-chip">Elitism</span>
    <span class="technique-chip">World-Defining</span>
    <span class="technique-chip">Beginners Luck</span>
    <span class="technique-chip">Free Lunch</span>
    <span class="technique-chip">Humanity Hero</span>
    <span class="technique-chip">Co-Creator</span>
    <span class="technique-chip">Inspiring Meaning</span>
  </div>
  <div class="designer-tip" style="border-color:#F59E0B">
    <div class="tip-label" style="color:#92400E">💡 Designer Tip</div>
    Connect your product to a mission bigger than the product itself. "You're helping build the world's largest free encyclopedia" is more motivating than any badge. Give users a title that reflects their role in that mission.
  </div>
</div>
```

### CD2: Development & Accomplishment

- [ ] **Step 3: Replace CD2 card body**

Find `<div class="drive-card" id="drive-2"` and update:

```html
<div class="drive-header">
  <div class="drive-header-left">
    <div class="drive-num" style="background:#3B82F6">CD2</div>
    <div class="drive-name" style="color:#1E40AF">Development &amp; Accomplishment</div>
    <div class="drive-tags">
      <span class="drive-tag white-hat">⬜ White Hat</span>
      <span class="drive-tag left-brain">🔢 Left Brain</span>
    </div>
  </div>
  <div class="drive-icon-big">🏆</div>
</div>
<div class="drive-body">
  <p class="drive-desc">The internal drive to make progress, develop skills, and overcome challenges. Points, badges, and leaderboards only work when the underlying challenge is genuinely rewarding — without real stakes, they feel hollow.</p>
  <div class="examples-label">Real-World Examples</div>
  <div class="examples-grid-3col">
    <div class="example-card">
      <div class="example-product">💼 LinkedIn</div>
      <span class="example-technique" style="background:#3B82F6">Progress Bar</span>
      <p class="example-body">Profile strength % with specific next steps keeps users returning until "All-Star" status.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🐙 GitHub</div>
      <span class="example-technique" style="background:#3B82F6">Contribution Graph</span>
      <p class="example-body">The annual heatmap becomes a public record of consistency — developers feel pride maintaining it.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🦜 Duolingo</div>
      <span class="example-technique" style="background:#3B82F6">XP + Leagues</span>
      <p class="example-body">Weekly league standings create social benchmarking — you can always see who you're competing with.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🏃 Strava</div>
      <span class="example-technique" style="background:#3B82F6">Milestone Badges</span>
      <p class="example-body">First 5K, personal best, 100-mile club — each milestone provides a sense of earned achievement.</p>
    </div>
    <div class="example-card">
      <div class="example-product">🎮 Xbox / Steam</div>
      <span class="example-technique" style="background:#3B82F6">Achievement System</span>
      <p class="example-body">Unlockable trophies extend game value beyond completion — players return for 100% completion runs.</p>
    </div>
    <div class="example-card">
      <div class="example-product">📚 Khan Academy</div>
      <span class="example-technique" style="background:#3B82F6">Mastery Bars</span>
      <p class="example-body">Colour-coded skill mastery (practiced → familiar → proficient → mastered) makes invisible learning visible.</p>
    </div>
  </div>
  <div class="examples-label">Technique UI Examples</div>
  <div class="ui-demos-row">
    <div class="ui-demo-panel">
      <div class="ui-demo-label">Progress Bar</div>
      <div style="display:flex;justify-content:space-between;font-size:.65rem;color:#6b7280;margin-bottom:3px"><span>Level 3</span><span style="color:#3B82F6">680 / 1000 XP</span></div>
      <div style="height:7px;background:#21262d;border-radius:4px"><div style="width:68%;height:100%;background:linear-gradient(90deg,#3B82F6,#8B5CF6);border-radius:4px;box-shadow:0 0 6px rgba(59,130,246,.5)"></div></div>
      <div style="font-size:.62rem;color:#6b7280;margin-top:4px">Next reward: 320 XP away</div>
    </div>
    <div class="ui-demo-panel">
      <div class="ui-demo-label">Badge Achievement</div>
      <div style="display:flex;align-items:center;gap:8px">
        <div style="width:40px;height:40px;border-radius:50%;background:linear-gradient(135deg,#1d4ed8,#7c3aed);display:flex;align-items:center;justify-content:center;font-size:1.2rem;box-shadow:0 0 12px rgba(59,130,246,.4)">🎨</div>
        <div>
          <div style="font-size:.72rem;font-weight:700;color:#e6edf3">Level Up!</div>
          <div style="font-size:.62rem;color:#6b7280">You reached Level 4 · Explorer</div>
          <div style="font-size:.6rem;color:#3B82F6;margin-top:2px">+1 new skill unlocked</div>
        </div>
      </div>
    </div>
    <div class="ui-demo-panel">
      <div class="ui-demo-label">Quest List</div>
      <div style="display:flex;flex-direction:column;gap:4px">
        <div style="display:flex;align-items:center;gap:5px;font-size:.7rem">
          <div style="width:14px;height:14px;border-radius:50%;background:#10B981;display:flex;align-items:center;justify-content:center;font-size:.55rem;color:#fff;flex-shrink:0">✓</div>
          <span style="color:#6b7280;text-decoration:line-through">Complete profile</span>
        </div>
        <div style="display:flex;align-items:center;gap:5px;font-size:.7rem">
          <div style="width:14px;height:14px;border-radius:50%;border:1.5px solid #3B82F6;flex-shrink:0"></div>
          <span style="color:#e6edf3;font-weight:600">Make first post</span>
          <span style="margin-left:auto;color:#3B82F6;font-size:.6rem">+60 XP</span>
        </div>
        <div style="display:flex;align-items:center;gap:5px;font-size:.7rem">
          <div style="width:14px;height:14px;border-radius:50%;border:1.5px solid #30363d;flex-shrink:0"></div>
          <span style="color:#6b7280">Get 10 followers</span>
        </div>
      </div>
    </div>
  </div>
  <div class="examples-label">All Techniques</div>
  <div class="techniques-row">
    <span class="technique-chip">Points</span>
    <span class="technique-chip">Badges</span>
    <span class="technique-chip">Leaderboard</span>
    <span class="technique-chip">Progress Bar</span>
    <span class="technique-chip">Quest Lists</span>
    <span class="technique-chip">Boss Fights</span>
    <span class="technique-chip">Certificates</span>
    <span class="technique-chip">High-Five</span>
    <span class="technique-chip">Achievement Symbols</span>
  </div>
  <div class="designer-tip" style="border-color:#3B82F6">
    <div class="tip-label" style="color:#1E40AF">💡 Designer Tip</div>
    Celebrate micro-wins. Show "50 XP to Level 4" rather than just the current state — the approaching reward motivates more than the current position. Never add points to something that doesn't already have intrinsic value.
  </div>
</div>
```

### CD3–CD8: Remaining Drive Cards

- [ ] **Step 4: Update CD3 card** (Empowerment of Creativity & Feedback)

Hat: Black Hat, Right Brain. Examples: Minecraft, Figma, Roblox, TikTok Duet, Canva, StackOverflow. Techniques: Milestone Unlocks, Choice Perception, Instant Feedback, Boosters, Plant & Predict, Attribute Combo, Build From Scratch. Follow the same 3-col examples + 3 UI panels + all-techniques chips structure as CD2.

UI panels for CD3:
- "Combo Builder" dark panel: show tag cloud with 3 selected tags and a combo result badge
- "Real-time Preview" panel: show live canvas thumbnail updating as user drags a slider
- "Feedback Loop" panel: show a submission with a green "✓ Valid" badge and score

- [ ] **Step 5: Update CD4 card** (Ownership & Possession)

Hat: White Hat, Left Brain. Examples: NFTs/crypto wallets, Animal Crossing, Pokémon, Tamagotchi, LinkedIn profile, Notion personal workspace. Techniques: Virtual Goods, Virtual Currency, Build to Give, Collection Set, Protection & Preservation, Territory Takeover, Alfred Effect. 

UI panels for CD4:
- "Passport Stamps" panel: 4-stamp grid (2 claimed colored, 2 dashed grey)
- "Virtual Currency" panel: wallet balance "⭐ 4,820 Coins" + recent earn row
- "Collection Set" panel: 4-item grid with 3 collected and 1 locked

- [ ] **Step 6: Update CD5 card** (Social Influence & Relatedness)

Hat: White Hat, Right Brain. Examples: Facebook Reactions, LinkedIn Endorsements, Yelp Elite, Twitch Bits, Discord Roles, Referral Programs. Techniques: Social Treasure/Gifting, Friending, Conformity Anchor, Group Quest, Social Prod, Mentorship, Totem/Trophy Shelf.

UI panels for CD5:
- "Activity Feed" panel: scrolling list of "Alex just earned Level 4 · 2m ago" style entries
- "Social Proof" panel: avatar stack + "14 of your teammates already use this"
- "Group Quest" panel: team progress bar, 3 avatar initials, "3/5 tasks done"

- [ ] **Step 7: Update CD6 card** (Scarcity & Impatience)

Hat: Black Hat, Left Brain. Examples: Snapchat Stories, Farmville, Gmail Beta invite, Flash Sales, Booking.com "only 2 rooms left", Supreme drops. Techniques: Dangling Carrot, Appointment Dynamics, Fixed Intervals, Lottery, Morning Star Bonus, Last Mile Drive, Magnetic Cap.

UI panels for CD6:
- "Countdown Timer" panel: `23:47:12` countdown, red text "Offer ends soon"
- "Ticket Availability" panel: 5 seat holes (3 filled grey, 2 open), "2 seats remain"
- "Appointment Dynamic" panel: crop icon, "🌽 Ready to harvest in 4h 12m", progress bar

- [ ] **Step 8: Update CD7 card** (Unpredictability & Curiosity)

Hat: Black Hat, Right Brain. Examples: Loot boxes, Twitter feed, Spotify Discover Weekly, Mystery boxes, Wordle, Blind box toys. Techniques: Glowing Choice, Mini Quests, Visual Storytelling, Easter Eggs, Random Rewards, Rolling Rewards, Sudden Rewards, Evolved UI.

UI panels for CD7:
- "Scratch Card" panel: grey textured overlay with coin icon "Scratch to reveal technique"
- "Random Reward" panel: spinning wheel with ? and a "Spin" button
- "Easter Egg" panel: faint hidden icon in corner, "🥚 You found a hidden feature!"

- [ ] **Step 9: Update CD8 card** (Loss & Avoidance)

Hat: Black Hat, Left Brain. Examples: Duolingo streak, Snapchat streak, LinkedIn "profile views dropping", Farmville dying crops, Premium expiry warnings, Casino near-misses. Techniques: Progress Loss, Status Quo Sloth, Sunk Cost Prison, Rightful Heritage, Countdown Timer, FOMO Punch, Streaks.

UI panels for CD8:
- "Streak Tracker" panel: 🔥 icon, 7-day week row (5 filled green, today dashed red), "⚠️ At risk"
- "Progress Loss Warning" panel: "Your level will drop in 3 days" with red bar declining
- "FOMO Counter" panel: "🔔 47 people viewed your profile this week (↓ 18%)"

- [ ] **Step 10: Verify all 8 cards**

Open `site/index.html`, scroll through all 8 drive cards. Each should show:
- Colored left border
- CD badge + drive name + hat/brain inline tags
- 6 product examples in 3 columns
- 3 dark UI demo panels
- Technique chips row
- Designer tip

- [ ] **Step 11: Commit**

```bash
git add site/index.html
git commit -m "feat(learn): richer drive cards — 6 examples, UI demos, full technique chips"
```

---

## Task 6: Learn Page — Sidebar Octagon (Scroll-Linked)

**Files:**
- Modify: `site/index.html` (explorer layout + IntersectionObserver JS)

The existing two-column explorer layout has a sticky sidebar with the segmented octagon. Keep the layout but use a smaller version of the spoke-wheel ring (no outer cards — just the ring + numbers) as the sidebar octagon. Clicking a segment scrolls to that drive.

- [ ] **Step 1: Update sidebar octagon HTML**

Find the sticky sidebar block (`.sidebar` or similar). Replace the SVG element and its container with:

```html
<div class="sidebar-oct-wrap">
  <svg id="sidebar-oct-svg" viewBox="0 0 240 240" xmlns="http://www.w3.org/2000/svg"
       style="width:200px;height:200px;cursor:pointer"></svg>
  <div style="text-align:center;margin-top:8px;font-size:.7rem;color:#6B7280;font-weight:600">Click to jump</div>
</div>
```

- [ ] **Step 2: Build sidebar octagon via JS**

Add this function after `buildSpokeRing()`:

```js
(function buildSidebarOct() {
  const SVG_NS = 'http://www.w3.org/2000/svg';
  const svg = document.getElementById('sidebar-oct-svg');
  if (!svg) return;
  const CX = 120, CY = 120, R_OUTER = 100, R_INNER = 60;
  const DRIVES = [
    {cd:1,color:'#F59E0B'},{cd:2,color:'#3B82F6'},{cd:3,color:'#8B5CF6'},
    {cd:4,color:'#10B981'},{cd:5,color:'#EC4899'},{cd:6,color:'#F97316'},
    {cd:7,color:'#06B6D4'},{cd:8,color:'#EF4444'}
  ];

  function vtx(i, r) {
    const a = (-90 + i * 45) * Math.PI / 180;
    return { x: CX + r * Math.cos(a), y: CY + r * Math.sin(a) };
  }

  DRIVES.forEach((d, i) => {
    const va = vtx(i, R_OUTER), vb = vtx(i + 1, R_OUTER);
    const ia = vtx(i, R_INNER), ib = vtx(i + 1, R_INNER);
    const poly = document.createElementNS(SVG_NS, 'polygon');
    poly.setAttribute('points', `${CX},${CY} ${va.x},${va.y} ${vb.x},${vb.y}`);
    poly.setAttribute('fill', d.color);
    poly.setAttribute('opacity', '0.25');
    poly.setAttribute('stroke', '#fff');
    poly.setAttribute('stroke-width', '1.5');
    poly.style.cursor = 'pointer';
    poly.style.transition = 'opacity .2s';
    poly.dataset.cd = d.cd;

    poly.addEventListener('mouseenter', () => { if (!poly.classList.contains('oct-active')) poly.setAttribute('opacity','0.7'); });
    poly.addEventListener('mouseleave', () => { if (!poly.classList.contains('oct-active')) poly.setAttribute('opacity','0.25'); });
    poly.addEventListener('click', () => {
      const card = document.getElementById(`drive-${d.cd}`);
      if (card) card.scrollIntoView({ behavior: 'smooth', block: 'start' });
    });
    svg.appendChild(poly);
  });

  // Center circle + label
  const c = document.createElementNS(SVG_NS, 'circle');
  c.setAttribute('cx', CX); c.setAttribute('cy', CY);
  c.setAttribute('r', R_INNER - 4); c.setAttribute('fill', '#fff');
  svg.appendChild(c);
  const t = document.createElementNS(SVG_NS, 'text');
  t.setAttribute('x', CX); t.setAttribute('y', CY + 1);
  t.setAttribute('text-anchor', 'middle'); t.setAttribute('dominant-baseline', 'middle');
  t.setAttribute('font-size', '9'); t.setAttribute('font-weight', '900');
  t.setAttribute('fill', '#374151'); t.setAttribute('font-family', 'system-ui, sans-serif');
  t.setAttribute('pointer-events', 'none');
  t.textContent = 'OCT';
  svg.appendChild(t);

  // Expose highlight function for IntersectionObserver
  window._highlightOctSeg = function(activeCd) {
    svg.querySelectorAll('polygon').forEach(p => {
      const isActive = +p.dataset.cd === activeCd;
      p.setAttribute('opacity', isActive ? '1' : '0.25');
      p.classList.toggle('oct-active', isActive);
    });
  };
})();
```

- [ ] **Step 3: Wire IntersectionObserver**

Find the existing IntersectionObserver code and update the callback to call `window._highlightOctSeg(cd)` instead of the old logic:

```js
const observer = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const id = entry.target.id; // e.g. "drive-3"
      const cd = parseInt(id.replace('drive-', ''), 10);
      if (window._highlightOctSeg) window._highlightOctSeg(cd);
    }
  });
}, { threshold: 0.3 });

document.querySelectorAll('.drive-card[id^="drive-"]').forEach(el => observer.observe(el));
```

- [ ] **Step 4: Verify**

Open `site/index.html`. Scroll through drive cards — the sidebar octagon segment should highlight the active drive in its color. Clicking a segment jumps to that card.

- [ ] **Step 5: Commit**

```bash
git add site/index.html
git commit -m "feat(learn): scroll-linked sidebar octagon"
```

---

## Task 7: Playground Page — Dark Shell, Nav & Hero

**Files:**
- Modify: `site/playground.html`

- [ ] **Step 1: Replace skeleton with full dark page structure**

Replace the entire contents of `site/playground.html` with:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Octalysis Playground — Experience the Drives</title>
  <style>
    :root {
      --bg:#0d1117; --surface:#161b22; --border:#30363d;
      --text:#e6edf3; --muted:#8b949e; --dim:#6b7280;
      --cd1:#F59E0B; --cd2:#3B82F6; --cd3:#8B5CF6; --cd4:#10B981;
      --cd5:#EC4899; --cd6:#F97316; --cd7:#06B6D4; --cd8:#EF4444;
    }
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }
    body { background: var(--bg); color: var(--text); font-family: system-ui, -apple-system, sans-serif; min-height: 100vh; }

    /* NAV */
    .pg-nav {
      display: flex; align-items: center; justify-content: space-between;
      padding: 10px 32px; background: var(--surface); position: sticky;
      top: 0; z-index: 300; border-bottom: 1px solid var(--border);
    }
    .pg-nav-logo {
      font-size: .7rem; font-weight: 800; letter-spacing: 1px;
      text-transform: uppercase; color: #a78bfa; background: #1a0d3f;
      border: 1px solid #6d28d9; border-radius: 20px; padding: 5px 14px;
      text-decoration: none;
    }
    .pg-nav-right { display: flex; align-items: center; gap: 12px; }
    .pg-nav-learn {
      font-size: .75rem; font-weight: 700; padding: 6px 14px;
      border-radius: 20px; background: #fff; color: #111;
      border: 1px solid #E5E7EB; text-decoration: none;
    }
    .pg-nav-learn:hover { background: #f9fafb; }
    .pg-nav-active { font-size: .8rem; font-weight: 700; color: var(--muted); }

    /* HERO */
    .pg-hero {
      text-align: center; padding: 56px 24px 48px;
      border-bottom: 1px solid var(--border);
    }
    .pg-hero h1 {
      font-size: 2.2rem; font-weight: 900; letter-spacing: -.04em;
      color: var(--text); margin-bottom: 10px;
    }
    .pg-hero p { font-size: .98rem; color: var(--muted); max-width: 480px; margin: 0 auto; }

    /* 3-PANEL HUB */
    .hub {
      max-width: 1280px; margin: 0 auto; padding: 48px 24px 64px;
      display: grid; grid-template-columns: 1fr 320px 1fr; gap: 32px;
      align-items: start;
    }
    .hub-panel {
      background: var(--surface); border: 1px solid var(--border);
      border-radius: 16px; padding: 24px; min-height: 400px;
    }
    .hub-panel-label {
      font-size: .65rem; font-weight: 700; letter-spacing: .8px;
      text-transform: uppercase; color: var(--dim); margin-bottom: 16px;
    }

    /* Octagon hub */
    .hub-oct-wrap { display: flex; flex-direction: column; align-items: center; gap: 12px; }
    #pg-oct-svg { width: 280px; height: 280px; cursor: pointer; }
    .hub-oct-hint { font-size: .72rem; color: var(--dim); text-align: center; }

    /* Desc panel (left) */
    .desc-cd-num { font-size: 2rem; font-weight: 900; margin-bottom: 4px; }
    .desc-drive-name { font-size: 1.1rem; font-weight: 800; color: var(--text); margin-bottom: 12px; }
    .desc-body { font-size: .88rem; color: var(--muted); line-height: 1.7; margin-bottom: 16px; }
    .desc-tags { display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 16px; }
    .desc-tag {
      font-size: .62rem; font-weight: 700; padding: 3px 8px;
      border-radius: 8px; border: 1px solid var(--border); color: var(--muted);
    }
    .desc-read-link { font-size: .8rem; font-weight: 600; color: #58a6ff; text-decoration: none; }
    .desc-read-link:hover { text-decoration: underline; }
    .desc-empty { color: var(--dim); font-size: .9rem; margin-top: 60px; text-align: center; }

    /* Demo panel (right) */
    .demo-title { font-size: .95rem; font-weight: 800; color: var(--text); margin-bottom: 16px; }
    .demo-empty { color: var(--dim); font-size: .9rem; margin-top: 60px; text-align: center; }

    /* Fade-in animation for panels */
    .panel-content { opacity: 0; transform: translateY(6px); transition: opacity .3s, transform .3s; }
    .panel-content.visible { opacity: 1; transform: translateY(0); }

    /* FOOTER */
    .pg-footer {
      text-align: center; padding: 24px; border-top: 1px solid var(--border);
      font-size: .82rem; color: var(--dim);
    }
    .pg-footer a { color: #58a6ff; text-decoration: none; }
    .pg-footer a:hover { text-decoration: underline; }

    /* Responsive */
    @media (max-width: 900px) {
      .hub { grid-template-columns: 1fr; }
      #pg-oct-svg { width: 220px; height: 220px; }
    }
  </style>
</head>
<body>

<nav class="pg-nav">
  <a href="index.html" class="pg-nav-logo">Octalysis</a>
  <div class="pg-nav-right">
    <a href="index.html" class="pg-nav-learn">📖 Learn</a>
    <span class="pg-nav-active">🎮 Playground</span>
  </div>
</nav>

<section class="pg-hero">
  <h1>Experience the Drives</h1>
  <p>Click a segment on the octagon to activate a drive demo.</p>
</section>

<div class="hub">
  <!-- Left: Description -->
  <div class="hub-panel" id="desc-panel">
    <div class="hub-panel-label">Framework Item</div>
    <div id="desc-content" class="panel-content">
      <div class="desc-empty">← Select a drive to explore</div>
    </div>
  </div>

  <!-- Center: Octagon -->
  <div class="hub-oct-wrap">
    <svg id="pg-oct-svg" viewBox="0 0 280 280" xmlns="http://www.w3.org/2000/svg"></svg>
    <div class="hub-oct-hint" id="oct-hint">Click a drive segment</div>
  </div>

  <!-- Right: Demo -->
  <div class="hub-panel" id="demo-panel">
    <div class="hub-panel-label">Live Demo</div>
    <div id="demo-content" class="panel-content">
      <div class="demo-empty">← Select a drive to see a demo</div>
    </div>
  </div>
</div>

<footer class="pg-footer">
  Octalysis Framework by <a href="https://yukaichou.com" target="_blank" rel="noopener">Yu-kai Chou</a> &nbsp;·&nbsp;
  <a href="index.html">📖 Read the full framework →</a>
</footer>

<script>
// ── Playground octagon + 8 demos ────────────────────────────────
// (demo JS added in Tasks 8–9)
</script>
</body>
</html>
```

- [ ] **Step 2: Verify**

Open `site/playground.html`. Expect: dark page, dark nav with "📖 Learn" pill and "🎮 Playground" label, hero text, empty 3-panel layout, footer.

- [ ] **Step 3: Commit**

```bash
git add site/playground.html
git commit -m "feat(playground): dark shell, nav, hero, 3-panel layout"
```

---

## Task 8: Playground Page — Interactive Octagon Hub

**Files:**
- Modify: `site/playground.html` (the `<script>` block)

- [ ] **Step 1: Add octagon + drive data JS**

Replace the empty `<script>` block with:

```js
const SVG_NS = 'http://www.w3.org/2000/svg';
const DRIVES = [
  { cd:1, name:'Epic Meaning & Calling',      icon:'🌐', color:'#F59E0B',
    hat:'⬜ White Hat', brain:'🧠 Right Brain',
    desc:'The belief that you are doing something greater than yourself. When users feel chosen for a mission, effort becomes purposeful.',
    demoId:'demo-cd1' },
  { cd:2, name:'Development & Accomplishment', icon:'🏆', color:'#3B82F6',
    hat:'⬜ White Hat', brain:'🔢 Left Brain',
    desc:'The internal drive to progress, develop skills, and overcome challenges. Progress bars and XP make invisible growth visible.',
    demoId:'demo-cd2' },
  { cd:3, name:'Empowerment of Creativity',    icon:'🧩', color:'#8B5CF6',
    hat:'⬛ Black Hat', brain:'🧠 Right Brain',
    desc:'The drive to try new combinations and get meaningful feedback. When every choice leads to a unique outcome, creativity loops become addictive.',
    demoId:'demo-cd3' },
  { cd:4, name:'Ownership & Possession',       icon:'📦', color:'#10B981',
    hat:'⬜ White Hat', brain:'🔢 Left Brain',
    desc:'The drive to accumulate and protect things you own. Collection, virtual currency, and customisation trigger this drive deeply.',
    demoId:'demo-cd4' },
  { cd:5, name:'Social Influence & Relatedness', icon:'👥', color:'#EC4899',
    hat:'⬜ White Hat', brain:'🧠 Right Brain',
    desc:'The drive to collaborate, compete, and belong. Seeing what peers do creates social benchmarking and conformity pressure.',
    demoId:'demo-cd5' },
  { cd:6, name:'Scarcity & Impatience',        icon:'⏳', color:'#F97316',
    hat:'⬛ Black Hat', brain:'🔢 Left Brain',
    desc:'Wanting something more because you cannot have it yet. Countdown timers and limited availability create urgent desire.',
    demoId:'demo-cd6' },
  { cd:7, name:'Unpredictability & Curiosity', icon:'🎲', color:'#06B6D4',
    hat:'⬛ Black Hat', brain:'🧠 Right Brain',
    desc:'Not knowing what comes next keeps the brain engaged. Variable rewards and hidden surprises create compulsive curiosity.',
    demoId:'demo-cd7' },
  { cd:8, name:'Loss & Avoidance',             icon:'🚩', color:'#EF4444',
    hat:'⬛ Black Hat', brain:'🔢 Left Brain',
    desc:'The drive to avoid losing what you already have. Streaks at risk, expiring progress, and FOMO are powerful motivators.',
    demoId:'demo-cd8' }
];

let activeCd = null;

// ── Build playground octagon ────────────────────────────────────
const pgSvg = document.getElementById('pg-oct-svg');
const CX = 140, CY = 140, R = 110;

function vtx(i, r) {
  const a = (-90 + i * 45) * Math.PI / 180;
  return { x: CX + r * Math.cos(a), y: CY + r * Math.sin(a) };
}

const segments = [];

DRIVES.forEach((d, i) => {
  const va = vtx(i, R), vb = vtx(i + 1, R);
  const poly = document.createElementNS(SVG_NS, 'polygon');
  poly.setAttribute('points', `${CX},${CY} ${va.x},${va.y} ${vb.x},${vb.y}`);
  poly.setAttribute('fill', d.color);
  poly.setAttribute('opacity', '0.2');
  poly.setAttribute('stroke', '#0d1117');
  poly.setAttribute('stroke-width', '2');
  poly.style.cursor = 'pointer';
  poly.style.transition = 'opacity .2s';
  pgSvg.appendChild(poly);
  segments.push(poly);

  // Hover
  poly.addEventListener('mouseenter', () => {
    if (activeCd !== d.cd) poly.setAttribute('opacity', '0.7');
    document.getElementById('oct-hint').textContent = d.name;
  });
  poly.addEventListener('mouseleave', () => {
    if (activeCd !== d.cd) poly.setAttribute('opacity', '0.2');
    document.getElementById('oct-hint').textContent = activeCd ? DRIVES[activeCd-1].name : 'Click a drive segment';
  });
  poly.addEventListener('click', () => activateDrive(d.cd));
});

// Center circle
const cc = document.createElementNS(SVG_NS, 'circle');
cc.setAttribute('cx', CX); cc.setAttribute('cy', CY);
cc.setAttribute('r', '36'); cc.setAttribute('fill', '#161b22');
cc.setAttribute('stroke', '#30363d'); cc.setAttribute('stroke-width', '1.5');
pgSvg.appendChild(cc);
const ct = document.createElementNS(SVG_NS, 'text');
ct.setAttribute('x', CX); ct.setAttribute('y', CY - 4);
ct.setAttribute('text-anchor', 'middle'); ct.setAttribute('dominant-baseline', 'middle');
ct.setAttribute('font-size', '8'); ct.setAttribute('font-weight', '900');
ct.setAttribute('fill', '#8b949e'); ct.setAttribute('font-family', 'system-ui, sans-serif');
ct.setAttribute('pointer-events', 'none'); ct.textContent = 'CLICK';
pgSvg.appendChild(ct);

// ── Activate a drive ────────────────────────────────────────────
function activateDrive(cd) {
  activeCd = cd;
  const d = DRIVES[cd - 1];

  // Update segment opacities
  segments.forEach((p, i) => {
    const isActive = i === cd - 1;
    p.setAttribute('opacity', isActive ? '1' : '0.15');
    if (isActive) {
      p.style.filter = `drop-shadow(0 0 8px ${d.color})`;
    } else {
      p.style.filter = '';
    }
  });
  ct.textContent = `CD${cd}`;

  // Render description panel
  const descEl = document.getElementById('desc-content');
  descEl.classList.remove('visible');
  descEl.innerHTML = `
    <div class="desc-cd-num" style="color:${d.color}">CD${cd}</div>
    <div class="desc-drive-name">${d.icon} ${d.name}</div>
    <p class="desc-body">${d.desc}</p>
    <div class="desc-tags">
      <span class="desc-tag">${d.hat}</span>
      <span class="desc-tag">${d.brain}</span>
    </div>
    <a href="index.html#drive-${cd}" class="desc-read-link">→ Read full description</a>
  `;
  requestAnimationFrame(() => descEl.classList.add('visible'));

  // Render demo panel
  const demoEl = document.getElementById('demo-content');
  demoEl.classList.remove('visible');
  demoEl.innerHTML = renderDemo(cd);
  requestAnimationFrame(() => {
    demoEl.classList.add('visible');
    initDemo(cd);
  });
}

// ── Demo renderer (returns HTML string) ─────────────────────────
function renderDemo(cd) {
  const demos = {
    1: renderMissionCounter,
    2: renderXPLevelUp,
    3: renderComboBuilder,
    4: renderPassportStamps,
    5: renderLivePulse,
    6: renderEventTicket,
    7: renderScratchCard,
    8: renderStreakTracker,
  };
  return demos[cd] ? demos[cd]() : '<div class="demo-empty">Demo coming soon</div>';
}
```

- [ ] **Step 2: Verify octagon renders**

Open `site/playground.html`. Center octagon should show all 8 segments at 20% opacity. Hovering a segment brightens it. Clicking should (currently) show empty panels with the description fade-in working.

- [ ] **Step 3: Commit**

```bash
git add site/playground.html
git commit -m "feat(playground): interactive octagon hub with drive activation"
```

---

## Task 9: Playground Demos — CD1 Mission Counter & CD2 XP Level-Up

**Files:**
- Modify: `site/playground.html` (append demo functions to `<script>`)

- [ ] **Step 1: Add CD1 and CD2 demo render functions**

Append to the `<script>` block (after the `renderDemo` function):

```js
// ── CD1: Mission Counter ─────────────────────────────────────
function renderMissionCounter() {
  return `
  <div class="demo-title">Mission Counter</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;text-align:center">
    <div style="font-size:2.8rem;margin-bottom:8px">🌐</div>
    <div style="font-size:.7rem;color:#6b7280;letter-spacing:.8px;text-transform:uppercase;margin-bottom:6px">Community Members</div>
    <div id="mission-count" style="font-size:2.6rem;font-weight:900;color:#F59E0B;letter-spacing:-.03em;line-height:1">84,271</div>
    <div style="font-size:.78rem;color:#8b949e;margin-top:6px">designers, developers &amp; educators</div>
    <div style="display:flex;justify-content:center;gap:8px;margin-top:16px;flex-wrap:wrap">
      <span style="font-size:.68rem;padding:3px 10px;border-radius:12px;background:#78350f;color:#fde68a;border:1px solid #92400e">🎨 Designers: 31,402</span>
      <span style="font-size:.68rem;padding:3px 10px;border-radius:12px;background:#1e3a5f;color:#93c5fd;border:1px solid #1d4ed8">💻 Developers: 28,915</span>
      <span style="font-size:.68rem;padding:3px 10px;border-radius:12px;background:#064e3b;color:#6ee7b7;border:1px solid #059669">📚 Educators: 23,954</span>
    </div>
    <button id="mission-btn" onclick="addVoice()" style="margin-top:16px;background:#F59E0B;color:#fff;border:none;border-radius:8px;padding:8px 20px;font-size:.82rem;font-weight:700;cursor:pointer;transition:transform .15s">
      + Add Your Voice
    </button>
  </div>`;
}

function initMissionCounter() {
  // nothing needed — button has inline onclick
}

window.addVoice = function() {
  const el = document.getElementById('mission-count');
  if (!el) return;
  const current = parseInt(el.textContent.replace(/,/g, ''), 10) + 1;
  el.textContent = current.toLocaleString();
  el.style.transform = 'scale(1.15)';
  el.style.color = '#fff';
  setTimeout(() => { el.style.transform = ''; el.style.color = '#F59E0B'; }, 300);
};

// ── CD2: XP Level-Up ─────────────────────────────────────────
function renderXPLevelUp() {
  return `
  <div class="demo-title">XP Level-Up</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d" id="xp-container">
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:12px">
      <div>
        <div style="font-size:.65rem;color:#6b7280;text-transform:uppercase;letter-spacing:.7px">Current Level</div>
        <div id="xp-level-name" style="font-size:1rem;font-weight:800;color:#e6edf3">Level 3 · Practitioner</div>
      </div>
      <div id="xp-badge" style="width:48px;height:48px;border-radius:50%;background:linear-gradient(135deg,#1d4ed8,#7c3aed);display:flex;align-items:center;justify-content:center;font-size:1.4rem;box-shadow:0 0 12px rgba(59,130,246,.3)">🏆</div>
    </div>
    <div style="display:flex;justify-content:space-between;font-size:.65rem;color:#6b7280;margin-bottom:4px">
      <span>XP Progress</span><span id="xp-label">680 / 1000</span>
    </div>
    <div style="height:10px;background:#21262d;border-radius:5px;overflow:hidden;margin-bottom:16px">
      <div id="xp-bar" style="width:68%;height:100%;background:linear-gradient(90deg,#3B82F6,#8B5CF6);border-radius:5px;transition:width .4s ease;box-shadow:0 0 8px rgba(59,130,246,.5)"></div>
    </div>
    <div style="font-size:.75rem;font-weight:700;color:#8b949e;margin-bottom:8px">Complete tasks to earn XP:</div>
    <div id="xp-tasks" style="display:flex;flex-direction:column;gap:6px">
      <div class="xp-task" data-xp="120" onclick="earnXP(this)" style="display:flex;align-items:center;justify-content:space-between;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:8px 12px;cursor:pointer;transition:border-color .15s">
        <span style="font-size:.78rem;color:#e6edf3">📝 Write a design brief</span>
        <span style="font-size:.68rem;font-weight:700;color:#3B82F6">+120 XP</span>
      </div>
      <div class="xp-task" data-xp="80" onclick="earnXP(this)" style="display:flex;align-items:center;justify-content:space-between;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:8px 12px;cursor:pointer;transition:border-color .15s">
        <span style="font-size:.78rem;color:#e6edf3">💬 Give feedback to a peer</span>
        <span style="font-size:.68rem;font-weight:700;color:#3B82F6">+80 XP</span>
      </div>
      <div class="xp-task" data-xp="200" onclick="earnXP(this)" style="display:flex;align-items:center;justify-content:space-between;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:8px 12px;cursor:pointer;transition:border-color .15s">
        <span style="font-size:.78rem;color:#e6edf3">🚀 Ship a prototype</span>
        <span style="font-size:.68rem;font-weight:700;color:#3B82F6">+200 XP</span>
      </div>
    </div>
  </div>`;
}

let xpCurrent = 680;
const xpMax = 1000;
const levelNames = ['Novice','Apprentice','Practitioner','Expert','Master'];

window.earnXP = function(el) {
  if (el.dataset.done) return;
  el.dataset.done = '1';
  el.style.opacity = '.5';
  el.style.pointerEvents = 'none';
  el.querySelector('span:last-child').textContent = '✓ Done';

  const gain = parseInt(el.dataset.xp, 10);
  xpCurrent = Math.min(xpCurrent + gain, xpMax);

  const barEl = document.getElementById('xp-bar');
  const labelEl = document.getElementById('xp-label');
  if (barEl) barEl.style.width = (xpCurrent / xpMax * 100) + '%';
  if (labelEl) labelEl.textContent = `${xpCurrent} / ${xpMax}`;

  if (xpCurrent >= xpMax) {
    const container = document.getElementById('xp-container');
    if (container) {
      container.style.background = 'linear-gradient(135deg,#1a0d3f,#0d2137)';
      container.style.borderColor = '#8B5CF6';
    }
    const lvlEl = document.getElementById('xp-level-name');
    if (lvlEl) lvlEl.textContent = 'Level 4 · Expert';
    const badge = document.getElementById('xp-badge');
    if (badge) {
      badge.style.background = 'linear-gradient(135deg,#7c3aed,#ec4899)';
      badge.style.boxShadow = '0 0 20px rgba(139,92,246,.6)';
      badge.textContent = '⭐';
    }
  }
};

function initXPLevelUp() { xpCurrent = 680; }
```

- [ ] **Step 2: Update `initDemo` to call per-demo inits**

Add this function right before or after `renderDemo`:

```js
function initDemo(cd) {
  const inits = {
    1: () => {}, // mission counter uses onclick
    2: initXPLevelUp,
    3: initComboBuilder,
    4: initPassportStamps,
    5: initLivePulse,
    6: initEventTicket,
    7: initScratchCard,
    8: initStreakTracker,
  };
  if (inits[cd]) inits[cd]();
}
```

(The other `init*` functions will be added in Tasks 10–12. Until then, stub them out:)

```js
function initComboBuilder() {}
function initPassportStamps() {}
function initLivePulse() {}
function initEventTicket() {}
function initScratchCard() {}
function initStreakTracker() {}
```

- [ ] **Step 3: Verify CD1 and CD2**

Open `site/playground.html`. Click CD1 (amber segment) — description panel shows CD1 info, demo shows mission counter with "+ Add Your Voice" button that increments the number. Click CD2 (blue segment) — XP demo shows level 3 bar, clicking task rows earns XP and advances the bar; when it hits 1000 the background shifts.

- [ ] **Step 4: Commit**

```bash
git add site/playground.html
git commit -m "feat(playground): CD1 mission counter + CD2 XP level-up demos"
```

---

## Task 10: Playground Demos — CD3 Combo Builder & CD4 Passport Stamps

**Files:**
- Modify: `site/playground.html`

- [ ] **Step 1: Add CD3 Combo Builder**

Append to `<script>`:

```js
// ── CD3: Combo Builder ────────────────────────────────────────
const COMBO_TAGS = ['Progress Bar','Streak','Badge','Leaderboard','Scratch Card','Countdown Timer','Boss Fight','Random Reward','Social Proof','Quest List','Stamp Collection','Narrative'];
const COMBOS = {
  'Progress Bar+Streak': {name:'The Habit Loop', effect:'Daily momentum builds automatic behaviour'},
  'Badge+Leaderboard': {name:'The Status Race', effect:'Visible rank turns achievement into competition'},
  'Scratch Card+Random Reward': {name:'Variable Surprise', effect:'Unpredictable payoff creates compulsive checking'},
  'Countdown Timer+Leaderboard': {name:'Rush Hour', effect:'Urgency + rank creates peak engagement spikes'},
  'Boss Fight+Progress Bar': {name:'Final Push', effect:'Visible endpoint makes the hardest challenge feel winnable'},
  'Narrative+Badge': {name:'Story Reward', effect:'Meaning-laden achievement deepens emotional investment'},
};
const COMBO_DEFAULT = {name:'Custom Combo', effect:'Pick 2–3 techniques to see their combined effect'};

function renderComboBuilder() {
  return `
  <div class="demo-title">Combo Builder</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="font-size:.7rem;color:#6b7280;margin-bottom:10px">Pick 2–3 techniques:</div>
    <div id="combo-tags" style="display:flex;flex-wrap:wrap;gap:6px;margin-bottom:14px">
      ${COMBO_TAGS.map(t => `<button class="combo-tag-btn" data-tag="${t}" onclick="toggleComboTag(this)"
        style="font-size:.72rem;padding:5px 12px;border-radius:20px;border:1.5px solid #30363d;background:#161b22;color:#8b949e;cursor:pointer;transition:all .15s">${t}</button>`).join('')}
    </div>
    <div id="combo-result" style="background:#161b22;border-radius:10px;padding:14px;border:1px solid #30363d;min-height:70px">
      <div style="font-size:.68rem;color:#6b7280;text-transform:uppercase;letter-spacing:.6px;margin-bottom:4px">Combo Effect</div>
      <div id="combo-name" style="font-size:.95rem;font-weight:800;color:#8B5CF6;margin-bottom:4px">—</div>
      <div id="combo-effect" style="font-size:.8rem;color:#8b949e;line-height:1.5">Select techniques above</div>
    </div>
  </div>`;
}

const selectedTags = new Set();
window.toggleComboTag = function(btn) {
  const tag = btn.dataset.tag;
  if (selectedTags.has(tag)) {
    selectedTags.delete(tag);
    btn.style.background = '#161b22'; btn.style.color = '#8b949e';
    btn.style.borderColor = '#30363d';
  } else {
    if (selectedTags.size >= 3) return;
    selectedTags.add(tag);
    btn.style.background = '#1a0d3f'; btn.style.color = '#a78bfa';
    btn.style.borderColor = '#8B5CF6';
  }
  updateComboResult();
};

function updateComboResult() {
  const tags = [...selectedTags].sort();
  const key = tags.slice(0,2).join('+');
  const combo = COMBOS[key] || (selectedTags.size >= 2 ? COMBO_DEFAULT : null);
  const nameEl = document.getElementById('combo-name');
  const effectEl = document.getElementById('combo-effect');
  if (!nameEl || !effectEl) return;
  if (combo) {
    nameEl.textContent = combo.name;
    effectEl.textContent = combo.effect;
  } else {
    nameEl.textContent = '—';
    effectEl.textContent = 'Select techniques above';
  }
}

function initComboBuilder() { selectedTags.clear(); }

// ── CD4: Passport Stamps ─────────────────────────────────────
const STAMP_DRIVES = [
  {cd:1,icon:'🌐',color:'#F59E0B',name:'Epic Meaning'},
  {cd:2,icon:'🏆',color:'#3B82F6',name:'Accomplishment'},
  {cd:3,icon:'🧩',color:'#8B5CF6',name:'Creativity'},
  {cd:4,icon:'📦',color:'#10B981',name:'Ownership'},
  {cd:5,icon:'👥',color:'#EC4899',name:'Social'},
  {cd:6,icon:'⏳',color:'#F97316',name:'Scarcity'},
  {cd:7,icon:'🎲',color:'#06B6D4',name:'Curiosity'},
  {cd:8,icon:'🚩',color:'#EF4444',name:'Loss Avoid.'},
];
const STAMP_KEY = 'octalysis_stamps';

function getStamps() {
  try { return JSON.parse(localStorage.getItem(STAMP_KEY)) || []; } catch { return []; }
}
function saveStamps(arr) {
  try { localStorage.setItem(STAMP_KEY, JSON.stringify(arr)); } catch {}
}

function renderPassportStamps() {
  const claimed = getStamps();
  const count = claimed.length;
  const stamps = STAMP_DRIVES.map(d => {
    const isClaimed = claimed.includes(d.cd);
    return isClaimed
      ? `<div class="stamp-item claimed" style="--sc:${d.color};border:2px solid ${d.color};background:color-mix(in srgb,${d.color} 12%,#0d1117);box-shadow:0 0 10px color-mix(in srgb,${d.color} 30%,transparent);border-radius:8px;aspect-ratio:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:2px;font-size:1.3rem;cursor:default">
          ${d.icon}
          <div style="font-size:.52rem;font-weight:800;color:${d.color}">${d.name}</div>
         </div>`
      : `<div onclick="claimStamp(${d.cd})" style="border:2px dashed #30363d;border-radius:8px;aspect-ratio:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:2px;font-size:1.3rem;opacity:.4;cursor:pointer;transition:opacity .15s" onmouseenter="this.style.opacity='.7'" onmouseleave="this.style.opacity='.4'">
          ${d.icon}
          <div style="font-size:.52rem;color:#6b7280">${d.name}</div>
         </div>`;
  }).join('');

  return `
  <div class="demo-title">Passport Stamps</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;justify-content:space-between;font-size:.7rem;color:#6b7280;margin-bottom:12px">
      <span>Octalysis Passport</span>
      <span id="stamp-count-label">${count}/8 collected</span>
    </div>
    <div id="stamps-grid" style="display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-bottom:12px">
      ${stamps}
    </div>
    <div style="height:4px;background:#21262d;border-radius:2px;overflow:hidden">
      <div id="stamp-bar" style="width:${count/8*100}%;height:100%;background:linear-gradient(90deg,#10B981,#06B6D4);border-radius:2px;transition:width .4s"></div>
    </div>
    <div style="font-size:.65rem;color:#6b7280;margin-top:5px;text-align:center">Click unclaimed stamps to collect · Progress saved in browser</div>
  </div>`;
}

window.claimStamp = function(cd) {
  const claimed = getStamps();
  if (!claimed.includes(cd)) {
    claimed.push(cd);
    saveStamps(claimed);
  }
  // Re-render
  const demoEl = document.getElementById('demo-content');
  if (demoEl) { demoEl.innerHTML = renderPassportStamps(); }
};

function initPassportStamps() {}
```

- [ ] **Step 2: Verify CD3 and CD4**

Click CD3 (purple) — Combo Builder shows technique tags. Selecting 2 tags shows a named combo result. Click CD4 (green) — Passport Stamps shows 8-slot grid. Click an unclaimed stamp to collect it; progress bar advances. Reload page — stamps persist.

- [ ] **Step 3: Commit**

```bash
git add site/playground.html
git commit -m "feat(playground): CD3 combo builder + CD4 passport stamps (localStorage)"
```

---

## Task 11: Playground Demos — CD5 Live Pulse & CD6 Event Ticket

**Files:**
- Modify: `site/playground.html`

- [ ] **Step 1: Add CD5 Live Pulse and CD6 Event Ticket**

Append to `<script>`:

```js
// ── CD5: Live Pulse ──────────────────────────────────────────
const PULSE_MSGS = [
  '🎨 A designer in Berlin just earned Level 4',
  '💻 12 developers are exploring CD7 right now',
  '📚 An educator from Tokyo claimed their first stamp',
  '🚀 3 people just completed the XP challenge',
  '🌍 84 new members joined this week',
  '🏆 Someone in São Paulo hit Expert tier',
  '💡 15 designers are using the Combo Builder',
  '🔥 A streak champion in Seoul: 42 days running',
];
let pulseInterval = null;

function renderLivePulse() {
  return `
  <div class="demo-title">Live Pulse</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;align-items:center;gap:8px;margin-bottom:12px">
      <div style="width:8px;height:8px;border-radius:50%;background:#10B981;box-shadow:0 0 8px #10B981;animation:pulse-dot 1.5s infinite"></div>
      <span style="font-size:.72rem;font-weight:700;color:#10B981">Live Activity</span>
    </div>
    <style>@keyframes pulse-dot{0%,100%{opacity:1}50%{opacity:.3}}</style>
    <div id="pulse-feed" style="display:flex;flex-direction:column;gap:6px;max-height:200px;overflow:hidden"></div>
  </div>`;
}

function initLivePulse() {
  if (pulseInterval) clearInterval(pulseInterval);
  const feed = document.getElementById('pulse-feed');
  if (!feed) return;
  feed.innerHTML = '';

  function addMsg() {
    const el = document.getElementById('pulse-feed');
    if (!el) { clearInterval(pulseInterval); return; }
    const msg = PULSE_MSGS[Math.floor(Math.random() * PULSE_MSGS.length)];
    const row = document.createElement('div');
    row.style.cssText = 'display:flex;align-items:flex-start;gap:8px;background:#161b22;border-radius:8px;padding:8px 10px;border:1px solid #21262d;opacity:0;transition:opacity .4s;font-size:.78rem;color:#e6edf3';
    row.innerHTML = `<span style="flex-shrink:0;margin-top:1px;font-size:.7rem;color:#6b7280">${new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'})}</span>${msg}`;
    el.insertBefore(row, el.firstChild);
    requestAnimationFrame(() => row.style.opacity = '1');
    while (el.children.length > 5) el.removeChild(el.lastChild);
  }

  addMsg();
  pulseInterval = setInterval(addMsg, 2200);
}

// ── CD6: Event Ticket ────────────────────────────────────────
let ticketClaimed = false;
let countdownInterval = null;
let remainingSeconds = 23 * 3600 + 47 * 60 + 12;

function renderEventTicket() {
  const seats = ticketClaimed ? 4 : 3;
  const seatsHtml = Array.from({length:5}, (_,i) =>
    i < seats
      ? `<div style="width:18px;height:18px;border-radius:50%;background:#F97316;border:2px solid #ea580c"></div>`
      : `<div style="width:18px;height:18px;border-radius:50%;border:2px dashed #30363d;background:#0d1117"></div>`
  ).join('');

  return `
  <div class="demo-title">Event Ticket</div>
  <div style="border-radius:12px;overflow:hidden;border:1px solid #30363d">
    <div style="background:linear-gradient(135deg,#7c2d12,#c2410c);padding:16px;text-align:center">
      <div style="font-size:.65rem;color:#fed7aa;letter-spacing:.8px;text-transform:uppercase;margin-bottom:4px">Octalysis Workshop</div>
      <div style="font-size:1.1rem;font-weight:900;color:#fff">Live Design Sprint</div>
    </div>
    <div style="background:#161b22;padding:16px">
      <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:14px">
        <div>
          <div style="font-size:.62rem;color:#6b7280;text-transform:uppercase;letter-spacing:.6px">Time Remaining</div>
          <div id="ticket-countdown" style="font-size:1.6rem;font-weight:900;color:#F97316;letter-spacing:-.02em">23:47:12</div>
        </div>
        <div>
          <div style="font-size:.62rem;color:#6b7280;text-transform:uppercase;letter-spacing:.6px;margin-bottom:5px">Seats</div>
          <div style="display:flex;gap:4px">${seatsHtml}</div>
          <div style="font-size:.62rem;color:#F97316;margin-top:3px">${5-seats} of 5 remaining</div>
        </div>
      </div>
      <button id="ticket-btn" onclick="claimTicket()" ${ticketClaimed ? 'disabled' : ''}
        style="width:100%;background:${ticketClaimed ? '#21262d' : '#F97316'};color:${ticketClaimed ? '#6b7280' : '#fff'};border:none;border-radius:8px;padding:10px;font-size:.84rem;font-weight:700;cursor:${ticketClaimed ? 'default' : 'pointer'};transition:background .2s">
        ${ticketClaimed ? '✓ Spot Claimed' : 'Claim Your Spot'}
      </button>
    </div>
  </div>`;
}

window.claimTicket = function() {
  if (ticketClaimed) return;
  ticketClaimed = true;
  const demoEl = document.getElementById('demo-content');
  if (demoEl) demoEl.innerHTML = renderEventTicket();
  initEventTicket();
};

function formatCountdown(s) {
  const h = Math.floor(s/3600), m = Math.floor((s%3600)/60), sec = s%60;
  return `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(sec).padStart(2,'0')}`;
}

function initEventTicket() {
  if (countdownInterval) clearInterval(countdownInterval);
  countdownInterval = setInterval(() => {
    const el = document.getElementById('ticket-countdown');
    if (!el) { clearInterval(countdownInterval); return; }
    remainingSeconds = Math.max(0, remainingSeconds - 1);
    el.textContent = formatCountdown(remainingSeconds);
  }, 1000);
}
```

- [ ] **Step 2: Verify CD5 and CD6**

Click CD5 (pink) — Live Pulse shows a feed of activity messages auto-appending every 2.2s with fade-in. Click CD6 (orange) — Event Ticket shows countdown timer ticking, 3/5 filled seat holes, "Claim Your Spot" button; clicking it fills a seat and shows "✓ Spot Claimed."

- [ ] **Step 3: Commit**

```bash
git add site/playground.html
git commit -m "feat(playground): CD5 live pulse + CD6 event ticket demos"
```

---

## Task 12: Playground Demos — CD7 Scratch Card & CD8 Streak Tracker

**Files:**
- Modify: `site/playground.html`

- [ ] **Step 1: Add CD7 Scratch Card and CD8 Streak Tracker**

Append to `<script>`:

```js
// ── CD7: Scratch Card ────────────────────────────────────────
const SCRATCH_POOL = [
  {name:'Variable Reward', desc:'Unpredictable payoffs are more motivating than predictable ones.'},
  {name:'Easter Egg', desc:'Hidden surprises reward curious explorers and create delight.'},
  {name:'Glowing Choice', desc:'Highlighting one option makes it feel special and urgent.'},
  {name:'Rolling Rewards', desc:'Rewards that build over time create anticipation for the next cycle.'},
  {name:'Sudden Reward', desc:'Unexpected bonuses create emotional spikes stronger than any announcement.'},
  {name:'Mini Quest', desc:'Small side-challenges break monotony and reward exploration.'},
  {name:'Visual Storytelling', desc:'Showing events as they unfold keeps users engaged with what happens next.'},
  {name:'Evolved UI', desc:'An interface that reveals new features over time sustains long-term curiosity.'},
];

let currentScratch = null;
let scratchRevealed = false;

function pickScratch() {
  return SCRATCH_POOL[Math.floor(Math.random() * SCRATCH_POOL.length)];
}

function renderScratchCard() {
  currentScratch = pickScratch();
  scratchRevealed = false;
  return `
  <div class="demo-title">Scratch Card</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d;text-align:center">
    <div style="font-size:.72rem;color:#6b7280;margin-bottom:14px">Click to reveal a hidden technique</div>
    <div id="scratch-card" onclick="revealScratch()" style="position:relative;height:110px;border-radius:10px;overflow:hidden;cursor:pointer;margin-bottom:14px">
      <!-- Revealed content (behind) -->
      <div style="position:absolute;inset:0;background:linear-gradient(135deg,#0e4d4d,#0d3d5a);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:6px;padding:12px">
        <div id="scratch-technique" style="font-size:.95rem;font-weight:900;color:#06B6D4">${currentScratch.name}</div>
        <div id="scratch-desc" style="font-size:.75rem;color:#8b949e;line-height:1.5;max-width:220px">${currentScratch.desc}</div>
      </div>
      <!-- Scratch overlay (on top) -->
      <div id="scratch-overlay" style="position:absolute;inset:0;background:repeating-linear-gradient(45deg,#21262d,#21262d 4px,#1c2129 4px,#1c2129 8px);display:flex;align-items:center;justify-content:center;transition:opacity .5s">
        <div style="font-size:1.8rem">🪙</div>
        <div style="font-size:.75rem;color:#8b949e;margin-left:8px">Scratch here</div>
      </div>
    </div>
    <button onclick="resetScratch()" style="background:#161b22;color:#06B6D4;border:1px solid #06B6D4;border-radius:8px;padding:7px 16px;font-size:.78rem;font-weight:700;cursor:pointer">↺ Scratch another</button>
  </div>`;
}

window.revealScratch = function() {
  if (scratchRevealed) return;
  scratchRevealed = true;
  const overlay = document.getElementById('scratch-overlay');
  if (overlay) { overlay.style.opacity = '0'; overlay.style.pointerEvents = 'none'; }
};

window.resetScratch = function() {
  const demoEl = document.getElementById('demo-content');
  if (demoEl) demoEl.innerHTML = renderScratchCard();
};

function initScratchCard() {}

// ── CD8: Streak Tracker ──────────────────────────────────────
let streakKept = false;

function renderStreakTracker() {
  const days = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
  const filledDays = [0,1,2,3,4]; // Mon–Fri filled
  const todayIdx = 5; // Sat = today (at risk)

  const dayDots = days.map((day, i) => {
    let bg, border, color;
    if (streakKept && i === todayIdx) {
      bg = '#10B981'; border = '#059669'; color = '#6ee7b7';
    } else if (filledDays.includes(i)) {
      bg = '#EF4444'; border = '#dc2626'; color = '#fca5a5';
    } else if (i === todayIdx) {
      bg = 'transparent'; border = '#EF4444'; color = '#EF4444';
    } else {
      bg = '#21262d'; border = '#30363d'; color = '#6b7280';
    }
    return `<div style="display:flex;flex-direction:column;align-items:center;gap:3px">
      <div style="width:28px;height:28px;border-radius:50%;background:${bg};border:2px solid ${border};display:flex;align-items:center;justify-content:center;font-size:.6rem;font-weight:800;color:${color}">${i === todayIdx ? (streakKept ? '✓' : '?') : '✓'}</div>
      <div style="font-size:.55rem;color:#6b7280">${day}</div>
    </div>`;
  }).join('');

  const warningHtml = streakKept
    ? `<div style="display:flex;align-items:center;gap:6px;background:#064e3b;border-radius:8px;padding:8px 12px;border:1px solid #059669;font-size:.78rem;color:#6ee7b7"><span>✓</span> Streak protected — 6 days strong!</div>`
    : `<div style="display:flex;align-items:center;gap:6px;background:#450a0a;border-radius:8px;padding:8px 12px;border:1px solid #dc2626;font-size:.78rem;color:#fca5a5"><span>⚠️</span> Streak at risk — complete today's task to keep it!</div>`;

  return `
  <div class="demo-title">Streak Tracker</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;align-items:center;gap:8px;margin-bottom:14px">
      <span style="font-size:1.8rem">🔥</span>
      <div>
        <div style="font-size:1.2rem;font-weight:900;color:${streakKept ? '#10B981' : '#EF4444'}">${streakKept ? 6 : 5} day streak</div>
        <div style="font-size:.68rem;color:#6b7280">Keep going — don't break the chain</div>
      </div>
    </div>
    <div style="display:flex;justify-content:space-between;margin-bottom:14px">${dayDots}</div>
    ${warningHtml}
    ${streakKept ? '' : `<button onclick="keepStreak()" style="width:100%;margin-top:10px;background:#EF4444;color:#fff;border:none;border-radius:8px;padding:10px;font-size:.84rem;font-weight:700;cursor:pointer">🔥 Keep My Streak</button>`}
  </div>`;
}

window.keepStreak = function() {
  streakKept = true;
  const demoEl = document.getElementById('demo-content');
  if (demoEl) { demoEl.innerHTML = renderStreakTracker(); }
};

function initStreakTracker() { streakKept = false; }
```

- [ ] **Step 2: Verify CD7 and CD8**

Click CD7 (cyan) — Scratch card shows textured overlay. Click overlay to reveal technique name + description with fade. "↺ Scratch another" picks a new random technique. Click CD8 (red) — Streak Tracker shows 5-day streak, "⚠️ At risk" warning, "🔥 Keep My Streak" button. Clicking it turns Saturday green and changes warning to "✓ Streak protected."

- [ ] **Step 3: Cleanup: stop intervals when switching drives**

At the top of `activateDrive()`, add cleanup calls before rendering:

```js
function activateDrive(cd) {
  // Cleanup running intervals from previous demo
  if (pulseInterval) { clearInterval(pulseInterval); pulseInterval = null; }
  if (countdownInterval) { clearInterval(countdownInterval); countdownInterval = null; }
  // ... rest of activateDrive unchanged
```

- [ ] **Step 4: Commit**

```bash
git add site/playground.html
git commit -m "feat(playground): CD7 scratch card + CD8 streak tracker + interval cleanup"
```

---

## Task 13: Final Wiring, Polish & Deploy Check

**Files:**
- Modify: `site/index.html`, `site/playground.html`

- [ ] **Step 1: Add section anchors**

Ensure `site/index.html` has:
- `id="apply"` on the "How to Apply" section wrapper
- `id="about"` on the About/Attribution section wrapper
- `id="core-drives"` on the hero (already added in Task 3)

- [ ] **Step 2: Verify cross-page links**

In `site/index.html`: click "🎮 Playground" nav pill — should open `playground.html`.  
In `site/playground.html`: click "📖 Learn" nav pill — should open `index.html`.  
In `playground.html` description panel: click "→ Read full description" for any active drive — should open `index.html#drive-N`.

- [ ] **Step 3: Verify Learn page on mobile viewport**

Open `site/index.html` in browser devtools at 375px width. Octagon hero should scale to `max-width: 500px`. Nav wraps without overflow. Drive cards stack into single column.

- [ ] **Step 4: Verify Playground page on mobile viewport**

At 375px, the hub grid should stack to single column (`.hub { grid-template-columns: 1fr }`). Octagon resizes to 220px.

- [ ] **Step 5: Smoke test deploy**

```bash
# Run a local HTTP server from the site dir
cd site && python3 -m http.server 8080
```

Visit `http://localhost:8080/` and `http://localhost:8080/playground.html`. Both pages should load without console errors.

- [ ] **Step 6: Final commit**

```bash
git add site/index.html site/playground.html
git commit -m "feat: wire cross-page links, fix anchors, verify responsive layout"
```

---

## Spec Coverage Check

| Spec Requirement | Covered By |
|-----------------|-----------|
| Learn page — light editorial nav with Playground pill | Task 2 |
| Spoke-wheel octagon hero, click to scroll | Task 3 |
| Quick reference 4×2 grid | (kept from existing, no change needed) |
| Drive cards: 6 examples, 3-col | Task 5 |
| Drive cards: UI technique demo panels | Task 5 |
| Drive cards: full technique chip list | Task 5 |
| Hat + Brain inline tags on card header | Task 5 |
| Remove standalone Hat/Brain sections | Task 4 |
| Sticky sidebar octagon, scroll-linked | Task 6 |
| How to Apply section (kept) | Task 13 |
| About / Attribution section (kept) | Task 13 |
| Playground — dark shell, nav, hero | Task 7 |
| 3-panel octagon hub (description / octagon / demo) | Task 8 |
| Octagon hover/active states with glow | Task 8 |
| CD1 Mission Counter demo | Task 9 |
| CD2 XP Level-Up demo | Task 9 |
| CD3 Combo Builder demo | Task 10 |
| CD4 Passport Stamps (localStorage) demo | Task 10 |
| CD5 Live Pulse demo | Task 11 |
| CD6 Event Ticket + countdown demo | Task 11 |
| CD7 Scratch Card demo | Task 12 |
| CD8 Streak Tracker demo | Task 12 |
| Full technique library (~48 techniques) | Task 5 |
| `.superpowers/` in `.gitignore` | Task 1 |
