# Playground Technique Tabs — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a tab bar to the playground's demo panel so each Core Drive shows multiple technique demos (existing demo becomes Tab 1, plus 3 new tabs per drive = 24 new demos).

**Architecture:** All changes are in `playground.html` (single-file app). Each drive in the `DRIVES` array gains a `tabs` property listing `{name, render, init}` objects. A new `activateTab(cd, tabIdx)` function replaces the current `renderDemo`/`initDemo` calls in `activateDrive()`. New render/init function pairs implement each technique demo.

**Tech Stack:** Vanilla HTML/CSS/JS, no build step. Open `playground.html` directly in a browser to verify.

---

## Task 1: Tab bar infrastructure

**Files:**
- Modify: `playground.html`

This task wires the tab mechanism without adding any new demos. After this task, clicking a drive shows the same single demo it always did — but now inside a tab bar with one tab.

- [ ] **Step 1: Add tab bar CSS**

In `playground.html`, find the `/* FOOTER */` comment (around line 91) and insert this CSS block before it:

```css
/* TAB BAR */
.tab-bar {
  display:flex; overflow-x:auto; border-bottom:1px solid var(--border);
  margin:-4px 0 16px; scrollbar-width:none;
}
.tab-bar::-webkit-scrollbar { display:none; }
.tab-btn {
  padding:8px 14px; font-size:.7rem; font-weight:700; white-space:nowrap;
  border:none; border-bottom:2px solid transparent; background:none;
  cursor:pointer; color:var(--muted); transition:all .15s; flex-shrink:0;
}
.tab-btn:hover { color:var(--text); }
```

- [ ] **Step 2: Add `tabs` arrays to DRIVES**

Replace the entire `DRIVES` array (lines ~152–185) with the version below. Each drive gets a `tabs` property referencing existing render/init functions. New tabs will be added in later tasks.

```js
const DRIVES = [
  { cd:1, name:'Epic Meaning & Calling',      icon:'🌐', color:'#F59E0B',
    hat:'⬜ White Hat', brain:'🧠 Right Brain',
    desc:'The belief that you are doing something greater than yourself. When users feel chosen for a mission, effort becomes purposeful.',
    tabs:[
      {name:'Mission Counter', render:()=>renderMissionCounter(), init:()=>{}},
    ]},
  { cd:2, name:'Development & Accomplishment', icon:'🏆', color:'#3B82F6',
    hat:'⬜ White Hat', brain:'🔢 Left Brain',
    desc:'The internal drive to progress, develop skills, and overcome challenges. Progress bars and XP make invisible growth visible.',
    tabs:[
      {name:'XP Level-Up', render:()=>renderXPLevelUp(), init:()=>initXPLevelUp()},
    ]},
  { cd:3, name:'Empowerment of Creativity',    icon:'🧩', color:'#8B5CF6',
    hat:'⬜ White Hat', brain:'🧠 Right Brain',
    desc:'The drive to try new combinations and get meaningful feedback. When every choice leads to a unique outcome, creativity loops become addictive.',
    tabs:[
      {name:'Combo Builder', render:()=>renderComboBuilder(), init:()=>initComboBuilder()},
    ]},
  { cd:4, name:'Ownership & Possession',       icon:'📦', color:'#10B981',
    hat:'⬜ White Hat', brain:'🔢 Left Brain',
    desc:'The drive to accumulate and protect things you own. Collection, virtual currency, and customisation trigger this drive deeply.',
    tabs:[
      {name:'Passport Stamps', render:()=>renderPassportStamps(), init:()=>initPassportStamps()},
    ]},
  { cd:5, name:'Social Influence & Relatedness', icon:'👥', color:'#EC4899',
    hat:'⬜ White Hat', brain:'🧠 Right Brain',
    desc:'The drive to collaborate, compete, and belong. Seeing what peers do creates social benchmarking and conformity pressure.',
    tabs:[
      {name:'Live Pulse', render:()=>renderLivePulse(), init:()=>initLivePulse()},
    ]},
  { cd:6, name:'Scarcity & Impatience',        icon:'⏳', color:'#F97316',
    hat:'⬛ Black Hat', brain:'🔢 Left Brain',
    desc:'Wanting something more because you cannot have it yet. Countdown timers and limited availability create urgent desire.',
    tabs:[
      {name:'Event Ticket', render:()=>renderEventTicket(), init:()=>initEventTicket()},
    ]},
  { cd:7, name:'Unpredictability & Curiosity', icon:'🎲', color:'#06B6D4',
    hat:'⬛ Black Hat', brain:'🧠 Right Brain',
    desc:'Not knowing what comes next keeps the brain engaged. Variable rewards and hidden surprises create compulsive curiosity.',
    tabs:[
      {name:'Scratch Card', render:()=>renderScratchCard(), init:()=>initScratchCard()},
    ]},
  { cd:8, name:'Loss & Avoidance',             icon:'🚩', color:'#EF4444',
    hat:'⬛ Black Hat', brain:'🔢 Left Brain',
    desc:'The drive to avoid losing what you already have. Streaks at risk, expiring progress, and FOMO are powerful motivators.',
    tabs:[
      {name:'Streak Tracker', render:()=>renderStreakTracker(), init:()=>initStreakTracker()},
    ]},
];
```

- [ ] **Step 3: Add interval variables, `clearAllIntervals`, `renderTabBar`, and `activateTab`**

Find the line `let activeCd = null;` (line ~187) and insert the new interval variables after it:

```js
let activeCd = null;
// Interval handles for all auto-running demos (cleared on drive/tab switch)
let apptInterval = null;
let lossInterval = null;
let fomoInterval = null;
let rollInterval = null;
let chainTimer   = null;
```

Then find the comment `// ── Activate a drive` (line ~239) and insert these functions immediately before it:

```js
// ── Clear all running demo intervals ────────────────────────────
function clearAllIntervals() {
  [pulseInterval, countdownInterval, apptInterval, lossInterval, fomoInterval, rollInterval, chainTimer]
    .forEach(id => { if (id) clearInterval(id); });
  pulseInterval = countdownInterval = apptInterval = lossInterval = fomoInterval = rollInterval = chainTimer = null;
}

// ── Tab bar renderer ────────────────────────────────────────────
function renderTabBar(d, activeIdx) {
  return d.tabs.map((t, i) =>
    `<button class="tab-btn${i===activeIdx?' active':''}"
      style="${i===activeIdx?`border-bottom-color:${d.color};color:${d.color}`:''}"
      onclick="switchTab(${d.cd},${i})">${t.name}</button>`
  ).join('');
}

// ── Activate a specific tab within the active drive ─────────────
function activateTab(cd, tabIdx) {
  clearAllIntervals();
  const d   = DRIVES[cd - 1];
  const tab = d.tabs[tabIdx];
  const demoEl = document.getElementById('demo-content');
  demoEl.classList.remove('visible');
  demoEl.innerHTML = `<div class="tab-bar">${renderTabBar(d, tabIdx)}</div>${tab.render()}`;
  requestAnimationFrame(() => { demoEl.classList.add('visible'); tab.init(); });
}

window.switchTab = function(cd, tabIdx) { activateTab(cd, tabIdx); };
```

- [ ] **Step 4: Update `activateDrive` to use `activateTab`**

Inside `activateDrive(cd)`, find the block that renders the demo panel (lines ~273–280):

```js
  // Render demo panel
  const demoEl = document.getElementById('demo-content');
  demoEl.classList.remove('visible');
  demoEl.innerHTML = renderDemo(cd);
  requestAnimationFrame(() => {
    demoEl.classList.add('visible');
    initDemo(cd);
  });
```

Replace it with:

```js
  // Render demo panel
  activateTab(cd, 0);
```

- [ ] **Step 5: Verify in browser**

Open `playground.html` in a browser. Click each octagon segment.

Expected:
- Each drive shows a single tab button above its demo
- Tab is drive-coloured with underline
- Demo content and behaviour unchanged
- Clicking a different drive resets to its single tab

- [ ] **Step 6: Commit**

```bash
git add playground.html
git commit -m "feat: add tab bar infrastructure to demo panel"
```

---

## Task 2: CD1 — Epic Meaning new tabs

**Files:**
- Modify: `playground.html`

Add three technique demos to CD1: Narrative, Humanity Hero, Co-creationist.

- [ ] **Step 1: Add tabs to CD1 in DRIVES**

In the DRIVES array, find the CD1 entry and replace its `tabs` array:

```js
tabs:[
  {name:'Mission Counter', render:()=>renderMissionCounter(), init:()=>{}},
  {name:'Narrative',       render:()=>renderNarrative(),      init:()=>initNarrative()},
  {name:'Humanity Hero',   render:()=>renderHumanityHero(),   init:()=>{}},
  {name:'Co-creationist',  render:()=>renderCoCreationist(),  init:()=>initCoCreationist()},
],
```

- [ ] **Step 2: Add render/init functions for CD1 new tabs**

Find the comment `// ── CD2:` and insert the following block immediately before it:

