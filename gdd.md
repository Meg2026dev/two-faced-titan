# GAME DESIGN DOCUMENT FOR: Two-Faced Titan

## 10. Game Overview

**Title:** Two-Faced Titan
**Genre:** Action Boss-Battler (single-screen arena fighter)
**Theme Interpretation (Coin Flip):** The boss is a colossal living coin. Its entire body is a giant minted coin-face that perpetually flips between **HEADS** (vulnerable, face forward) and **TAILS** (armored, backplate forward). Combat rhythm is literally coin flipping—players must read the flip state and act accordingly. Secondary coin motifs: arena floor is a giant coin medallion, player attacks are "mint strikes," boss health bar is a two-sided coin gauge.
**Target Audience:** Casual-to-midcore mobile players (ages 10+), boss-rush fans, one-more-run arcade players. Touch-first, playable in 2-5 minute sessions.
**Platform:** Modern mobile + desktop browsers. Single `index.html`, vanilla JS, Canvas 2D.

---

## 1. CORE LOOP and Level Design

### Core Loop (30-second readable)
1. Titan flips between HEADS (gold face, eyes open, glowing weak point) and TAILS (silver armored back, sparks deflecting) on a timed pattern.
2. During **HEADS** windows → player taps to strike the glowing weak-point rune on the titan's forehead. Successful hits build the **Mint Combo** meter.
3. During **TAILS** windows → titan attacks (slam, sweep, projectile volley). Player drags to dodge out of telegraphed danger zones. Near-miss dodges grant **Near-Miss Bonus (+50 pts)**.
4. Fill the Mint Combo meter to trigger **MINT BREAK** — a high-damage critical burst with screen-wide coin-shower.
5. Deplete titan HP to zero → Victory. Reach Zone 2/3 HP thresholds → **Zone Transition** (flip-pattern accelerates, new attack types unlock, arena palette shifts).

### Levels / Phases (Linear Difficulty)
The game is a single continuous boss fight divided into 3 escalating **Zones** (HP phases):

| Zone | HP Range | Flip Speed | Attacks Unlocked | Arena Modifier |
|------|----------|------------|------------------|----------------|
| Zone 1 (Learning) | 100%–66% | Slow (2.5s per flip), long HEADS windows (1.8s) | Coin Slam (telegraphed AoE circle), Headbutt Lunge | Neutral gold medallion floor |
| Zone 2 (Challenge) | 66%–33% | Medium (1.8s per flip, HEADS windows 1.2s) | + Coin Projectile Volley (3-way spread), Armor Sweep (linear telegraph) | Floor medallion splits into moving quarter-sectors that rotate slowly |
| Zone 3 (Mastery) | 33%–0% | Fast (1.2s per flip, HEADS windows 0.8s, occasional fake-out feint flips) | + Double Slam (sequential AoEs), Rage Volley (5-way spread), Enraged Lunge (faster, shorter telegraph) | Arena edges crumble (smaller safe zone), periodic "Coin Shower" hazard (falling coin-stuns, dodge circles) |

**Enemy/Object Placement:** Single boss centered-upper arena. Player starts bottom-center. Danger-zone telegraphs render as translucent red/amber circles or lines on the floor before impact. No minion enemies—focus is 1v1 duel readability.

**Zone Bonuses:** Reaching each new zone grants **+200 Zone Bonus pts** + full Mint Combo meter reset + 1.5s invulnerability breather + "ZONE X" banner + palette shift feedback.

---

## 2. CONTROLS

### Touch (Smartphone) — Primary
- **Tap** anywhere on the arena during **HEADS** windows → Mint Strike (attacks boss weak point). Single tap = one strike. Rapid taps during long HEADS windows = combo chain (capped at 3 hits per window to prevent spam).
- **Drag** anywhere on the arena → player moves along a horizontal/2D dodge lane at the bottom of the screen. Drag left/right repositions player to dodge telegraphed AoEs. Drag distance maps 1:1 to movement (finger-slead feel).
- **Release** drag → player stops at current position.
- All touch targets finger-sized: dodge lane occupies bottom 25% of screen height; tap-to-strike registers on entire upper 70% arena area (generous, no tiny buttons).

