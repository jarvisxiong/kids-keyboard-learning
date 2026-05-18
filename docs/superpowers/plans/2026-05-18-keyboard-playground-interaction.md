# Keyboard Playground Interaction Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rework the single-page kids learning tool into a child-friendly keyboard playground with large controls, immediate feedback, and lightweight rewards.

**Architecture:** Keep the project as a single static `index.html`, matching the existing structure. Reorganize the DOM into a header, central stage, control dock, and recent-discovery strip while preserving the current data, audio, speech, particle, pinyin, and quiz logic.

**Tech Stack:** Plain HTML, CSS, and JavaScript; Canvas 2D particles; Web Audio; Web Speech API; Chrome for verification.

---

## File Structure

- Modify: `index.html`
  - Replace the current bottom mode/effect bars with a playground shell.
  - Add reward state rendering for stars, streak, and recent discoveries.
  - Preserve existing learning data and media/effect functions.
  - Update input routing for activity buttons, stage surprise clicks, pinyin discovery, and challenge flow.

No new build system, framework, or external dependency is required.

## Task 1: Playground Shell And Responsive Layout

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add the new structural DOM**

Replace the body UI block from `<!-- Free mode -->` through `<!-- Mode bar -->` with:

```html
<main id="app-shell">
  <header id="top-bar">
    <div>
      <h1>键盘游乐场</h1>
      <p id="friendly-prompt">按一个键，发现一个小惊喜</p>
    </div>
    <div id="reward-panel" aria-live="polite">
      <div class="reward-pill"><span>⭐</span><strong id="stars-count">0</strong></div>
      <div class="reward-pill"><span>连击</span><strong id="streak-count">0</strong></div>
    </div>
  </header>

  <section id="play-area" aria-live="polite">
    <div id="hint">按键盘，或点这里随机发现 ✨<br><small>Press any key or tap for surprise</small></div>

    <div id="display">
      <div id="key-letter"></div>
      <div id="emoji-box"></div>
      <div id="num-row" style="display:none">
        <div id="num-arabic"></div>
        <div id="num-chinese"></div>
        <div id="svg-digit-wrap"></div>
      </div>
      <div id="count-row"></div>
      <div id="chinese-word"></div>
      <div id="pinyin"></div>
      <div id="english-word"></div>
    </div>

    <div id="pinyin-mode">
      <div id="pinyin-hint">拼一拼 📖<br><small>输入拼音，Backspace 删除</small></div>
      <div id="pinyin-buf-display">_</div>
      <div id="pinyin-result">
        <div id="pinyin-zi"></div>
        <div id="pinyin-emoji"></div>
        <div id="pinyin-py"></div>
        <div id="pinyin-tip">继续输入下一个字</div>
      </div>
    </div>

    <div id="quiz-mode">
      <div id="quiz-prompt">小挑战：按下正确的字母键</div>
      <div id="quiz-emoji"></div>
      <div id="quiz-word"></div>
      <div id="quiz-feedback"></div>
      <button id="skip-quiz-btn" class="soft-btn" type="button">换一个</button>
    </div>
  </section>

  <aside id="control-dock" aria-label="游乐场控制台">
    <button class="play-btn active" data-action="letters" type="button"><span>🔤</span><strong>字母</strong></button>
    <button class="play-btn" data-action="numbers" type="button"><span>🔢</span><strong>数字</strong></button>
    <button class="play-btn" data-action="pinyin" type="button"><span>📖</span><strong>拼一拼</strong></button>
    <button class="play-btn" data-action="challenge" type="button"><span>🎮</span><strong>小挑战</strong></button>
    <button class="play-btn" data-action="effect" type="button"><span id="effect-icon">💥</span><strong id="effect-label">爆炸</strong></button>
  </aside>

  <section id="recent-panel" aria-label="最近发现">
    <span class="recent-title">最近发现</span>
    <div id="recent-list"></div>
  </section>
</main>
```

- [ ] **Step 2: Replace layout CSS**

Update the top CSS so `body`, `#app-shell`, `#top-bar`, `#play-area`, `#control-dock`, `.play-btn`, and `#recent-panel` define a responsive playground. Keep existing animation, particle, display, pinyin, quiz, and mobile-input selectors where still relevant.

Use these concrete rules:

```css
body {
  background: #0d0d2b;
  font-family: "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif;
  overflow: hidden;
  min-height: 100vh;
  color: #fff;
  user-select: none;
}

#app-shell {
  position: relative;
  z-index: 1;
  width: min(1180px, calc(100vw - 32px));
  height: min(760px, calc(100vh - 32px));
  margin: 16px auto;
  display: grid;
  grid-template-columns: 1fr 190px;
  grid-template-rows: auto 1fr auto;
  gap: 14px;
}

#top-bar {
  grid-column: 1 / -1;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
}

#top-bar h1 {
  font-size: clamp(28px, 4vw, 48px);
  line-height: 1;
}

#friendly-prompt {
  margin-top: 8px;
  color: rgba(255,255,255,0.68);
  font-size: clamp(14px, 2vw, 20px);
}

#reward-panel {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  justify-content: flex-end;
}

.reward-pill {
  min-width: 92px;
  border: 2px solid rgba(255,255,255,0.18);
  background: rgba(255,255,255,0.1);
  border-radius: 18px;
  padding: 10px 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-size: 18px;
}

#play-area {
  position: relative;
  overflow: hidden;
  border: 2px solid rgba(255,255,255,0.16);
  border-radius: 26px;
  background: rgba(255,255,255,0.06);
  box-shadow: 0 24px 80px rgba(0,0,0,0.25);
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 0;
  cursor: pointer;
}

#control-dock {
  display: grid;
  gap: 10px;
  align-content: stretch;
}

.play-btn, .soft-btn {
  border: 2px solid rgba(255,255,255,0.16);
  background: rgba(255,255,255,0.09);
  color: #fff;
  border-radius: 18px;
  cursor: pointer;
  transition: transform 0.16s ease, background 0.16s ease, border-color 0.16s ease;
}

.play-btn {
  min-height: 86px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-size: 18px;
}

.play-btn span {
  font-size: 30px;
}

.play-btn:hover, .soft-btn:hover {
  transform: translateY(-2px);
  background: rgba(255,255,255,0.15);
}

.play-btn.active {
  border-color: #ffd93d;
  background: rgba(255,217,61,0.18);
}

#recent-panel {
  grid-column: 1 / -1;
  min-height: 62px;
  display: flex;
  align-items: center;
  gap: 12px;
  overflow: hidden;
}

.recent-title {
  color: rgba(255,255,255,0.55);
  white-space: nowrap;
}

#recent-list {
  display: flex;
  gap: 8px;
  overflow: hidden;
}

.recent-chip {
  border-radius: 14px;
  padding: 8px 12px;
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.14);
  white-space: nowrap;
  font-weight: 700;
}

@media (max-width: 760px) {
  body { overflow: hidden; }
  #app-shell {
    width: calc(100vw - 20px);
    height: calc(100vh - 20px);
    margin: 10px auto;
    grid-template-columns: 1fr;
    grid-template-rows: auto 1fr auto auto;
  }
  #top-bar {
    align-items: flex-start;
  }
  #reward-panel {
    gap: 6px;
  }
  .reward-pill {
    min-width: 70px;
    padding: 8px 10px;
    font-size: 14px;
    border-radius: 14px;
  }
  #control-dock {
    grid-row: 3;
    grid-template-columns: repeat(5, minmax(0, 1fr));
    gap: 6px;
  }
  .play-btn {
    min-height: 62px;
    border-radius: 14px;
    font-size: 12px;
    gap: 4px;
  }
  .play-btn span {
    font-size: 22px;
  }
  #recent-panel {
    grid-row: 4;
    min-height: 44px;
  }
}
```

- [ ] **Step 3: Verify layout**

Open `index.html` through a local server. Expected: a large central stage, header rewards, right-side controls on desktop, bottom controls on mobile viewport, and no overlapping text.

## Task 2: Reward State And Recent Discoveries

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add DOM references and reward state**

After existing DOM refs, add:

```js
const starsCountEl  = document.getElementById("stars-count");
const streakCountEl = document.getElementById("streak-count");
const recentListEl  = document.getElementById("recent-list");
const effectIconEl  = document.getElementById("effect-icon");
const effectLabelEl = document.getElementById("effect-label");

let stars = 0;
let streak = 0;
const recentDiscoveries = [];
```

- [ ] **Step 2: Add render/update helpers**

Add these helpers before the mode system:

```js
function renderRewards() {
  starsCountEl.textContent = stars;
  streakCountEl.textContent = streak;
}

function addReward(amount = 1) {
  stars += amount;
  streak += 1;
  renderRewards();
}

function addDiscovery(label, emoji) {
  recentDiscoveries.unshift({ label, emoji });
  recentDiscoveries.splice(8);
  recentListEl.innerHTML = "";
  for (const item of recentDiscoveries) {
    const chip = document.createElement("span");
    chip.className = "recent-chip";
    chip.textContent = `${item.emoji} ${item.label}`;
    recentListEl.appendChild(chip);
  }
}
```

- [ ] **Step 3: Wire rewards into `showKey()`**

In the letter branch of `showKey()`, after `triggerEffect(...)`, add:

```js
addReward(1);
addDiscovery(`${upper} ${data.zh}`, data.emoji);
```

In the number branch of `showKey()`, after the effect trigger block and before `speakNumber(...)`, add:

```js
addReward(1);
addDiscovery(`${upper} ${data.zh}`, data.emoji);
```

- [ ] **Step 4: Initialize rewards**

Near `setMode("free");`, add:

```js
renderRewards();
```

- [ ] **Step 5: Verify rewards**

Press `A`, `B`, and `3`. Expected: stars and streak increase to 3, recent discoveries show the newest items first.

## Task 3: Activity Dock And Input Routing

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace mode button wiring**

Remove the old `.mode-btn` and `.fx-btn` event listener blocks. Add:

```js
const EFFECTS = [
  { icon: "💥", label: "爆炸" },
  { icon: "🎵", label: "音符" },
  { icon: "🎻", label: "小提琴" },
];

function updateActivityButtons(activeAction) {
  document.querySelectorAll(".play-btn").forEach(btn => {
    const action = btn.dataset.action;
    btn.classList.toggle("active", action === activeAction || (activeAction === "free" && action === "letters"));
  });
}

function updateEffectButton() {
  const fx = EFFECTS[effectMode];
  effectIconEl.textContent = fx.icon;
  effectLabelEl.textContent = fx.label;
}

document.querySelectorAll(".play-btn").forEach(btn => {
  btn.addEventListener("click", e => {
    e.stopPropagation();
    const action = btn.dataset.action;
    if (action === "letters") {
      setMode("free");
      showRandomLetter();
    } else if (action === "numbers") {
      setMode("free");
      showRandomNumber();
    } else if (action === "pinyin") {
      setMode("pinyin");
    } else if (action === "challenge") {
      setMode("quiz");
    } else if (action === "effect") {
      setEffectMode((effectMode + 1) % EFFECTS.length);
    }
  });
});
```

- [ ] **Step 2: Add random helpers**

Before `showKey(raw)`, add:

```js
function showRandomLetter() {
  const keys = Object.keys(LETTERS);
  showKey(keys[Math.floor(Math.random() * keys.length)]);
}

function showRandomNumber() {
  const keys = Object.keys(NUMBERS);
  showKey(keys[Math.floor(Math.random() * keys.length)]);
}

function showSurprise() {
  if (Math.random() < 0.7) showRandomLetter();
  else showRandomNumber();
}
```

- [ ] **Step 3: Update `setMode()` button state**

Inside `setMode(m)`, replace the old mode button toggling with:

```js
updateActivityButtons(m === "quiz" ? "challenge" : m === "pinyin" ? "pinyin" : "free");
```

- [ ] **Step 4: Update `setEffectMode()`**

Inside `setEffectMode(n)`, replace the old `.fx-btn` toggling with:

```js
effectMode = n;
updateEffectButton();
```

- [ ] **Step 5: Replace global click behavior**

Replace the current document click listener with:

```js
document.getElementById("play-area").addEventListener("click", e => {
  if (e.target.closest("button")) return;
  if (currentMode === "free") showSurprise();
  else if (currentMode === "quiz") nextQuiz();
});
```

- [ ] **Step 6: Verify routing**

Click Letters, Numbers, Pinyin, Challenge, and Sounds. Expected: each control changes the visible activity or effect; clicking the stage in free mode shows a random item.

## Task 4: Friendlier Challenge And Pinyin Discovery

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add skip challenge wiring**

After quiz DOM refs, add:

```js
document.getElementById("skip-quiz-btn").addEventListener("click", e => {
  e.stopPropagation();
  nextQuiz();
});
```

- [ ] **Step 2: Update correct challenge reward**

In `handleQuizKey`, inside the correct-answer branch before speech, add:

```js
addReward(3);
addDiscovery(`${quizAnswer} ${LETTERS[quizAnswer].zh}`, LETTERS[quizAnswer].emoji);
```

- [ ] **Step 3: Keep incorrect feedback non-punitive**

In the incorrect-answer branch, ensure it does not reset stars or streak. Keep the text:

```js
quizFeedbackEl.textContent = "再试试！";
```

- [ ] **Step 4: Reward pinyin matches**

In `handlePinyinKey`, after `triggerEffect(c1,c2,523);`, add:

```js
addReward(2);
addDiscovery(`${match.zi} ${match.py}`, match.emoji);
```

- [ ] **Step 5: Verify discovery activities**

Enter pinyin `shan`. Expected: character displays, stars increase by 2, recent discoveries update. Start challenge and answer correctly. Expected: stars increase by 3 and challenge advances.

## Task 5: Mobile Polish And Browser Verification

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Make mobile keyboard trigger clearer**

Update `#keyboard-btn` CSS:

```css
#keyboard-btn {
  position: fixed;
  right: 16px;
  bottom: 108px;
  background: rgba(255,255,255,0.16);
  border: 2px solid rgba(255,255,255,0.24);
  border-radius: 18px;
  width: 64px;
  height: 56px;
  font-size: 26px;
  cursor: pointer;
  display: none;
  align-items: center;
  justify-content: center;
  z-index: 20;
  color: #fff;
}
```

- [ ] **Step 2: Update button label**

Change the button text from `🎹` to:

```html
⌨️
```

- [ ] **Step 3: Run local server**

Run:

```bash
python3 -m http.server 8080
```

Expected: server starts and serves `http://localhost:8080/`.

- [ ] **Step 4: Verify in Chrome desktop**

Open `http://localhost:8080/`. Expected:

- No console-visible page crash.
- Pressing `A` shows apple content.
- Pressing `5` shows number content and count items.
- Sound button cycles labels.
- Challenge accepts correct and incorrect answers.

- [ ] **Step 5: Verify responsive layout**

Use browser viewport or screenshot at a narrow width. Expected: control dock moves to bottom, stage remains visible, text does not overlap buttons, keyboard button is visible for mobile user agents or remains hidden on desktop.