```js
// ── CD1: Narrative ───────────────────────────────────────────────
const NARRATIVE_LINES = [
  '🌍 Every great product starts with a problem worth solving.',
  '💡 Somewhere, a designer saw friction where others saw routine.',
  '🛠 They built slowly, tested often, and listened carefully.',
  '🚀 And one day — it shipped. Not perfect, but real.',
  '🌐 That\'s how missions are born. What\'s yours?',
];
let narrativeIdx = 0;

function renderNarrative() {
  return `
  <div class="demo-title">Narrative</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="font-size:.65rem;color:#6b7280;letter-spacing:.8px;text-transform:uppercase;margin-bottom:12px">The Origin Story</div>
    <div id="narrative-lines" style="min-height:80px;margin-bottom:16px"></div>
    <button onclick="advanceNarrative()" id="narrative-btn"
      style="background:#F59E0B;color:#111;border:none;border-radius:8px;padding:8px 20px;font-size:.82rem;font-weight:700;cursor:pointer">
      ▶ Begin
    </button>
  </div>`;
}

window.advanceNarrative = function() {
  if (narrativeIdx >= NARRATIVE_LINES.length) return;
  const linesEl = document.getElementById('narrative-lines');
  if (!linesEl) return;
  const line = document.createElement('p');
  line.style.cssText = 'font-size:.85rem;color:#e6edf3;line-height:1.7;margin-bottom:6px;opacity:0;transition:opacity .4s';
  line.textContent = NARRATIVE_LINES[narrativeIdx];
  linesEl.appendChild(line);
  requestAnimationFrame(() => line.style.opacity = '1');
  narrativeIdx++;
  const btn = document.getElementById('narrative-btn');
  if (!btn) return;
  btn.textContent = narrativeIdx >= NARRATIVE_LINES.length ? '✓ Story Complete' : '▶ Next Chapter';
  if (narrativeIdx >= NARRATIVE_LINES.length) { btn.disabled = true; btn.style.opacity = '.5'; }
};

function initNarrative() { narrativeIdx = 0; }

// ── CD1: Humanity Hero ───────────────────────────────────────────
function renderHumanityHero() {
  return `
  <div class="demo-title">Humanity Hero</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;text-align:center">
    <div style="font-size:2.2rem;margin-bottom:8px">🦸</div>
    <div style="font-size:.72rem;color:#6b7280;margin-bottom:4px">Your contributions have helped</div>
    <div id="hero-count" style="font-size:2.4rem;font-weight:900;color:#F59E0B;letter-spacing:-.03em">0</div>
    <div style="font-size:.78rem;color:#8b949e;margin-bottom:16px">people learn gamification design</div>
    <button onclick="heroContribute()"
      style="background:#F59E0B;color:#111;border:none;border-radius:8px;padding:8px 20px;font-size:.82rem;font-weight:700;cursor:pointer">
      + Make an Impact
    </button>
  </div>`;
}

window.heroContribute = function() {
  const el = document.getElementById('hero-count');
  if (!el) return;
  let current = parseInt(el.textContent.replace(/,/g, ''), 10);
  const target = current + Math.floor(Math.random() * 40 + 15);
  const step = () => {
    current = Math.min(current + Math.ceil((target - current) / 4), target);
    el.textContent = current.toLocaleString();
    if (current < target) requestAnimationFrame(step);
  };
  requestAnimationFrame(step);
};

// ── CD1: Co-creationist ──────────────────────────────────────────
let coVotes = { a: 62, b: 38 };
let coHasVoted = false;

function renderCoCreationist() {
  const total = coVotes.a + coVotes.b;
  const pctA = Math.round(coVotes.a / total * 100);
  const pctB = 100 - pctA;
  return `
  <div class="demo-title">Co-creationist</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="font-size:.78rem;color:#e6edf3;font-weight:700;margin-bottom:4px">What should we build next?</div>
    <div style="font-size:.65rem;color:#6b7280;margin-bottom:14px">You're shaping the product roadmap</div>
    <div style="display:flex;flex-direction:column;gap:10px">
      <div onclick="coVote('a')" id="co-opt-a"
        style="background:#161b22;border:1px solid #30363d;border-radius:8px;padding:12px;cursor:pointer;transition:border-color .15s">
        <div style="display:flex;justify-content:space-between;margin-bottom:6px">
          <span style="font-size:.8rem;color:#e6edf3">🗺 Technique Explorer</span>
          <span id="co-pct-a" style="font-size:.72rem;font-weight:700;color:#F59E0B">${pctA}%</span>
        </div>
        <div style="height:5px;background:#21262d;border-radius:3px;overflow:hidden">
          <div id="co-bar-a" style="width:${pctA}%;height:100%;background:#F59E0B;transition:width .4s;border-radius:3px"></div>
        </div>
      </div>
      <div onclick="coVote('b')" id="co-opt-b"
        style="background:#161b22;border:1px solid #30363d;border-radius:8px;padding:12px;cursor:pointer;transition:border-color .15s">
        <div style="display:flex;justify-content:space-between;margin-bottom:6px">
          <span style="font-size:.8rem;color:#e6edf3">🧪 Gamification Simulator</span>
          <span id="co-pct-b" style="font-size:.72rem;font-weight:700;color:#F59E0B">${pctB}%</span>
        </div>
        <div style="height:5px;background:#21262d;border-radius:3px;overflow:hidden">
          <div id="co-bar-b" style="width:${pctB}%;height:100%;background:#F59E0B;transition:width .4s;border-radius:3px"></div>
        </div>
      </div>
    </div>
    <div id="co-voted" style="font-size:.65rem;color:#6b7280;text-align:center;margin-top:10px;${coHasVoted?'':'display:none'}">
      ✓ Vote counted — thanks for shaping this project!
    </div>
  </div>`;
}

window.coVote = function(choice) {
  if (coHasVoted) return;
  coHasVoted = true;
  coVotes[choice] += 5;
  const total = coVotes.a + coVotes.b;
  ['a','b'].forEach(k => {
    const pct = Math.round(coVotes[k] / total * 100);
    const pctEl = document.getElementById(`co-pct-${k}`);
    const barEl = document.getElementById(`co-bar-${k}`);
    const optEl = document.getElementById(`co-opt-${k}`);
    if (pctEl) pctEl.textContent = pct + '%';
    if (barEl) barEl.style.width = pct + '%';
    if (optEl && k === choice) optEl.style.borderColor = '#F59E0B';
  });
  const votedEl = document.getElementById('co-voted');
  if (votedEl) votedEl.style.display = 'block';
};

function initCoCreationist() { coVotes = {a:62, b:38}; coHasVoted = false; }
```

- [ ] **Step 3: Verify in browser**

Open `playground.html`. Click CD1 (Epic Meaning).

Expected:
- Tab bar shows 4 tabs: Mission Counter · Narrative · Humanity Hero · Co-creationist
- Mission Counter tab works as before
- Narrative tab: click "Begin" → lines appear one by one
- Humanity Hero tab: click button → count animates up
- Co-creationist tab: click an option → bar shifts, vote message appears
- Switching drives and back resets all demos

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd1): add Narrative, Humanity Hero, Co-creationist tabs"
```

---

## Task 3: CD2 — Accomplishment new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD2 in DRIVES**

```js
tabs:[
  {name:'XP Level-Up',  render:()=>renderXPLevelUp(),   init:()=>initXPLevelUp()},
  {name:'Leaderboard',  render:()=>renderLeaderboard(),  init:()=>initLeaderboard()},
  {name:'Boss Fight',   render:()=>renderBossFight(),    init:()=>initBossFight()},
  {name:'Quest List',   render:()=>renderQuestList(),    init:()=>initQuestList()},
],
```

- [ ] **Step 2: Add render/init functions for CD2 new tabs**

Find `// ── CD3:` and insert immediately before it:

```js
// ── CD2: Leaderboard ─────────────────────────────────────────────
let lbYourXP = 3940;

function renderLeaderboard() {
  return `
  <div class="demo-title">Leaderboard</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;flex-direction:column;gap:5px">
      <div style="display:flex;align-items:center;gap:10px;padding:7px 10px;border-radius:7px;background:#1a2d4a">
        <span style="width:20px;font-size:.8rem">🥇</span>
        <span style="flex:1;font-size:.78rem">Maya K.</span>
        <span style="font-size:.68rem;font-weight:700;color:#3B82F6">4,820 XP</span>
      </div>
      <div style="display:flex;align-items:center;gap:10px;padding:7px 10px;border-radius:7px;background:#1a2d4a">
        <span style="width:20px;font-size:.8rem">🥈</span>
        <span style="flex:1;font-size:.78rem">Luca T.</span>
        <span style="font-size:.68rem;font-weight:700;color:#3B82F6">4,310 XP</span>
      </div>
      <div style="display:flex;align-items:center;gap:10px;padding:7px 10px;border-radius:7px;background:#2a1f3f;border:1px solid #8B5CF6">
        <span style="width:20px;font-size:.75rem;font-weight:900;color:#8B5CF6">3</span>
        <span style="flex:1;font-size:.78rem;color:#a78bfa;font-weight:700">You</span>
        <span id="lb-your-xp" style="font-size:.68rem;font-weight:700;color:#a78bfa">3,940 XP</span>
      </div>
      <div style="display:flex;align-items:center;gap:10px;padding:7px 10px;border-radius:7px">
        <span style="width:20px;font-size:.72rem;color:#6b7280">4</span>
        <span style="flex:1;font-size:.78rem;color:#8b949e">Rin S.</span>
        <span style="font-size:.68rem;font-weight:700;color:#6b7280">3,210 XP</span>
      </div>
    </div>
    <div id="lb-nudge" style="font-size:.65rem;color:#6b7280;text-align:center;margin-top:10px;padding:7px;background:#0d1117;border-radius:6px">
      +370 XP to reach #2 · <button onclick="lbEarnXP()" style="background:none;border:none;color:#3B82F6;font-size:.65rem;font-weight:700;cursor:pointer;padding:0">Earn XP →</button>
    </div>
  </div>`;
}

window.lbEarnXP = function() {
  lbYourXP += 80;
  const xpEl  = document.getElementById('lb-your-xp');
  const nudge = document.getElementById('lb-nudge');
  if (xpEl) { xpEl.textContent = lbYourXP.toLocaleString() + ' XP'; xpEl.style.transform='scale(1.15)'; xpEl.style.color='#fff'; setTimeout(()=>{xpEl.style.transform='';xpEl.style.color='#a78bfa';},300); }
  if (nudge) {
    const gap = 4310 - lbYourXP;
    nudge.innerHTML = gap <= 0
      ? '🎉 You reached #2!'
      : `+${gap} XP to reach #2 · <button onclick="lbEarnXP()" style="background:none;border:none;color:#3B82F6;font-size:.65rem;font-weight:700;cursor:pointer;padding:0">Earn XP →</button>`;
  }
};

function initLeaderboard() { lbYourXP = 3940; }

// ── CD2: Boss Fight ──────────────────────────────────────────────
let bossHp = 62;
let bossLocked = false;

const BOSS_QUESTIONS = [
  { q:'Which Core Drive uses Countdown Timers?',          a:1, opts:['Epic Meaning','Scarcity & Impatience','Loss & Avoidance'] },
  { q:'What does CD3 (Empowerment of Creativity) drive?', a:0, opts:['Creative expression & feedback','Fear of missing out','Social conformity'] },
  { q:'Which hat colour labels CD8?',                     a:2, opts:['White Hat','Grey Hat','Black Hat'] },
];
let bossQIdx = 0;