### Mouse (PC Browser) — Simultaneous Support
- **mousedown / mousemove / mouseup** mirror touch exactly: click-and-hold + move = dodge drag; click = tap-strike.
- Cursor position drives dodge direction identically to touch finger position.

### Optional Keyboard (not enabled this run — "full controls" not requested)
- WASD / Arrow keys / Space omitted per orchestrator flag (touch + mouse only required).

### Input Zones (Visual Guide)
- Bottom 25% of screen: **DODGE ZONE** (drag to move player token left/right).
- Upper 75% of screen: **STRIKE ZONE** (tap to attack during HEADS).
- Visual feedback: subtle dashed divider line; player token glows when in valid strike range.

---

## 3. MECHANICS

### 3a. Primary & Secondary Mechanics

**Primary: Flip-State Combat**
- Titan has state machine: `HEADS` ↔ `TAILS`, flipping on timer with zone-based speed.
- HEADS: Weak-point rune on forehead glows gold. Player strikes deal damage. Titan does not attack (except telegraphed transitions).
- TAILS: Armored silver backplate. Player strikes deal 0 damage + metallic "clink" deflect feedback (discourages spam). Titan executes 1–2 attacks per TAILS window.
- Flip animation: 0.4s spin (Y-axis coin flip visual), brief hit-stop at apex, lands with thud SFX.

**Secondary: Mint Combo Meter**
- Each successful HEADS strike adds +1 combo (max 5 per meter).
- Meter decays −1 combo every 3s of no hits (encourages aggression).
- Full meter (5/5) → **MINT BREAK**: auto-triggers high-damage burst (15% of current zone HP), massive coin-shower particles, screenshake, score popup "+MINT BREAK xN".
- Combo count displays above player token; breaks with shatter SFX on decay.

**Win Condition:** Titan HP reaches 0 → Victory screen.
**Lose Condition:** Player HP (3 hearts) reaches 0 → Game Over screen. Player takes damage from un-dodged attacks (1 heart per hit), invulnerability frames 1s post-hit.

**Scoring System:**
- HEADS strike hit: +100 pts × current combo multiplier (combo 1=×1, 2=×2 ... 5=×5).
- Near-Miss Bonus: +50 pts when dodge ends within 0.5s of a danger-zone impact edge (risk-reward for late dodges).
- Zone Bonus: +200 pts per zone transition.
- MINT BREAK bonus: +500 pts + damage.
- Victory bonus: +1000 pts + remaining hearts ×200.
- Score popups float up from hit/dodge points, auto-fade after 1s.

### 3b. DIFFICULTY BALANCE — BEGINNER-FRIENDLY & MOTIVATING (REQUIRED)

**Entry Level Values (Zone 1):**
- Flip speed: 2.5s per state, HEADS window 1.8s (generous — ~3-4 taps possible).
- Titan attacks: Coin Slam only (single large AoE circle, 1.2s telegraph windup, easy to walk out of via drag).
- Player speed: fast drag-response (near 1:1), dodge lane wide (full screen width).
- Mint Combo decay: 5s (very forgiving early).

**Motivation (First 30 Seconds):**
- Zone 1 opens with a 2s "READY?" countdown, then titan does a slow introductory flip (no attack) — player lands 3-4 free strikes, sees combo build to 3-4x, score popups fly. First success (20%+ HP chunk or combo 3) achievable in first 15-20s.
- Tutorial tooltips (non-blocking, fade after 6s): "TAP to strike on HEADS!" then "DRAG to dodge on TAILS!".

**Near-Miss Bonus (+50 pts):** Risky late dodges (staying in danger zone until last 0.5s then escaping) reward +50 pts + "NEAR MISS!" popup + brief slow-mo + gold particle burst. Teaches mastery without punishing beginners (early safe dodges still work fine, just no bonus).

**Zone Bonus:** Each zone transition = +200 pts + banner + palette shift + 1.5s breather. Sense of progression is explicit (banner text: "ZONE 2 — THE FLIP QUICKENS!").

**Progression (Linear Curve):**
- Learning (Zone 1): generous gaps, slow speed, 1 attack type, 5s combo decay.
- Challenge (Zone 2): tighter windows (1.2s), 2 attack types, moving floor sectors add positioning puzzle, 3s combo decay.
- Mastery (Zone 3): fast flips (0.8s windows), 4 attack types, feint flips (fake-out: starts flip, reverses — reads as heads then tails), shrinking arena, Coin Shower hazard. Linear, not exponential — each zone adds ~1 new variable, doesn't multiply all existing ones.

---

## 4. VISUAL STYLE

- **Art Style:** Flat-shaded vector cartoon with bold outlines (2-3px), readable at mobile scale. Think "mobile boss-rush arcade" — clean silhouettes, high contrast between gold (HEADS) / silver (TAILS) states.
- **Key Visual Elements:**
  - Titan: giant circular coin-body (fills 40% of screen height), embossed face (HEADS: serene king face with glowing rune forehead; TAILS: heraldic shield-backplate with rivets). Spin-flip animation with motion blur arcs.
  - Player: small armored knight token (bottom lane), simple 2-frame bob idle, lunge-strike animation, dodge-roll animation.
  - Arena: giant floor coin-medallion (heads/tails embossed, rotates slowly in later zones), torch-lit colosseum arches in background silhouette.
  - Danger telegraphs: translucent red/amber circles & lines with pulsing outline before strike.
  - Particles: coin sparks on deflects, gold dust on hits, confetti on MINT BREAK, dust puffs on dodges.
- **Color Palette:** Gold #FFD700 (HEADS), Silver #C0C0C0 (TAILS), Deep Purple arena bg #2D1B4E, Amber telegraph #FFA500, Red danger #FF4444, Teal UI accents #00CED1, White highlights.
- **Mood:** Dramatic but readable — epic boss arena without visual clutter. High-contrast states so players instantly read HEADS vs TAILS even on small screens.

---

## 5. AUDIO DIRECTION

- **Music:** Orchestral-electronic hybrid — driving taiko-style percussion + brass stabs for tension, synth pads for arena atmosphere. Intensity layers: Zone 1 (mid), Zone 2 (+faster percussion), Zone 3 (+choir risers, distorted bass). MINT BREAK triggers a brief musical flourish/sting.
- **Sound Effects:**
  - Flip spin: rising whoosh (0.3s).
  - Flip land: deep thud + metallic ring.
  - HEADS strike hit: satisfying "mint press" crunch + coin jingle.
  - TAILS deflect: dull metallic "clink" (discouraging, not punishing).
  - Danger telegraph: low ominous hum building to sharp warning ping at impact moment.
  - Near-miss dodge: sharp "swish" + rising shimmer.
  - MINT BREAK: explosive coin-shower cascade + triumphant sting.
  - Player hit: grunt + armor crack + brief low-pass filter on music.
  - UI: button taps = crisp coin-click, menu open = soft page-flip.
- **Technical:** Web Audio API oscillators + noise buffers (no external files, single HTML). Music/SFX togglable via Web Audio gain nodes. Procedural SFX preferred over samples for size.

---

## 6. GAME STATES

| State | Description | Transitions |
|-------|-------------|-------------|
| TITLE | Logo, Start, How to Play, Settings entry | Start → HowToPlay / Gameplay; Settings → Settings |
| HOW_TO_PLAY | Static instruction screen with diagrams | Back → Title / Pause Menu |
| PLAYING | Active boss fight, HUD (HP hearts, score, combo meter, zone indicator) | Pause btn → PAUSED; player HP 0 → GAME_OVER; titan HP 0 → VICTORY |
| PAUSED | Overlay menu: Resume, How to Play, Music/SFX toggles, Back to Title | Resume → PLAYING; HowToPlay → HOW_TO_PLAY (returns to PAUSED); BackToTitle → TITLE |
| SETTINGS | Music/SFX toggles, Reset Data, Credits | Back → TITLE / PAUSED (context-aware) |
| ZONE_TRANSITION | Brief banner + palette shift + breather (auto-resumes) | Auto → PLAYING (next zone) |
| GAME_OVER | Score, retry button, back to title | Retry → PLAYING (reset); BackToTitle → TITLE |
| VICTORY | Score breakdown, medals, replay button | Replay → PLAYING; BackToTitle → TITLE |