function renderBossFight() {
  bossHp = 62; bossLocked = false; bossQIdx = 0;
  return `
  <div class="demo-title">Boss Fight</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="text-align:center;font-size:2rem;margin-bottom:4px" id="boss-emoji">👾</div>
    <div style="font-size:.75rem;font-weight:800;text-align:center;color:#e6edf3;margin-bottom:2px">The Complexity Boss</div>
    <div style="font-size:.6rem;color:#6b7280;text-align:center;margin-bottom:6px">HP: <span id="boss-hp-label">62%</span></div>
    <div style="height:10px;background:#21262d;border-radius:5px;overflow:hidden;margin-bottom:12px">
      <div id="boss-hp-bar" style="width:62%;height:100%;background:linear-gradient(90deg,#EF4444,#F97316);border-radius:5px;transition:width .5s"></div>
    </div>
    <div id="boss-q-block" style="background:#161b22;border:1px solid #30363d;border-radius:8px;padding:12px">
      <div id="boss-q" style="font-size:.75rem;color:#8b949e;margin-bottom:8px">${BOSS_QUESTIONS[0].q}</div>
      <div id="boss-opts" style="display:flex;flex-direction:column;gap:5px">
        ${BOSS_QUESTIONS[0].opts.map((o,i)=>`<div onclick="bossAnswer(${i})" data-idx="${i}"
          style="font-size:.72rem;padding:7px 10px;border-radius:6px;border:1px solid #30363d;cursor:pointer;transition:background .15s"
          onmouseenter="if(!this.dataset.done)this.style.background='#21262d'" onmouseleave="if(!this.dataset.done)this.style.background=''">
          ${String.fromCharCode(65+i)}. ${o}</div>`).join('')}
      </div>
    </div>
    <div id="boss-msg" style="font-size:.68rem;color:#8b949e;text-align:center;margin-top:8px">Answer correctly to deal damage</div>
  </div>`;
}

window.bossAnswer = function(chosenIdx) {
  if (bossLocked) return;
  const q = BOSS_QUESTIONS[bossQIdx];
  const opts = document.querySelectorAll('#boss-opts [data-idx]');
  if (chosenIdx === q.a) {
    bossLocked = true;
    opts[chosenIdx].style.background='#064e3b'; opts[chosenIdx].style.borderColor='#059669'; opts[chosenIdx].style.color='#6ee7b7';
    bossHp = Math.max(0, bossHp - 22);
    const bar = document.getElementById('boss-hp-bar');
    const lbl = document.getElementById('boss-hp-label');
    const msg = document.getElementById('boss-msg');
    if (bar) bar.style.width = bossHp + '%';
    if (lbl) lbl.textContent = bossHp + '%';
    if (bossHp <= 0) {
      document.getElementById('boss-emoji').textContent = '💀';
      if (msg) msg.textContent = '🎉 Boss defeated! +500 XP earned';
    } else {
      bossQIdx = (bossQIdx + 1) % BOSS_QUESTIONS.length;
      setTimeout(() => {
        bossLocked = false;
        const qEl   = document.getElementById('boss-q');
        const optsEl = document.getElementById('boss-opts');
        const newQ  = BOSS_QUESTIONS[bossQIdx];
        if (qEl) qEl.textContent = newQ.q;
        if (optsEl) optsEl.innerHTML = newQ.opts.map((o,i)=>`<div onclick="bossAnswer(${i})" data-idx="${i}"
          style="font-size:.72rem;padding:7px 10px;border-radius:6px;border:1px solid #30363d;cursor:pointer;transition:background .15s"
          onmouseenter="if(!this.dataset.done)this.style.background='#21262d'" onmouseleave="if(!this.dataset.done)this.style.background=''">
          ${String.fromCharCode(65+i)}. ${o}</div>`).join('');
        if (msg) msg.textContent = '⚔️ Hit! ' + bossHp + '% HP remaining';
      }, 700);
    }
  } else {
    opts[chosenIdx].style.background='#2d0a0a'; opts[chosenIdx].style.borderColor='#dc2626'; opts[chosenIdx].style.color='#f87171';
    setTimeout(()=>{ opts[chosenIdx].style.background=''; opts[chosenIdx].style.borderColor=''; opts[chosenIdx].style.color=''; }, 600);
  }
};

function initBossFight() { bossHp=62; bossLocked=false; bossQIdx=0; }

// ── CD2: Quest List ──────────────────────────────────────────────
let questDone = 0;

function renderQuestList() {
  return `
  <div class="demo-title">Quest List</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="font-size:.68rem;color:#6b7280;margin-bottom:8px">Complete all tasks to unlock the next chapter</div>
    <div style="height:5px;background:#21262d;border-radius:3px;overflow:hidden;margin-bottom:12px">
      <div id="quest-bar" style="width:0%;height:100%;background:linear-gradient(90deg,#3B82F6,#8B5CF6);border-radius:3px;transition:width .4s"></div>
    </div>
    ${['Read the CD2 description','Try the XP Level-Up demo','Beat the Boss Fight','Reach the Leaderboard top 3'].map((task,i)=>`
    <div onclick="checkQuest(${i})" id="quest-row-${i}"
      style="display:flex;align-items:center;gap:10px;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:9px 12px;margin-bottom:6px;cursor:pointer;transition:opacity .2s">
      <div id="quest-check-${i}" style="width:18px;height:18px;border-radius:50%;border:2px solid #30363d;flex-shrink:0;display:flex;align-items:center;justify-content:center;font-size:.6rem;transition:all .2s"></div>
      <span style="font-size:.78rem">${task}</span>
    </div>`).join('')}
    <div id="quest-reward" style="display:none;background:#0d2137;border:1px solid #1d4ed8;border-radius:8px;padding:12px;text-align:center;margin-top:8px">
      <div style="font-size:1.4rem">🏆</div>
      <div style="font-size:.78rem;font-weight:700;color:#93c5fd;margin-top:4px">Chapter Complete!</div>
    </div>
  </div>`;
}

window.checkQuest = function(i) {
  const row   = document.getElementById(`quest-row-${i}`);
  const check = document.getElementById(`quest-check-${i}`);
  if (!row || !check || check.dataset.done) return;
  check.dataset.done = '1';
  check.style.background='#3B82F6'; check.style.borderColor='#3B82F6'; check.textContent='✓'; check.style.color='#fff';
  row.style.opacity='.5'; row.style.pointerEvents='none';
  questDone++;
  const bar = document.getElementById('quest-bar');
  if (bar) bar.style.width = (questDone/4*100)+'%';
  if (questDone >= 4) { const r=document.getElementById('quest-reward'); if(r) r.style.display='block'; }
};

function initQuestList() { questDone = 0; }
```

- [ ] **Step 3: Verify in browser**

Click CD2. Expected:
- 4 tabs: XP Level-Up · Leaderboard · Boss Fight · Quest List
- Leaderboard: "Earn XP →" button increments your score, nudge updates
- Boss Fight: correct answer depletes HP, wrong answer flashes red, new question loads after hit
- Quest List: clicking rows checks them off, progress bar fills, reward appears at 4/4

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd2): add Leaderboard, Boss Fight, Quest List tabs"
```

---

## Task 4: CD3 — Creativity new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD3 in DRIVES**

```js
tabs:[
  {name:'Combo Builder',    render:()=>renderComboBuilder(),    init:()=>initComboBuilder()},
  {name:'Chain Combos',     render:()=>renderChainCombos(),     init:()=>initChainCombos()},
  {name:'Milestone Unlock', render:()=>renderMilestoneUnlock(), init:()=>initMilestoneUnlock()},
  {name:'Dyn. Feedback',    render:()=>renderDynFeedback(),     init:()=>{}},
],
```

- [ ] **Step 2: Add render/init functions**

Find `// ── CD4:` and insert immediately before it:

```js
// ── CD3: Chain Combos ────────────────────────────────────────────
let chainCount = 0;
let chainTimer  = null;
let chainMulti  = 1;

function renderChainCombos() {
  return `
  <div class="demo-title">Chain Combos</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;text-align:center">
    <div style="font-size:.68rem;color:#6b7280;margin-bottom:8px">Tap fast to build your multiplier. Break the chain and it resets.</div>
    <div id="chain-multi" style="font-size:3rem;font-weight:900;color:#8B5CF6;line-height:1;margin-bottom:4px;transition:transform .1s">×1</div>
    <div style="font-size:.65rem;color:#6b7280;margin-bottom:16px">multiplier</div>
    <div id="chain-timer-bar-wrap" style="height:5px;background:#21262d;border-radius:3px;overflow:hidden;margin-bottom:16px">
      <div id="chain-timer-bar" style="width:100%;height:100%;background:#8B5CF6;border-radius:3px;transition:width .05s linear"></div>
    </div>
    <button onclick="chainTap()" style="background:#8B5CF6;color:#fff;border:none;border-radius:10px;padding:12px 28px;font-size:.9rem;font-weight:700;cursor:pointer;transition:transform .1s;width:100%">
      ⚡ Tap
    </button>
    <div id="chain-score" style="font-size:.72rem;color:#8b949e;margin-top:10px">Score: 0</div>
  </div>`;
}

let chainScore = 0;
let chainDeadline = 0;

window.chainTap = function() {
  const now = Date.now();
  if (now < chainDeadline) {
    chainMulti = Math.min(chainMulti + 1, 8);
  } else {
    chainMulti = 1;
  }
  chainDeadline = now + 1000;
  chainScore += chainMulti * 10;

  const multiEl = document.getElementById('chain-multi');
  const scoreEl = document.getElementById('chain-score');
  if (multiEl) { multiEl.textContent = '×'+chainMulti; multiEl.style.transform='scale(1.3)'; setTimeout(()=>multiEl.style.transform='',100); }
  if (scoreEl) scoreEl.textContent = 'Score: '+chainScore;

  if (chainTimer) clearInterval(chainTimer);
  const barEl = document.getElementById('chain-timer-bar');
  const start = Date.now();
  chainTimer = setInterval(() => {
    const elapsed = Date.now() - start;
    const pct = Math.max(0, 100 - (elapsed/1000*100));
    if (barEl) barEl.style.width = pct+'%';
    if (elapsed >= 1000) {
      clearInterval(chainTimer); chainTimer=null; chainMulti=1;
      const m = document.getElementById('chain-multi');
      if (m) m.textContent='×1';
    }
  }, 50);
};

function initChainCombos() { chainCount=0; chainScore=0; chainMulti=1; chainDeadline=0; if(chainTimer){clearInterval(chainTimer);chainTimer=null;} }

// ── CD3: Milestone Unlock ────────────────────────────────────────
let milestoneXP = 0;
const MILESTONE_THRESHOLD = 100;

function renderMilestoneUnlock() {
  return `
  <div class="demo-title">Milestone Unlock</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;justify-content:space-between;font-size:.65rem;color:#6b7280;margin-bottom:4px">
      <span>Progress to unlock</span><span id="ms-xp-label">0 / 100 XP</span>
    </div>
    <div style="height:10px;background:#21262d;border-radius:5px;overflow:hidden;margin-bottom:14px">
      <div id="ms-bar" style="width:0%;height:100%;background:linear-gradient(90deg,#8B5CF6,#EC4899);border-radius:5px;transition:width .4s"></div>
    </div>
    <div id="ms-locked" style="background:#161b22;border:2px dashed #30363d;border-radius:10px;padding:20px;text-align:center;margin-bottom:12px">
      <div style="font-size:1.5rem;margin-bottom:6px">🔒</div>
      <div style="font-size:.75rem;color:#6b7280">Reach 100 XP to reveal the secret technique</div>
    </div>
    <div id="ms-unlocked" style="display:none;background:linear-gradient(135deg,#1a0d3f,#0d2137);border:1px solid #8B5CF6;border-radius:10px;padding:16px;text-align:center;margin-bottom:12px">
      <div style="font-size:1.5rem;margin-bottom:6px">✨</div>
      <div style="font-size:.88rem;font-weight:800;color:#a78bfa;margin-bottom:4px">Oracle Effect</div>
      <div style="font-size:.75rem;color:#8b949e;line-height:1.5">Give users a glimpse of the future to make them feel guided by a higher force — then let events confirm the prediction.</div>
    </div>
    <div style="display:flex;flex-direction:column;gap:6px">
      <div onclick="msEarnXP(30)" style="display:flex;justify-content:space-between;align-items:center;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:8px 12px;cursor:pointer">
        <span style="font-size:.78rem">🧩 Complete a creative challenge</span>
        <span style="font-size:.68rem;font-weight:700;color:#8B5CF6">+30 XP</span>
      </div>
      <div onclick="msEarnXP(50)" style="display:flex;justify-content:space-between;align-items:center;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:8px 12px;cursor:pointer">
        <span style="font-size:.78rem">🎨 Design a new combo</span>
        <span style="font-size:.68rem;font-weight:700;color:#8B5CF6">+50 XP</span>
      </div>
    </div>
  </div>`;
}

window.msEarnXP = function(amount) {
  milestoneXP = Math.min(milestoneXP + amount, MILESTONE_THRESHOLD);
  const bar  = document.getElementById('ms-bar');
  const lbl  = document.getElementById('ms-xp-label');
  if (bar) bar.style.width = (milestoneXP/MILESTONE_THRESHOLD*100)+'%';
  if (lbl) lbl.textContent = milestoneXP+' / '+MILESTONE_THRESHOLD+' XP';
  if (milestoneXP >= MILESTONE_THRESHOLD) {
    const locked   = document.getElementById('ms-locked');
    const unlocked = document.getElementById('ms-unlocked');
    if (locked)   locked.style.display   = 'none';
    if (unlocked) unlocked.style.display = 'block';
  }
};

function initMilestoneUnlock() { milestoneXP = 0; }

// ── CD3: Dynamic Feedback ────────────────────────────────────────
function renderDynFeedback() {
  return `
  <div class="demo-title">Dynamic Feedback</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="font-size:.72rem;color:#6b7280;margin-bottom:14px">How much creative freedom do your users have?</div>
    <input type="range" id="df-slider" min="0" max="100" value="50" oninput="dfUpdate(this.value)"
      style="width:100%;accent-color:#8B5CF6;margin-bottom:14px">
    <div id="df-label" style="font-size:1.1rem;font-weight:900;text-align:center;margin-bottom:6px;transition:color .3s">Balanced</div>
    <div id="df-bar-wrap" style="height:8px;background:#21262d;border-radius:4px;overflow:hidden;margin-bottom:10px">
      <div id="df-bar" style="width:50%;height:100%;border-radius:4px;transition:width .2s,background .3s;background:#8B5CF6"></div>
    </div>
    <div id="df-desc" style="font-size:.78rem;color:#8b949e;text-align:center;line-height:1.5;transition:opacity .2s">
      A mix of guided choices and open expression. Most products live here.
    </div>
  </div>`;
}

window.dfUpdate = function(val) {
  const v = parseInt(val, 10);
  const labelEl = document.getElementById('df-label');
  const barEl   = document.getElementById('df-bar');
  const descEl  = document.getElementById('df-desc');
  const levels = [
    {min:0,  max:20,  label:'Fully Guided',   color:'#3B82F6', desc:'Users follow a fixed path. Creativity drive is low — good for onboarding.'},
    {min:20, max:40,  label:'Structured',      color:'#8B5CF6', desc:'Choices exist but within a defined framework. Feels empowering without overwhelm.'},
    {min:40, max:60,  label:'Balanced',        color:'#8B5CF6', desc:'A mix of guided choices and open expression. Most products live here.'},
    {min:60, max:80,  label:'Open-ended',      color:'#EC4899', desc:'High creative latitude. Users invent their own paths — risky but deeply engaging.'},
    {min:80, max:101, label:'Fully Freeform',  color:'#EF4444', desc:'No guardrails. Creativity drive peaks — but so does cognitive load and drop-off risk.'},
  ];
  const lvl = levels.find(l => v >= l.min && v < l.max) || levels[2];
  if (labelEl) { labelEl.textContent=lvl.label; labelEl.style.color=lvl.color; }
  if (barEl)   { barEl.style.width=v+'%'; barEl.style.background=lvl.color; }
  if (descEl)  descEl.textContent=lvl.desc;
};
```

- [ ] **Step 3: Verify in browser**

Click CD3. Expected:
- 4 tabs: Combo Builder · Chain Combos · Milestone Unlock · Dyn. Feedback
- Chain Combos: rapid taps build ×2, ×3… multiplier; pausing resets to ×1
- Milestone Unlock: earn XP from tasks → lock disappears and secret technique appears at 100 XP
- Dynamic Feedback: dragging slider changes label, colour and description instantly

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd3): add Chain Combos, Milestone Unlock, Dynamic Feedback tabs"
```

---

## Task 5: CD4 — Ownership new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD4 in DRIVES**

```js
tabs:[
  {name:'Passport Stamps', render:()=>renderPassportStamps(),  init:()=>initPassportStamps()},
  {name:'Avatar Builder',  render:()=>renderAvatarBuilder(),   init:()=>{}},
  {name:'Virtual Goods',   render:()=>renderVirtualGoods(),    init:()=>initVirtualGoods()},
  {name:'Build from Scratch', render:()=>renderBuildFromScratch(), init:()=>initBuildFromScratch()},
],
```

- [ ] **Step 2: Add render/init functions**

Find `// ── CD5:` and insert before it:

```js
// ── CD4: Avatar Builder ──────────────────────────────────────────
const AVATAR_OPTIONS = {
  skin:  ['🟡','🟤','⬜'],
  hair:  ['👱','🧑','👩‍🦱','🧔'],
  accessory: ['🕶️','🎩','👑','—'],
};
const AVATAR_KEY = 'octalysis_avatar';

function getAvatarState() {
  try { return JSON.parse(localStorage.getItem(AVATAR_KEY)) || {skin:0,hair:0,accessory:3}; } catch { return {skin:0,hair:0,accessory:3}; }
}
function saveAvatarState(s) { try { localStorage.setItem(AVATAR_KEY, JSON.stringify(s)); } catch {} }

function renderAvatarBuilder() {
  const state = getAvatarState();
  return `
  <div class="demo-title">Avatar Builder</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="text-align:center;font-size:3.5rem;margin:8px 0 16px;line-height:1" id="avatar-preview">
      ${AVATAR_OPTIONS.skin[state.skin]}${AVATAR_OPTIONS.hair[state.hair]}${state.accessory < 3 ? AVATAR_OPTIONS.accessory[state.accessory] : ''}
    </div>
    ${['skin','hair','accessory'].map(cat=>`
    <div style="margin-bottom:12px">
      <div style="font-size:.62rem;text-transform:uppercase;letter-spacing:.6px;color:#6b7280;margin-bottom:6px">${cat}</div>
      <div style="display:flex;gap:6px;flex-wrap:wrap">
        ${AVATAR_OPTIONS[cat].map((opt,i)=>`
        <button onclick="avatarPick('${cat}',${i})" id="av-${cat}-${i}"
          style="font-size:1.2rem;padding:6px 10px;border-radius:8px;border:2px solid ${getAvatarState()[cat]===i?'#10B981':'#30363d'};background:#161b22;cursor:pointer;transition:border-color .15s">
          ${opt}
        </button>`).join('')}
      </div>
    </div>`).join('')}
    <div style="font-size:.62rem;color:#6b7280;text-align:center;margin-top:4px">Changes saved in browser</div>
  </div>`;
}

window.avatarPick = function(cat, idx) {
  const state = getAvatarState();
  state[cat] = idx;
  saveAvatarState(state);
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    const tabBarHtml = tabBar ? tabBar.outerHTML : '';
    demoEl.innerHTML = tabBarHtml + renderAvatarBuilder();
  }
};

// ── CD4: Virtual Goods ───────────────────────────────────────────
const SHOP_ITEMS = [
  {id:'badge-gold',  name:'Gold Badge',     icon:'🏅', price:50,  desc:'Rare'},
  {id:'theme-dark',  name:'Dark Theme',     icon:'🎨', price:80,  desc:'Cosmetic'},
  {id:'boost-xp',    name:'XP Booster',     icon:'⚡', price:120, desc:'Power-up'},
];
let shopCoins = 200;
let shopOwned = new Set();

function renderVirtualGoods() {
  return `
  <div class="demo-title">Virtual Goods</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
      <span style="font-size:.78rem;font-weight:700;color:#e6edf3">Shop</span>
      <span style="font-size:.75rem;font-weight:700;color:#F59E0B">🪙 <span id="shop-coins">${shopCoins}</span> coins</span>
    </div>
    <div style="display:flex;flex-direction:column;gap:8px" id="shop-items">
      ${SHOP_ITEMS.map(item=>`
      <div style="display:flex;align-items:center;gap:10px;background:#161b22;border:1px solid ${shopOwned.has(item.id)?'#10B981':'#30363d'};border-radius:8px;padding:10px 12px">
        <span style="font-size:1.4rem">${item.icon}</span>
        <div style="flex:1">
          <div style="font-size:.78rem;font-weight:700;color:#e6edf3">${item.name}</div>
          <div style="font-size:.62rem;color:#6b7280">${item.desc}</div>
        </div>
        ${shopOwned.has(item.id)
          ? `<span style="font-size:.7rem;font-weight:700;color:#10B981">✓ Owned</span>`
          : `<button onclick="shopBuy('${item.id}')" style="background:#F59E0B;color:#111;border:none;border-radius:6px;padding:5px 12px;font-size:.72rem;font-weight:700;cursor:pointer">${item.price} 🪙</button>`}
      </div>`).join('')}
    </div>
    <div id="shop-inventory" style="margin-top:12px;min-height:24px">
      ${shopOwned.size ? `<div style="font-size:.65rem;color:#6b7280">Inventory: ${[...shopOwned].join(', ')}</div>` : ''}
    </div>
  </div>`;
}

window.shopBuy = function(id) {
  const item = SHOP_ITEMS.find(i=>i.id===id);
  if (!item || shopCoins < item.price || shopOwned.has(id)) return;
  shopCoins -= item.price;
  shopOwned.add(id);
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    const tabBarHtml = tabBar ? tabBar.outerHTML : '';
    demoEl.innerHTML = tabBarHtml + renderVirtualGoods();
  }
};

function initVirtualGoods() { shopCoins=200; shopOwned=new Set(); }

// ── CD4: Build from Scratch ──────────────────────────────────────
const BUILD_BLOCKS = ['🧱','🪟','🚪','🏠','🌳'];
let buildPlaced = [];

function renderBuildFromScratch() {
  return `
  <div class="demo-title">Build from Scratch</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="font-size:.68rem;color:#6b7280;margin-bottom:10px">Click blocks to place them. Build your house!</div>
    <div id="build-canvas" style="min-height:80px;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:10px;display:flex;flex-wrap:wrap;gap:6px;margin-bottom:12px;align-items:center">
      ${buildPlaced.length ? buildPlaced.map(b=>`<span style="font-size:1.6rem">${b}</span>`).join('') : '<span style="font-size:.75rem;color:#6b7280">Your build appears here…</span>'}
    </div>
    <div style="display:flex;gap:8px;justify-content:center;margin-bottom:10px">
      ${BUILD_BLOCKS.map(b=>`<button onclick="buildPlace('${b}')" style="font-size:1.4rem;padding:8px;border-radius:8px;border:1px solid #30363d;background:#161b22;cursor:pointer;transition:transform .1s" onmouseenter="this.style.transform='scale(1.15)'" onmouseleave="this.style.transform=''">${b}</button>`).join('')}
    </div>
    <button onclick="buildReset()" style="background:#21262d;color:#8b949e;border:1px solid #30363d;border-radius:7px;padding:6px 16px;font-size:.72rem;cursor:pointer;width:100%">↺ Reset</button>
    <div id="build-reward" style="display:none;text-align:center;margin-top:10px;padding:10px;background:#064e3b;border:1px solid #059669;border-radius:8px">
      <span style="font-size:.78rem;font-weight:700;color:#6ee7b7">🏠 House built! You created something from nothing.</span>
    </div>
  </div>`;
}

window.buildPlace = function(block) {
  buildPlaced.push(block);
  const canvas = document.getElementById('build-canvas');
  if (canvas) canvas.innerHTML = buildPlaced.map(b=>`<span style="font-size:1.6rem">${b}</span>`).join('');
  if (buildPlaced.includes('🏠')) {
    const reward = document.getElementById('build-reward');
    if (reward) reward.style.display = 'block';
  }
};

window.buildReset = function() {
  buildPlaced = [];
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    const tabBarHtml = tabBar ? tabBar.outerHTML : '';
    demoEl.innerHTML = tabBarHtml + renderBuildFromScratch();
  }
};

function initBuildFromScratch() { buildPlaced = []; }
```

- [ ] **Step 3: Verify in browser**

Click CD4. Expected:
- 4 tabs: Passport Stamps · Avatar Builder · Virtual Goods · Build from Scratch
- Avatar Builder: clicking skin/hair/accessory buttons updates the preview and persists in localStorage
- Virtual Goods: buying deducts coins, button changes to "✓ Owned", can't go negative
- Build from Scratch: clicking blocks adds them to canvas; placing 🏠 reveals reward

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd4): add Avatar Builder, Virtual Goods, Build from Scratch tabs"
```

---

## Task 6: CD5 — Social Influence new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD5 in DRIVES**

```js
tabs:[
  {name:'Live Pulse',    render:()=>renderLivePulse(),    init:()=>initLivePulse()},
  {name:'Trophy Shelf',  render:()=>renderTrophyShelf(),  init:()=>{}},
  {name:'Group Quest',   render:()=>renderGroupQuest(),   init:()=>{}},
  {name:'Social Gifting',render:()=>renderSocialGifting(),init:()=>{}},
],
```

- [ ] **Step 2: Add render/init functions**

Find `// ── CD6:` and insert before it:

```js
// ── CD5: Trophy Shelf ────────────────────────────────────────────
const TROPHIES = [
  {icon:'🥇', name:'First Place',   who:'Maya K.',   when:'2 days ago'},
  {icon:'🏅', name:'Streak Master', who:'You',        when:'Today'},
  {icon:'🎖️', name:'Boss Slayer',   who:'Luca T.',   when:'1 week ago'},
  {icon:'⭐', name:'Top Mentor',    who:'Rin S.',    when:'3 days ago'},
];

function renderTrophyShelf() {
  return `
  <div class="demo-title">Trophy Shelf</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="font-size:.68rem;color:#6b7280;margin-bottom:12px">Hover a trophy to see who earned it</div>
    <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:10px">
      ${TROPHIES.map(t=>`
      <div style="position:relative;background:#161b22;border:1px solid #30363d;border-radius:10px;padding:14px;text-align:center;cursor:default"
        onmouseenter="document.getElementById('trophy-tip-${t.name.replace(/\s/g,'')}').style.display='block'"
        onmouseleave="document.getElementById('trophy-tip-${t.name.replace(/\s/g,'')}').style.display='none'">
        <div style="font-size:2rem;margin-bottom:4px">${t.icon}</div>
        <div style="font-size:.72rem;font-weight:700;color:#e6edf3">${t.name}</div>
        <div id="trophy-tip-${t.name.replace(/\s/g,'')}" style="display:none;position:absolute;bottom:calc(100% + 6px);left:50%;transform:translateX(-50%);background:#21262d;border:1px solid #30363d;border-radius:6px;padding:6px 10px;white-space:nowrap;font-size:.65rem;color:#8b949e;z-index:10">
          ${t.who} · ${t.when}
        </div>
      </div>`).join('')}
    </div>
  </div>`;
}

// ── CD5: Group Quest ─────────────────────────────────────────────
const GROUP_GOAL = 50;
let groupProgress = 31;

function renderGroupQuest() {
  const pct = Math.round(groupProgress/GROUP_GOAL*100);
  return `
  <div class="demo-title">Group Quest</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;text-align:center">
    <div style="font-size:.7rem;color:#6b7280;letter-spacing:.8px;text-transform:uppercase;margin-bottom:6px">Community Goal</div>
    <div style="font-size:1rem;font-weight:800;color:#e6edf3;margin-bottom:14px">Teach 50 people Octalysis this month</div>
    <div style="display:flex;justify-content:space-between;font-size:.65rem;color:#6b7280;margin-bottom:4px">
      <span><span id="gq-count">${groupProgress}</span> / ${GROUP_GOAL} people reached</span>
      <span id="gq-pct">${pct}%</span>
    </div>
    <div style="height:12px;background:#21262d;border-radius:6px;overflow:hidden;margin-bottom:16px">
      <div id="gq-bar" style="width:${pct}%;height:100%;background:linear-gradient(90deg,#EC4899,#8B5CF6);border-radius:6px;transition:width .5s"></div>
    </div>
    <div style="font-size:.72rem;color:#8b949e;margin-bottom:14px">31 contributors so far · Be the next</div>
    <button onclick="gqContribute()" id="gq-btn"
      style="background:#EC4899;color:#fff;border:none;border-radius:8px;padding:8px 24px;font-size:.82rem;font-weight:700;cursor:pointer">
      + I Taught Someone
    </button>
  </div>`;
}

window.gqContribute = function() {
  if (groupProgress >= GROUP_GOAL) return;
  groupProgress++;
  const pct = Math.round(groupProgress/GROUP_GOAL*100);
  const cEl = document.getElementById('gq-count');
  const pEl = document.getElementById('gq-pct');
  const bar = document.getElementById('gq-bar');
  if (cEl) cEl.textContent = groupProgress;
  if (pEl) pEl.textContent = pct+'%';
  if (bar) bar.style.width = pct+'%';
  if (groupProgress >= GROUP_GOAL) {
    const btn = document.getElementById('gq-btn');
    if (btn) { btn.textContent='🎉 Goal Reached!'; btn.disabled=true; btn.style.background='#10B981'; }
  }
};

// ── CD5: Social Gifting ──────────────────────────────────────────
const GIFT_TEAMMATES = ['Maya K. 🎨','Luca T. 💻','Rin S. 📚'];
let giftSelected = null;

function renderSocialGifting() {
  return `
  <div class="demo-title">Social Gifting</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="font-size:.72rem;color:#6b7280;margin-bottom:10px">Send a boost to a teammate</div>
    <div style="display:flex;flex-direction:column;gap:6px;margin-bottom:12px" id="gift-teammates">
      ${GIFT_TEAMMATES.map((t,i)=>`
      <div onclick="giftSelect(${i})" id="gift-tm-${i}"
        style="display:flex;align-items:center;justify-content:space-between;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:9px 12px;cursor:pointer;transition:border-color .15s">
        <span style="font-size:.78rem">${t}</span>
        <span id="gift-tm-xp-${i}" style="font-size:.65rem;color:#6b7280">— XP</span>
      </div>`).join('')}
    </div>
    <button onclick="giftSend()" id="gift-btn"
      style="background:#EC4899;color:#fff;border:none;border-radius:8px;padding:8px;font-size:.82rem;font-weight:700;cursor:pointer;width:100%;transition:opacity .2s;${giftSelected===null?'opacity:.5':''}">
      🎁 Send +50 XP Boost
    </button>
    <div id="gift-msg" style="display:none;font-size:.72rem;color:#10B981;text-align:center;margin-top:8px"></div>
  </div>`;
}

window.giftSelect = function(i) {
  giftSelected = i;
  GIFT_TEAMMATES.forEach((_,j) => {
    const el = document.getElementById(`gift-tm-${j}`);
    if (el) el.style.borderColor = j===i ? '#EC4899' : '#30363d';
  });
  const btn = document.getElementById('gift-btn');
  if (btn) btn.style.opacity = '1';
};

window.giftSend = function() {
  if (giftSelected === null) return;
  const xpEl = document.getElementById(`gift-tm-xp-${giftSelected}`);
  if (xpEl) { xpEl.textContent='+50 XP'; xpEl.style.color='#EC4899'; }
  const msg = document.getElementById('gift-msg');
  if (msg) { msg.textContent=`🎁 Boost sent to ${GIFT_TEAMMATES[giftSelected].split(' ')[0]}!`; msg.style.display='block'; }
  const btn = document.getElementById('gift-btn');
  if (btn) { btn.disabled=true; btn.style.opacity='.5'; btn.textContent='✓ Sent'; }
};
```

- [ ] **Step 3: Verify in browser**

Click CD5. Expected:
- 4 tabs: Live Pulse · Trophy Shelf · Group Quest · Social Gifting
- Trophy Shelf: hovering a trophy shows tooltip with who earned it and when
- Group Quest: clicking the button increments the counter and progress bar; at 50 the button becomes "Goal Reached!"
- Social Gifting: select a teammate → Send button activates → sending shows XP on their row and a confirmation

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd5): add Trophy Shelf, Group Quest, Social Gifting tabs"
```

---

## Task 7: CD6 — Scarcity new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD6 in DRIVES**

```js
tabs:[
  {name:'Event Ticket',     render:()=>renderEventTicket(),        init:()=>initEventTicket()},
  {name:'Appointment',      render:()=>renderAppointmentDynamics(), init:()=>initAppointmentDynamics()},
  {name:'Last Mile Drive',  render:()=>renderLastMileDrive(),       init:()=>{}},
  {name:'Prize Pacing',     render:()=>renderPrizePacing(),         init:()=>{}},
],
```

- [ ] **Step 2: Add render/init functions**

Find `// ── CD7:` and insert before it:

```js
// ── CD6: Appointment Dynamics ────────────────────────────────────
// apptInterval declared in Task 1
let apptSeconds  = 8 * 60 + 42;
let apptClaimed  = false;

function renderAppointmentDynamics() {
  const days = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
  const doneDays = [0,1];
  const todayIdx = 2;
  const dots = days.map((d,i) => {
    let bg, border, color;
    if (apptClaimed && i===todayIdx) { bg='#10B981'; border='#059669'; color='#6ee7b7'; }
    else if (doneDays.includes(i)) { bg='#F97316'; border='#ea580c'; color='#fff'; }
    else if (i===todayIdx) { bg='transparent'; border='#F97316'; color='#F97316'; }
    else { bg='transparent'; border='#30363d'; color='#6b7280'; }
    return `<div style="display:flex;flex-direction:column;align-items:center;gap:3px">
      <div style="width:28px;height:28px;border-radius:50%;background:${bg};border:2px solid ${border};display:flex;align-items:center;justify-content:center;font-size:.6rem;color:${color};font-weight:700">
        ${(apptClaimed&&i===todayIdx)||doneDays.includes(i)?'✓':d[0]}
      </div>
      <span style="font-size:.52rem;color:#6b7280">${d}</span>
    </div>`;
  }).join('');

  return `
  <div class="demo-title">Appointment Dynamics</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;text-align:center">
    <div style="font-size:.7rem;color:#6b7280;text-transform:uppercase;letter-spacing:.8px;margin-bottom:6px">Daily Reward</div>
    <div style="font-size:.88rem;font-weight:800;color:#e6edf3;margin-bottom:12px">Come back every day to keep your streak</div>
    <div style="display:flex;justify-content:center;gap:6px;margin-bottom:16px">${dots}</div>
    ${apptClaimed
      ? `<div style="background:#064e3b;border:1px solid #059669;border-radius:8px;padding:10px;font-size:.78rem;font-weight:700;color:#6ee7b7">✓ Reward collected! Come back tomorrow.</div>`
      : `<div style="margin-bottom:10px">
          <div style="font-size:.62rem;color:#6b7280">Next reward expires in</div>
          <div id="appt-countdown" style="font-size:1.8rem;font-weight:900;color:#F97316;letter-spacing:-.02em">${formatCountdown(apptSeconds)}</div>
        </div>
        <button onclick="apptClaim()" style="background:#F97316;color:#fff;border:none;border-radius:8px;padding:9px 24px;font-size:.82rem;font-weight:700;cursor:pointer">
          Collect Today's Reward
        </button>`}
  </div>`;
}

window.apptClaim = function() {
  apptClaimed = true;
  if (apptInterval) { clearInterval(apptInterval); apptInterval=null; }
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    demoEl.innerHTML = (tabBar?tabBar.outerHTML:'') + renderAppointmentDynamics();
  }
};

function initAppointmentDynamics() {
  apptClaimed = false; apptSeconds = 8*60+42;
  if (apptInterval) clearInterval(apptInterval);
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    demoEl.innerHTML = (tabBar?tabBar.outerHTML:'') + renderAppointmentDynamics();
  }
  apptInterval = setInterval(() => {
    const el = document.getElementById('appt-countdown');
    if (!el) { clearInterval(apptInterval); apptInterval=null; return; }
    apptSeconds = Math.max(0, apptSeconds-1);
    el.textContent = formatCountdown(apptSeconds);
  }, 1000);
}

// ── CD6: Last Mile Drive ─────────────────────────────────────────
function renderLastMileDrive() {
  return `
  <div class="demo-title">Last Mile Drive</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="font-size:.7rem;color:#6b7280;text-transform:uppercase;letter-spacing:.8px;margin-bottom:4px">Level Progress</div>
    <div style="font-size:.88rem;font-weight:800;color:#e6edf3;margin-bottom:12px">You're almost there — just a little more!</div>
    <div style="display:flex;justify-content:space-between;font-size:.65rem;color:#6b7280;margin-bottom:4px">
      <span>Level 5 · Expert</span><span id="lm-label">850 / 1000 XP</span>
    </div>
    <div style="height:14px;background:#21262d;border-radius:7px;overflow:hidden;margin-bottom:6px">
      <div id="lm-bar" style="width:85%;height:100%;background:linear-gradient(90deg,#F97316,#F59E0B);border-radius:7px;transition:width .4s;box-shadow:0 0 10px rgba(249,115,22,.4)"></div>
    </div>
    <div style="font-size:.65rem;color:#F97316;text-align:right;margin-bottom:16px">Only 150 XP to go!</div>
    <div style="display:flex;flex-direction:column;gap:6px">
      ${[['🎯 Complete one more task',50],['⚡ Use a daily boost',30],['🤝 Help a teammate',70]].map(([task,xp])=>`
      <div onclick="lmEarnXP(${xp},this)" style="display:flex;justify-content:space-between;align-items:center;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:8px 12px;cursor:pointer">
        <span style="font-size:.78rem">${task}</span>
        <span style="font-size:.68rem;font-weight:700;color:#F97316">+${xp} XP</span>
      </div>`).join('')}
    </div>
    <div id="lm-reward" style="display:none;background:#1a0e04;border:1px solid #F97316;border-radius:8px;padding:12px;text-align:center;margin-top:10px">
      <div style="font-size:1.2rem">🎉</div>
      <div style="font-size:.78rem;font-weight:700;color:#F59E0B">Level Up! Welcome to Expert.</div>
    </div>
  </div>`;
}

let lmXP = 850;

window.lmEarnXP = function(amount, el) {
  if (el.dataset.done) return;
  el.dataset.done='1'; el.style.opacity='.4'; el.style.pointerEvents='none';
  el.querySelector('span:last-child').textContent='✓';
  lmXP = Math.min(lmXP+amount, 1000);
  const bar = document.getElementById('lm-bar');
  const lbl = document.getElementById('lm-label');
  if (bar) bar.style.width=(lmXP/1000*100)+'%';
  if (lbl) lbl.textContent=lmXP+' / 1000 XP';
  if (lmXP>=1000) { const r=document.getElementById('lm-reward'); if(r) r.style.display='block'; }
};

// ── CD6: Prize Pacing ────────────────────────────────────────────
const PRIZE_TIERS = [
  {xp:0,   icon:'🥉', name:'Bronze',   desc:'Access to basic templates',         color:'#cd7f32'},
  {xp:100, icon:'🥈', name:'Silver',   desc:'Unlock the Combo Builder pro mode', color:'#9ca3af'},
  {xp:250, icon:'🥇', name:'Gold',     desc:'Early access to new techniques',    color:'#F59E0B'},
  {xp:500, icon:'💎', name:'Diamond',  desc:'Personal feedback from the team',   color:'#06B6D4'},
];
let prizeXP = 0;

function renderPrizePacing() {
  return `
  <div class="demo-title">Prize Pacing</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="font-size:.68rem;color:#6b7280;margin-bottom:12px">Each milestone reveals the next reward</div>
    <div style="display:flex;flex-direction:column;gap:8px;margin-bottom:14px">
      ${PRIZE_TIERS.map(t=>{
        const unlocked = prizeXP>=t.xp;
        return `<div style="display:flex;align-items:center;gap:10px;background:#161b22;border:1px solid ${unlocked?t.color:'#30363d'};border-radius:8px;padding:10px 12px;opacity:${unlocked?'1':'.5'}">
          <span style="font-size:1.3rem">${unlocked?t.icon:'🔒'}</span>
          <div style="flex:1">
            <div style="font-size:.75rem;font-weight:700;color:${unlocked?t.color:'#6b7280'}">${t.name}</div>
            <div style="font-size:.65rem;color:#6b7280">${unlocked?t.desc:'Reach '+t.xp+' XP to reveal'}</div>
          </div>
          ${unlocked?`<span style="font-size:.62rem;font-weight:700;color:${t.color}">Unlocked</span>`:''}
        </div>`;
      }).join('')}
    </div>
    <div style="display:flex;justify-content:space-between;font-size:.65rem;color:#6b7280;margin-bottom:4px">
      <span>Your XP</span><span id="pp-label">${prizeXP} XP</span>
    </div>
    <div style="height:6px;background:#21262d;border-radius:3px;overflow:hidden;margin-bottom:12px">
      <div id="pp-bar" style="width:${Math.min(prizeXP/500*100,100)}%;height:100%;background:linear-gradient(90deg,#F97316,#F59E0B);border-radius:3px;transition:width .4s"></div>
    </div>
    <button onclick="ppEarnXP()" style="background:#F97316;color:#fff;border:none;border-radius:8px;padding:8px;font-size:.82rem;font-weight:700;cursor:pointer;width:100%">
      + Earn 75 XP
    </button>
  </div>`;
}

window.ppEarnXP = function() {
  prizeXP = Math.min(prizeXP+75, 500);
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    demoEl.innerHTML = (tabBar?tabBar.outerHTML:'') + renderPrizePacing();
  }
};
```