State machine enforced via `gameState` string variable. No illegal transitions (e.g., can't PAUSED → VICTORY).

---

## 7. USER INTERFACE and Screen Inventory (REQUIRED — ALL INCLUDED)

### a. Title Screen
- Game logo: "TWO-FACED TITAN" with animated coin-flip in the "O" of TITAN (idle spin loop).
- **Start** button (large, center, pulsing gold).
- **How to Play** button.
- **Settings** gear icon (top-right corner).
- Background: slow-pan colosseum silhouette + floating idle coins.

### b. Pause Menu (MUST HAVE — all present)
- **Background music on/off toggle** (speaker icon, state saved to localStorage).
- **Sound fx on/off toggle** (SFX icon, state saved to localStorage).
- **How to Play button** (opens instructions overlay, closes back to pause).
- **Go back to Start Menu button** (returns to TITLE).
- **Resume Game button** (returns to PLAYING, unpauses).
- Semi-transparent dark overlay behind menu; game frozen (update loop halted).

### c. How to Play Screen
- Beginner-friendly instructions, icon-diagram pairs:
  - "TAP to strike when the Titan shows its HEADS face (gold, glowing rune)."
  - "DRAG left/right to dodge when the Titan shows its TAILS armor (silver) and attacks land."
  - "Build the Mint Combo meter for a MINT BREAK!"
  - "Near-misses give bonus points — dodge late for glory, early for safety."
- "Got it!" back button (context-aware: returns to Title or Pause Menu).

### d. Gameplay Screen
- Main arena view: titan (upper-center), player token (bottom dodge lane), danger telegraphs, HUD elements:
  - Top-left: player hearts (3), score counter.
  - Top-right: zone indicator ("ZONE 1"), pause button.
  - Bottom-center: Mint Combo meter (5-segment coin bar), combo count popup.
  - Titan HP bar: two-sided coin gauge (gold/silver halves deplete from center).

### e. Settings Screen
- **Music toggle** (on/off switch).
- **SFX toggle** (on/off switch).
- **Reset Data** button (clears localStorage: high score, settings — with confirm dialog).
- **Credits** button (opens overlay: "A Gameotron Jam Game — Theme: Coin Flip").
- **Back** button (context-aware return).

### f. Game Over / Victory Screen
- **Game Over:** "DEFEATED" title, final score, high-score comparison, Retry button, Back to Title.
- **Victory:** "TITAN VANQUISHED!" title, score breakdown (hits, near-misses, zones, mint breaks, heart bonus), medal rank (Bronze/Silver/Gold based on score), Replay button, Back to Title.

---

## 8. MONETIZATION/SCOPE

**FREE-TO-PLAY WITH NO MONETIZATION.** Single-session arcade boss fight, no ads, no IAP, no accounts, no external assets. Pure skill + high-score chase. Scope: one boss, 3 zones, ~2-4 minute full clear, instant restart loop.

---

## 9. IMPLEMENTATION NOTES

- **Single `index.html`** — all HTML/CSS/JS embedded, vanilla JS only, Canvas 2D rendering.
- **Responsive canvas:** internal resolution 800×1200 (portrait mobile-first), letterboxed/scaled to fit any viewport via CSS `object-fit: contain` + resize listener. Touch/mouse coordinates mapped from screen → canvas space.
- **Touch + Mouse simultaneous:** unified pointer input handler (pointerdown/move/up + touchstart/move/end fallback), maps to logical `isDragging` + `pointerX`.
- **Game loop:** `requestAnimationFrame` with fixed-timestep accumulator (60fps logic, render as-available). Delta-time capped (max 50ms) to prevent spiral-of-death on tab-switch.
- **State machine:** `gameState` enum, explicit transition function with validation.
- **Audio:** Web Audio API — master gain → music gain + sfx gain. Procedural oscillators/noise for all SFX (no samples). Music: looping sequencer with zone-intensity layers. Toggles write to localStorage.
- **Persistence:** localStorage keys: `tt_highscore`, `tt_music`, `tt_sfx`.
- **No external dependencies, no build step, no frameworks.** Runs from `file://` or any static host.
- **Performance:** particle cap 50 simultaneous, object pooling for projectiles/particles, no per-frame allocations in hot loop.

---

*GDD generated by gdd_agent — Two-Faced Titan, Gameotron pipeline.*