- [ ] **Step 3: Verify in browser**

Click CD6. Expected:
- 4 tabs: Event Ticket · Appointment · Last Mile Drive · Prize Pacing
- Appointment: countdown ticks down; clicking "Collect" marks today as done and stops timer; switching away and back resets
- Last Mile Drive: tasks fill the near-complete bar; at 1000 XP "Level Up" reward appears
- Prize Pacing: clicking "Earn 75 XP" progressively unlocks tiers with their rewards revealed

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd6): add Appointment Dynamics, Last Mile Drive, Prize Pacing tabs"
```

---

## Task 8: CD7 — Curiosity new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD7 in DRIVES**

```js
tabs:[
  {name:'Scratch Card',   render:()=>renderScratchCard(),   init:()=>initScratchCard()},
  {name:'Easter Egg',     render:()=>renderEasterEgg(),     init:()=>{}},
  {name:'Glowing Choice', render:()=>renderGlowingChoice(), init:()=>initGlowingChoice()},
  {name:'Rolling Rewards',render:()=>renderRollingRewards(),init:()=>initRollingRewards()},
],
```

- [ ] **Step 2: Add render/init functions**

Find `// ── CD8:` and insert before it:

```js
// ── CD7: Easter Egg ──────────────────────────────────────────────
let eggFound = false;

function renderEasterEgg() {
  return `
  <div class="demo-title">Easter Egg</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;min-height:160px;position:relative">
    <div style="font-size:.75rem;color:#8b949e;margin-bottom:8px">Explore the interface — something is hidden somewhere on this panel…</div>
    <div style="height:60px;background:#161b22;border-radius:8px;border:1px dashed #30363d;display:flex;align-items:center;justify-content:center">
      <span style="font-size:.75rem;color:#6b7280">Nothing here… or is there?</span>
    </div>
    <div id="egg-hidden" onclick="findEgg()" style="position:absolute;bottom:14px;right:16px;font-size:1.1rem;opacity:.12;cursor:pointer;transition:opacity .2s;user-select:none"
      onmouseenter="this.style.opacity='.3'" onmouseleave="this.style.opacity='.12'">🥚</div>
    <div id="egg-reveal" style="display:none;position:absolute;inset:0;background:linear-gradient(135deg,#0a1f24,#0d1117);border-radius:12px;display:none;align-items:center;justify-content:center;flex-direction:column;gap:8px;border:1px solid #06B6D4">
      <div style="font-size:2rem">🎉</div>
      <div style="font-size:.88rem;font-weight:800;color:#06B6D4">You found it!</div>
      <div style="font-size:.72rem;color:#8b949e;text-align:center;max-width:180px">Easter Eggs reward curiosity and exploration — turning users into detectives.</div>
    </div>
  </div>`;
}

window.findEgg = function() {
  if (eggFound) return;
  eggFound = true;
  const reveal = document.getElementById('egg-reveal');
  if (reveal) { reveal.style.display='flex'; }
};

function initEasterEgg() { eggFound = false; }

// ── CD7: Glowing Choice ──────────────────────────────────────────
let glowPicked = false;

function renderGlowingChoice() {
  const glowIdx = 1;
  return `
  <div class="demo-title">Glowing Choice</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="font-size:.72rem;color:#6b7280;margin-bottom:14px">One option is calling to you… choose wisely.</div>
    <div style="display:flex;flex-direction:column;gap:8px" id="glow-options">
      ${['🔵 Standard Path','✨ Recommended Path','🔴 Challenge Path'].map((opt,i)=>`
      <div onclick="glowPick(${i})" data-glow="${i===glowIdx?'true':'false'}" id="glow-opt-${i}"
        style="padding:12px 16px;border-radius:10px;border:${i===glowIdx?'2px solid #06B6D4':'1px solid #30363d'};background:${i===glowIdx?'rgba(6,182,212,.08)':'#161b22'};cursor:pointer;font-size:.8rem;font-weight:${i===glowIdx?'700':'400'};color:${i===glowIdx?'#06B6D4':'#8b949e'};transition:all .2s;${i===glowIdx?'box-shadow:0 0 16px rgba(6,182,212,.25)':''}">
        ${opt}${i===glowIdx?' 🌟':''}
      </div>`).join('')}
    </div>
    <div id="glow-result" style="display:none;margin-top:12px;padding:10px;border-radius:8px;font-size:.75rem;text-align:center"></div>
  </div>`;
}

window.glowPick = function(i) {
  if (glowPicked) return;
  glowPicked = true;
  document.querySelectorAll('[id^="glow-opt-"]').forEach(el=>{ el.style.pointerEvents='none'; });
  const result = document.getElementById('glow-result');
  if (!result) return;
  result.style.display = 'block';
  if (i===1) {
    result.style.background='rgba(6,182,212,.1)'; result.style.border='1px solid #06B6D4';
    result.style.color='#06B6D4'; result.textContent='✨ +50 bonus XP for following the glow!';
  } else {
    result.style.background='#161b22'; result.style.border='1px solid #30363d';
    result.style.color='#8b949e'; result.textContent='You chose your own path — the glow had a bonus waiting.';
  }
};

function initGlowingChoice() { glowPicked = false; }

// ── CD7: Rolling Rewards ─────────────────────────────────────────
let rollInterval = null;
let rollValue    = 0;
const ROLL_MAX   = 100;

function renderRollingRewards() {
  return `
  <div class="demo-title">Rolling Rewards</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d;text-align:center">
    <div style="font-size:.7rem;color:#6b7280;text-transform:uppercase;letter-spacing:.8px;margin-bottom:8px">Reward Meter</div>
    <div style="font-size:2.8rem;font-weight:900;color:#06B6D4;margin-bottom:4px;letter-spacing:-.03em" id="roll-pct">0%</div>
    <div style="height:16px;background:#21262d;border-radius:8px;overflow:hidden;margin:10px 0 6px">
      <div id="roll-bar" style="width:0%;height:100%;background:linear-gradient(90deg,#06B6D4,#8B5CF6);border-radius:8px;transition:width .3s"></div>
    </div>
    <div style="font-size:.65rem;color:#6b7280;margin-bottom:16px">Accumulates passively — pays out at 100%</div>
    <div id="roll-payout" style="display:none;background:linear-gradient(135deg,#0a1f24,#0d1117);border:1px solid #06B6D4;border-radius:10px;padding:12px;margin-bottom:12px">
      <div style="font-size:1.4rem">🎰</div>
      <div style="font-size:.88rem;font-weight:800;color:#06B6D4;margin-top:4px" id="roll-prize"></div>
    </div>
    <button onclick="rollClaim()" id="roll-claim-btn" style="background:#06B6D4;color:#111;border:none;border-radius:8px;padding:8px 20px;font-size:.82rem;font-weight:700;cursor:pointer;display:none">
      🎰 Claim Reward
    </button>
  </div>`;
}

const ROLL_PRIZES = ['🏅 Rare Badge','⚡ 2× XP for 1 hour','🎨 Exclusive theme','🌟 +200 bonus XP'];

window.rollClaim = function() {
  const prize = ROLL_PRIZES[Math.floor(Math.random()*ROLL_PRIZES.length)];
  const prizeEl = document.getElementById('roll-prize');
  const payout  = document.getElementById('roll-payout');
  const btn     = document.getElementById('roll-claim-btn');
  if (prizeEl) prizeEl.textContent = prize;
  if (payout)  payout.style.display  = 'block';
  if (btn)     btn.style.display     = 'none';
  rollValue = 0;
  const bar = document.getElementById('roll-bar');
  const pct = document.getElementById('roll-pct');
  if (bar) bar.style.width = '0%';
  if (pct) pct.textContent = '0%';
  if (rollInterval) clearInterval(rollInterval);
  rollInterval = setInterval(tickRoll, 300);
};

function tickRoll() {
  rollValue = Math.min(rollValue + 2, ROLL_MAX);
  const bar = document.getElementById('roll-bar');
  const pct = document.getElementById('roll-pct');
  if (!bar) { clearInterval(rollInterval); rollInterval=null; return; }
  bar.style.width = rollValue + '%';
  if (pct) pct.textContent = rollValue + '%';
  if (rollValue >= ROLL_MAX) {
    clearInterval(rollInterval); rollInterval=null;
    const btn    = document.getElementById('roll-claim-btn');
    const payout = document.getElementById('roll-payout');
    if (btn)    btn.style.display    = 'block';
    if (payout) payout.style.display = 'none';
  }
}

function initRollingRewards() {
  rollValue=0;
  if(rollInterval){clearInterval(rollInterval);rollInterval=null;}
  rollInterval = setInterval(tickRoll, 300);
}
```

- [ ] **Step 3: Verify in browser**

Click CD7. Expected:
- 4 tabs: Scratch Card · Easter Egg · Glowing Choice · Rolling Rewards
- Easter Egg: the 🥚 is barely visible in the bottom-right corner; clicking it reveals the full-panel surprise
- Glowing Choice: picking the glowing option gives a bonus; any other shows the "you missed it" message
- Rolling Rewards: meter fills passively; at 100% a "Claim" button appears; claiming pays a random prize and resets

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd7): add Easter Egg, Glowing Choice, Rolling Rewards tabs"
```

---

## Task 9: CD8 — Loss Avoidance new tabs

**Files:**
- Modify: `playground.html`

- [ ] **Step 1: Add tabs to CD8 in DRIVES**

```js
tabs:[
  {name:'Streak Tracker', render:()=>renderStreakTracker(),  init:()=>initStreakTracker()},
  {name:'Progress Loss',  render:()=>renderProgressLoss(),   init:()=>initProgressLoss()},
  {name:'FOMO Punch',     render:()=>renderFOMOPunch(),      init:()=>initFOMOPunch()},
  {name:'Sunk Cost',      render:()=>renderSunkCost(),       init:()=>{}},
],
```

- [ ] **Step 2: Add render/init functions**

Find the closing `</script>` tag and insert before it:

```js
// ── CD8: Progress Loss ───────────────────────────────────────────
let lossInterval = null;
let lossXP       = 780;
let lossDraining = true;

function renderProgressLoss() {
  return `
  <div class="demo-title">Progress Loss</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="font-size:.7rem;color:#6b7280;text-transform:uppercase;letter-spacing:.8px;margin-bottom:4px">Your Progress</div>
    <div style="font-size:.88rem;font-weight:800;color:#e6edf3;margin-bottom:12px">Your XP is draining — act now!</div>
    <div style="display:flex;justify-content:space-between;font-size:.65rem;margin-bottom:4px">
      <span style="color:#6b7280">Level 4</span>
      <span id="loss-label" style="color:#EF4444;font-weight:700">${lossXP} / 1000 XP</span>
    </div>
    <div style="height:14px;background:#21262d;border-radius:7px;overflow:hidden;margin-bottom:6px">
      <div id="loss-bar" style="width:${lossXP/10}%;height:100%;background:linear-gradient(90deg,#EF4444,#F97316);border-radius:7px;transition:width .5s;box-shadow:0 0 10px rgba(239,68,68,.3)"></div>
    </div>
    <div id="loss-warning" style="font-size:.65rem;color:#EF4444;margin-bottom:16px">⚠️ Losing 1 XP/sec — complete a task to stop the drain</div>
    <div style="display:flex;flex-direction:column;gap:6px">
      ${[['📝 Review your notes',30],['🎯 Answer a quiz',50]].map(([t,xp])=>`
      <div onclick="lossAct(${xp},this)" style="display:flex;justify-content:space-between;align-items:center;background:#2d0a0a;border:1px solid #7f1d1d;border-radius:8px;padding:9px 12px;cursor:pointer;transition:opacity .2s">
        <span style="font-size:.78rem;color:#fca5a5">${t}</span>
        <span style="font-size:.68rem;font-weight:700;color:#EF4444">+${xp} XP &amp; stop drain</span>
      </div>`).join('')}
    </div>
  </div>`;
}

window.lossAct = function(xp, el) {
  if (el.dataset.done) return;
  el.dataset.done='1'; el.style.opacity='.4'; el.style.pointerEvents='none';
  lossDraining = false;
  if (lossInterval) { clearInterval(lossInterval); lossInterval=null; }
  lossXP = Math.min(lossXP+xp, 1000);
  const bar = document.getElementById('loss-bar');
  const lbl = document.getElementById('loss-label');
  const warn= document.getElementById('loss-warning');
  if (bar) bar.style.width=(lossXP/10)+'%';
  if (lbl) { lbl.textContent=lossXP+' / 1000 XP'; lbl.style.color='#10B981'; }
  if (warn) { warn.textContent='✓ Drain stopped! Progress secured.'; warn.style.color='#10B981'; }
};

function initProgressLoss() {
  lossXP=780; lossDraining=true;
  if(lossInterval){clearInterval(lossInterval);lossInterval=null;}
  const demoEl = document.getElementById('demo-content');
  if (demoEl) {
    const tabBar = demoEl.querySelector('.tab-bar');
    demoEl.innerHTML = (tabBar?tabBar.outerHTML:'') + renderProgressLoss();
  }
  lossInterval = setInterval(()=>{
    if (!lossDraining) return;
    const bar = document.getElementById('loss-bar');
    const lbl = document.getElementById('loss-label');
    if (!bar) { clearInterval(lossInterval); lossInterval=null; return; }
    lossXP = Math.max(0, lossXP-1);
    bar.style.width=(lossXP/10)+'%';
    if (lbl) lbl.textContent=lossXP+' / 1000 XP';
  }, 1000);
}

// ── CD8: FOMO Punch ──────────────────────────────────────────────
const FOMO_EVENTS = [
  {flag:'🇧🇷', msg:'<strong>Carlos</strong> just unlocked Expert tier'},
  {flag:'🇯🇵', msg:'<strong>Hana</strong> claimed the last Workshop seat'},
  {flag:'🇩🇪', msg:'<strong>Lukas</strong> earned the Boss Slayer trophy'},
  {flag:'🇰🇷', msg:'<strong>Ji-woo</strong> hit a 30-day streak'},
  {flag:'🇫🇷', msg:'<strong>Claire</strong> reached #1 on the Leaderboard'},
  {flag:'🇮🇳', msg:'<strong>Aryan</strong> completed all 8 Core Drive demos'},
];
let fomoInterval = null;
let fomoCount    = 0;

function renderFOMOPunch() {
  return `
  <div class="demo-title">FOMO Punch</div>
  <div style="background:#0d1117;border-radius:12px;padding:16px;border:1px solid #21262d">
    <div style="display:flex;align-items:center;gap:8px;margin-bottom:10px">
      <div style="width:8px;height:8px;border-radius:50%;background:#EF4444;box-shadow:0 0 6px #EF4444;animation:pulse-dot 1.2s infinite"></div>
      <span style="font-size:.72rem;font-weight:700;color:#EF4444">Live Activity</span>
    </div>
    <div id="fomo-feed" style="display:flex;flex-direction:column;gap:6px;max-height:190px;overflow:hidden"></div>
    <div id="fomo-counter" style="margin-top:10px;background:#2d0a0a;border:1px solid #7f1d1d;border-radius:8px;padding:8px 12px;text-align:center;font-size:.72rem;color:#f87171;font-weight:700">
      ⏰ <span id="fomo-count">0</span> people acted while you read this
    </div>
  </div>`;
}

function fomoAddMsg() {
  const feed = document.getElementById('fomo-feed');
  if (!feed) { clearInterval(fomoInterval); fomoInterval=null; return; }
  const ev = FOMO_EVENTS[Math.floor(Math.random()*FOMO_EVENTS.length)];
  const row = document.createElement('div');
  row.style.cssText='display:flex;align-items:center;gap:8px;background:#2d0a0a;border:1px solid #7f1d1d;border-radius:7px;padding:8px 10px;opacity:0;transition:opacity .3s';
  row.innerHTML=`<span style="flex-shrink:0;font-size:1rem">${ev.flag}</span><span style="font-size:.75rem;color:#fca5a5;flex:1">${ev.msg}</span><span style="font-size:.62rem;color:#6b7280;flex-shrink:0">just now</span>`;
  feed.insertBefore(row, feed.firstChild);
  requestAnimationFrame(()=>row.style.opacity='1');
  while(feed.children.length>4) feed.removeChild(feed.lastChild);
  fomoCount++;
  const cEl = document.getElementById('fomo-count');
  if (cEl) cEl.textContent = fomoCount;
}

function initFOMOPunch() {
  fomoCount=0;
  if(fomoInterval){clearInterval(fomoInterval);fomoInterval=null;}
  fomoInterval=setInterval(fomoAddMsg,1800);
  fomoAddMsg();
}

// ── CD8: Sunk Cost Prison ────────────────────────────────────────
function renderSunkCost() {
  return `
  <div class="demo-title">Sunk Cost Prison</div>
  <div style="background:#0d1117;border-radius:12px;padding:20px;border:1px solid #21262d">
    <div style="text-align:center;font-size:.78rem;color:#8b949e;margin-bottom:14px">Here's everything you'd lose if you quit today:</div>
    <div style="display:flex;flex-direction:column;gap:8px;margin-bottom:16px">
      ${[
        ['⏱', '4 hours', 'of learning invested'],
        ['🏅', '3 badges', 'earned over 2 weeks'],
        ['🔥', '12-day streak', 'would reset to zero'],
        ['⭐', 'Level 5', 'drops back to Level 3'],
        ['🏆', '#2 on Leaderboard', 'gone by tomorrow'],
      ].map(([icon,stat,desc])=>`
      <div style="display:flex;align-items:center;gap:10px;background:#161b22;border:1px solid #30363d;border-radius:8px;padding:9px 12px">
        <span style="font-size:1.2rem">${icon}</span>
        <div>
          <div style="font-size:.8rem;font-weight:700;color:#EF4444">${stat}</div>
          <div style="font-size:.65rem;color:#6b7280">${desc}</div>
        </div>
      </div>`).join('')}
    </div>
    <button onclick="sunkQuit()" id="sunk-quit-btn" style="width:100%;background:#21262d;color:#6b7280;border:1px solid #30363d;border-radius:8px;padding:9px;font-size:.8rem;cursor:pointer">
      Still want to quit?
    </button>
    <div id="sunk-msg" style="display:none;margin-top:10px;padding:10px;background:#2d0a0a;border:1px solid #7f1d1d;border-radius:8px;text-align:center;font-size:.75rem;color:#fca5a5">
      Are you sure? You've come so far… most people stay.
    </div>
  </div>`;
}

window.sunkQuit = function() {
  const msg = document.getElementById('sunk-msg');
  const btn = document.getElementById('sunk-quit-btn');
  if (msg) msg.style.display='block';
  if (btn) { btn.textContent="I'm staying 💪"; btn.style.background='#10B981'; btn.style.color='#fff'; btn.style.borderColor='#059669'; btn.onclick=null; }
};
```

- [ ] **Step 3: Verify in browser**

Click CD8. Expected:
- 4 tabs: Streak Tracker · Progress Loss · FOMO Punch · Sunk Cost
- Progress Loss: XP bar drains 1/sec; completing a task stops the drain and turns the label green
- FOMO Punch: live events appear every 1.8s, counter increments
- Sunk Cost: "Still want to quit?" button reveals warning; second click changes to "I'm staying 💪"

- [ ] **Step 4: Commit**

```bash
git add playground.html
git commit -m "feat(cd8): add Progress Loss, FOMO Punch, Sunk Cost tabs"
```

---

## Done

After all 9 tasks, the playground has:
- 1 tab bar per drive (drive-coloured underline)
- 32 total technique demos (8 existing + 24 new)
- All demos independent, reset when switching drives
- No layout changes to the 3-panel hub or octagon
