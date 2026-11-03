<h1>Angry Bird</h1>

[閱讀中文](README_cn.md)

link:[https://hayes314.github.io/abhtml](https://hayes314.github.io/abhtml)

A complete Angry Birds game migrated from Python/Pygame to **HTML5 + Matter.js**, refined through multiple iterations into a feature-rich, smooth, cross-platform experience. Below is a full breakdown of all features by category.

---

## 🎮 1. Core Gameplay

### 1.1 Slingshot Launch System
- **Drag to aim**: Pull the bird on the slingshot backward to set angle and power
- **Real-time trajectory prediction**: White dotted parabola while dragging, perfectly synced with the physics engine
- **Power bar**: Green → yellow → red gradient for intuitive power feedback
- **Max pull limit**: Prevents over-pulling and keeps balance

### 1.2 Physics Engine (Matter.js)
- **Fixed timestep**: 60 Hz physics updates — consistent feel regardless of display refresh rate
- **High-precision solver**: 12 position iterations, 10 velocity iterations — stable stacks, no jitter
- **Chamfered collision bodies**: Reduces corner-catching issues on boxes
- **Sleeping mechanism**: Idle bodies auto-sleep, greatly reducing CPU usage
- **Auto-wake**: When a block is removed, upper layers are force-woken to prevent floating

### 1.3 Damage System
- **Momentum-based**: `impulse = relative velocity × effective mass`, not just speed
- **Threshold filter**: Impulses below 6 are ignored to prevent self-destruction from natural stacking
- **Material differentiation**: Wood, ice, and stone each have distinct HP / density / friction / restitution

---

## 🐦 2. Eight Unique Birds

| Bird | Ability | Special Mechanics |
|------|---------|-------------------|
| 🔴 **Red** | No special ability | Balanced HP, good for precise shots |
| 🟡 **Yellow** | Dash boost | **Unlimited use** + **exempt from 0.5s auto-settle** |
| ⚫ **Black** | Large explosion | **Extra 2 seconds of life**, can keep flying |
| 🔵 **Blue** | Tri-split | Fan coverage, each inherits 1.15× velocity |
| ⚪ **White** | Egg bomb drop | **Max 5 per bird** + small blast (90px) |
| 🟢 **Green** | Boomerang | Instantly reverses flight to hit targets behind |
| 🟠 **Orange** | Inflate impact | Mass ×2.8 for 1 second |
| 🟣 **Purple** | Sustained EMP | **1.5s continuous pulses** + **extra 2 seconds of life** |

### Design Highlights

**Yellow Bird**:
- Each tap adds 12px/step velocity (cap 40)
- Chain taps to pierce multiple walls
- **Auto-settle exempt**: staying still won't force-end the turn

**Black / Purple "Linger" mechanic**:
- When the turn would normally end, they gain **2 extra seconds** of active time
- Can keep flying, can be hit, can continue acting
- Purple can **re-trigger EMP** during linger
- Linger aura visualized (yellow/purple pulsing ring)

**White Bird limits**:
- Fixed 5-egg budget per bird
- Max 4 eggs alive at once (physics safety)
- UI shows `Eggs X/5` in real time

**Purple Bird enhancements**:
- EMP radius 220 → **260px**
- Duration 1.0 → **1.5 seconds**
- Pulses every 0.25s, **6 total damage ticks**
- Up to 900 cumulative damage (9× original)

---

## 🏗️ 3. Intelligent Level Generation

### 3.1 Reproducible Randomness (Seed System)
- **Mulberry32 algorithm**: Same seed + same level = same layout
- **5-character Base36 seed**: e.g. `ABC12`, shareable and replayable
- **Seed history**: localStorage keeps the last 8 seeds
- **One-click copy**: Click the 📋 button to copy to clipboard
- **Browser language detection**: Auto-matches system language

### 3.2 Four Structure Templates
- **Tower**: Single-column multi-floor stacking
- **Pyramid**: Wide base, narrow top
- **Fortress**: Archway + pigs hidden inside
- **Twin**: Two towers + connecting bridge

### 3.3 Difficulty Curve
- **Structure count**: 2 → 3 → 4 (scales with level)
- **Material tendency**: Wood → mixed → stone-heavy
- **Bird variety**: Red → Red/Yellow/Black → all 8 types
- **Bird count**: Dynamically calculated from pig count

### 3.4 Physical Stability Guarantees
- **Pre-generation stability simulation**: Run 50 steps in an isolated engine to check for collapse
- **Up to 8 retries**: Seed + 9973 (prime offset) each attempt
- **Fallback mechanism**: If all unstable, return the "best" attempt
- **Horizontal avoidance**: Prevents structures from overlapping

### 3.5 Anti-Clip Protection
- **Boundary clipping**: All objects constrained to `[WALL_LEFT+30, WALL_RIGHT-30]`
- **Initial stability test**: Invalid layouts skipped immediately
- **Triple visual protection**: Left/right invisible walls + ceiling + bottom ground

---

## 🎨 4. Visual Effects

### 4.1 Pre-rendering Optimization
- **Sky gradient**: Rendered once to an offscreen canvas
- **Clouds, distant mountains, near mountains**: Static layers, only blitted each frame
- **Ground gradient**: Dynamically drawn but only occupies the bottom 60px

### 4.2 Particle System
- **Impact particles**: Count scaled by intensity
- **Debris particles**: Scaled by block size; ice gets extra white shards
- **Triple-layer explosion**: Orange/red/yellow overlay
- **Pig poof**: Green + white dual burst
- **EMP ripples**: Three layers of purple waves expanding in cycles

### 4.3 Material Rendering
- **Wood**: Horizontal/vertical grain lines with center annual rings
- **Ice**: Cross-highlight lines, semi-transparent
- **Stone**: Internal dark circles, rough texture
- **HP visualization**: Lower HP = darker color

### 4.4 Bird Personalization
Each bird has a unique look:
- Red: Top feather spike
- Green: 5 spikes
- Orange: Flame pattern
- Purple: Starry spots
- Blue: Small top spike
- White: Head highlight
- All birds share: Shadow, gradient body, belly, eyes, angry brows, beak

### 4.5 Dynamic Feedback
- **Linger aura**: Yellow (black) / purple (purple) pulsing ring
- **Unlimited ability aura**: Green breathing ring (ready) / grey (cooling)
- **Inflate aura**: Orange expanding circle
- **Egg pre-explosion flash**: Red pulse
- **Floating text**: +1000 score rising and fading

---

## 📐 5. Adaptive & Cross-Platform

### 5.1 Triple Coordinate System
```
Screen pixels (CSS px) → Bitmap (physical px) → Logical coords (1200×700)
```

### 5.2 Letterbox Adaptation
- **Strict 1200:700 aspect ratio**
- **Fully visible on any screen, no cropping**
- **Unified scale factor**: X = Y, never stretched

### 5.3 Multi-Device Compatibility
| Scenario | Result |
|----------|--------|
| 1920×1080 desktop | Centered, ultra-sharp |
| 4K high-DPI | DPR=2, 2× bitmap resolution |
| Portrait phone | Fully visible, playable by touch |
| Ultra-wide 21:9 | Letterboxed, no distortion |
| Window resize | Real-time reflow, no lag |

### 5.4 Multi-Path Listeners
- `window.resize`: Desktop window stretch
- `orientationchange`: Portrait/landscape switch
- `visualViewport.resize`: Mobile address bar changes
- `visualViewport.scroll`: Mobile address bar scroll
- `matchMedia(resolution)`: DPI changes
- `ResizeObserver`: Fallback listener

### 5.5 Mouse + Touch Support
- **Mouse**: Drag, click
- **Touch**: Drag, click with `passive: false` to prevent scrolling
- **Coordinate inverse-mapping**: Click position always accurate regardless of scaling

---

## 🌐 6. Trilingual Internationalization

### 6.1 Three Languages
- **繁體中文** (zh-TW)
- **简体中文** (zh-CN)
- **English** (en)

### 6.2 Full Translation Coverage
- Start screen, buttons, hints
- Tutorial (3 major sections × 10+ items)
- Complete 8-bird codex (name, description, ability, usage)
- In-game HUD (score, birds, pigs, hints)
- Result screen
- Toast notifications
- Confirmation dialogs

### 6.3 Smart Language Detection
```
localStorage saved value → browser language detection → default to Simplified Chinese
```

### 6.4 Instant Switching
- **No refresh needed**: Click the switcher, effective immediately
- **State preserved**: Switching doesn't affect progress
- **Three update methods**:
  1. `data-i18n` attribute → static text
  2. Dynamic rebuild → codex + tutorial
  3. Canvas rendering → reads translation each frame

### 6.5 Language Switcher
- **Start screen**: Capsule-style in top-right
- **In-game**: Smaller version, position avoids other buttons
- **Persistent**: Remembers user choice

---

## 🎯 7. Total Score System

### 7.1 Dual-Score Display
```
┌─────────────────┐
│ LEVEL SCORE     │  ← Yellow 30px
│ 12500           │
│ ─────────────── │
│ TOTAL           │  ← Blue 22px
│ 45800           │
└─────────────────┘
```

### 7.2 Real-Time Accumulation
```js
function addScore(pts) {
  score += pts;
  totalScore += pts;   // synchronous accumulation
}
```

### 7.3 Scoring Rules
| Event | Points |
|-------|--------|
| Pig defeated | +1000 |
| Block destroyed | +50 |
| Unused bird on win | +500 each |

### 7.4 Lifecycle
| Action | Level | Total |
|--------|-------|-------|
| Next level | Reset | **Kept** |
| Retry level | Reset | **Kept** |
| New level | Reset | **Kept** |
| **Restart** | Reset | **Reset** |
| Page refresh | Reset | **Reset** |

---

## 🔄 8. Restart Feature

### 8.1 Three Entry Points
- **Start screen**: Only shown when there's prior progress
- **In-game**: Top-right 🔄 button
- **Result screen**: Side-by-side with "Next Level"

### 8.2 Confirmation Dialog
```
┌────────────────────────┐
│   Restart the game?    │
│                        │
│ Current total score    │
│ 45800 will be reset.   │
│ Progress cannot be     │
│ recovered.             │
│                        │
│ [Cancel] [Yes, restart]│
└────────────────────────┘
```

### 8.3 Smart Skip Confirmation
- **No progress**: Restart immediately, no dialog
- **Has progress**: Confirmation required to prevent accidents

### 8.4 Complete Reset
- Total score reset to zero
- Level back to 1
- Fresh random seed
- Clear physics world
- Hide all UI
- Return to start screen

### 8.5 Keyboard Shortcuts
- `Esc`: Cancel
- `Enter`: Confirm restart

---

## 🎮 9. Game State Management

### 9.1 Three States
```
aiming → flying → won / lost
```

### 9.2 Auto-Settle System (Multiple Safeguards)
- **0.5s still auto-end**: All birds except yellow
- **Lenient countdown**: Bird gone 1.5s + slight motion still counts down
- **Hard timeout**: 8 seconds force-settle (including linger)
- **Skip button**: Available 30 frames after bird disappears
- **Spacebar**: Skip anytime

### 9.3 Anti-Deadlock Design
- **Out-of-bounds removal**: Birds leaving the field are cleaned immediately
- **Reference sync**: `birdBody = null` synchronized in three places
- **Turn clearing**: `endTurn()` clears all birds + eggs
- **Unconditional state switch**: `endTurn()` always returns to `aiming`

### 9.4 Yellow Bird Special Exemption
- **Not subject to 0.5s auto-settle**
- Can keep aiming even when fully stopped
- Gives players a "take your time" option

---

## 📖 10. Tutorial System

### 10.1 Dual-Tab Design
- **🎯 Tutorial**: Goal, controls, scoring, materials
- **🐦 Bird Codex**: Complete cards for all 8 birds

### 10.2 Elegant Layout
- **Dynamic rendering**: Generated from `TRANSLATIONS`
- **Card-based**: Each rule as an independent card
- **kbd keycaps**: Realistic key styling
- **Material swatches**: Direct correspondence to in-game materials

### 10.3 Bird Card Design
Each card contains:
- Avatar (gradient background + Emoji)
- Name + category tag
- Description
- Ability box (left color bar by material)
- Usage tip (💡 hint)

### 10.4 Category Tags
- **Basic** (grey): No special ability
- **Active** (orange): Requires tap to trigger
- **Special** (purple): Changes trajectory or area effect
- **Unlimited** (green): Can be used indefinitely
- **Linger** (blue): Extra 2 seconds of life

---

## 🎵 11. Audio System

### 11.1 Pure Web Audio Synthesis
- **No external assets**: All sounds generated by oscillators
- **Low latency**: Direct playback, no loading
- **Adjustable params**: Frequency, duration, waveform, volume, slide

### 11.2 Sound List
| Event | Sound |
|-------|-------|
| Launch | Triangle wave descending |
| Impact | Square wave short (pitch by intensity) |
| Pig defeat | Sine wave rising |
| Wood break | Sawtooth mid-frequency |
| Ice break | Sine high-frequency descending |
| Stone break | Square low-frequency |
| Ability trigger | Sine wave rising |
| Explosion | Sawtooth very low frequency |

### 11.3 Audio Initialization
- **Lazy load**: `AudioContext` initialized on first click
- **Compatibility**: `webkitAudioContext` fallback
- **Exception protection**: Wrapped in `try/catch`

---

## 📱 12. Mobile Optimization

### 12.1 Preventing Unintended Interactions
- **`touch-action: none`**: Prevents double-tap zoom
- **`overscroll-behavior: none`**: Prevents pull-to-refresh
- **`user-select: none`**: Disables text selection
- **`passive: false`**: Allows preventDefault

### 12.2 Viewport Setup
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
```

### 12.3 Responsive Design
- **Below 480px**: Smaller font, narrower buttons, adjusted layout
- **Language switcher**: Position dynamically adjusted to avoid other buttons
- **UI components**: Scale proportionally

### 12.4 Performance Optimization
- **DPR cap at 2**: Avoids huge bitmaps on 4K screens
- **Pre-rendering**: Static layers rendered only once
- **Particle pool**: Array filtering instead of rebuilding
- **Fixed timestep**: Physics and rendering decoupled

---

## 🛠️ 13. Technical Architecture Highlights

### 13.1 Modular Design
```js
// Constants
const W = 1200, H = 700;
const FIXED_DT = 1000 / 60;

// Physics engine init
const engine = Engine.create({...});

// Game state
let state = 'aiming';

// Feature functions
function launch() {...}
function triggerAbility() {...}
function endTurn() {...}
```

### 13.2 Defensive Programming
- **`try/catch` around all physics operations**
- **`applyTemplate` validates input**
- **`loadLevel` triple fallback**
- **`resetGame` complete reset**

### 13.3 Memory Management
- **`removalQueue` deferred removal**: Avoids modifying world inside callbacks
- **`particlesSys.filter`**: Cleans expired particles
- **`clearDynamicBodies`**: Complete wipe on level change

### 13.4 Event System
- **`collisionStart` callback**: Reads impulse to compute damage
- **Only enqueue inside callback**: Never remove bodies directly
- **Main loop handles uniformly**: `processRemovals()`

### 13.5 State Synchronization
- **`birdBody = null` in three places**: Launch, out-of-bounds, turn end
- **`eggs` array sync**: Drop, explode, turn end
- **`empWaves` array sync**: Trigger, end, turn end

---

## 🎯 14. UX Design

### 14.1 Visual Feedback
- **Trajectory prediction**: White dotted line while dragging
- **Power bar**: Green → yellow → red gradient
- **Hit particles**: Impact, break, explosion
- **Floating text**: +1000 score rising
- **Auras**: Linger, unlimited ability, inflate

### 14.2 Audio Feedback
- **Every action has a sound**
- **Pitch by intensity**: Harder hits = higher pitch
- **Material distinction**: Different timbres for wood/ice/stone

### 14.3 State Indicators
- **Top-left HUD**: Score, birds, pigs
- **Top-right hints**: Controls, level progress
- **Ability hint bar**: Current bird's ability + status
- **Skip button**: Visible = usable

### 14.4 Error Protection
- **Invalid drag**: Below 12px won't fire
- **Seed validation**: Empty, illegal chars, too long
- **Copy failure**: Prompt to note manually
- **Load exception**: Fallback generation

### 14.5 Keyboard Shortcuts
| Key | Function |
|-----|----------|
| `R` | Retry level |
| `N` | New level |
| `Space` | Ability / skip |
| `Esc` | Close panel / dialog |
| `Enter` | Confirm / start |

---

## 🏆 15. Game Balance Design

### 15.1 Bird Limits
- **Yellow**: Unlimited dash, but capped speed
- **White**: Unlimited eggs, but max 5 per bird
- **Purple**: Unlimited EMP, but cooldown per use
- **Other birds**: One-shot abilities

### 15.2 Physics Safety
- **MAX_DASH_SPEED = 40**: Prevents tunneling
- **MAX_EGGS_ALIVE = 4**: Prevents lag
- **HARD_TIMEOUT_FRAMES = 480**: 8-second cap

### 15.3 Material Comparison
| Material | HP | Density | Trait |
|----------|-----|---------|-------|
| Wood | 400 | 0.0022 | Medium, standard |
| Ice | 180 | 0.0012 | Fragile, light, shatters |
| Stone | 1200 | 0.0055 | Hard, heavy, durable |

### 15.4 Progressive Difficulty
- **Level 1**: Pure red bird, single tower
- **Levels 2–3**: Yellow/black introduced
- **Level 4**: Blue/white introduced
- **Level 5**: Green/orange introduced
- **Level 7+**: All 8 birds

---

## 📊 16. Complete Feature Checklist

### Gameplay
- ✅ Slingshot launch + trajectory prediction
- ✅ 8 unique birds
- ✅ 3 building materials
- ✅ Physics engine (Matter.js)
- ✅ Intelligent level generation
- ✅ Seed system (reproducible)
- ✅ Seed history (8 slots)
- ✅ Seed copy
- ✅ Total score system
- ✅ Restart feature
- ✅ Trilingual switching
- ✅ Tutorial page
- ✅ Bird codex
- ✅ Sound system
- ✅ Particle effects

### UX
- ✅ Drag + touch dual support
- ✅ Keyboard shortcuts
- ✅ Toast notifications
- ✅ Confirmation dialogs
- ✅ Loading indicators
- ✅ Result screen
- ✅ Win/lose states
- ✅ Progress preservation

### Technical
- ✅ Fixed timestep
- ✅ Adaptive canvas
- ✅ DPR support
- ✅ Offscreen rendering
- ✅ Particle pool
- ✅ Defensive programming
- ✅ Memory management
- ✅ Event system

### Cross-Platform
- ✅ Desktop browsers
- ✅ Mobile browsers
- ✅ High-DPI screens
- ✅ Ultra-wide screens
- ✅ Orientation switching
- ✅ Window resizing

---

## 🎉 Summary

This Angry Birds project migrated from Python/Pygame to HTML5 and went through **20+ iterations**, resulting in a game that is:

- **Feature-complete**: 8 birds, 3 materials, 4 structures, endless levels
- **Smooth experience**: Stable physics, no deadlocks, rich feedback
- **Cross-platform**: Desktop + mobile + high-DPI
- **Internationalized**: Full trilingual coverage
- **Robust code**: Defensive programming, memory management, state sync
- **Polished visuals**: Particles, auras, gradients, animations
- **Well balanced**: Unlimited abilities with limits, material differentiation, progressive difficulty

**Key Highlights**:
1. 🎲 **Reproducible randomness**: Seed system makes every run shareable and replayable
2. 🐦 **8 unique birds**: Yellow unlimited dash, purple sustained EMP, black/purple linger
3. 📊 **Dual score system**: Level + total, real-time accumulation
4. 🌐 **Trilingual instant switching**: No refresh needed, state preserved
5. 🔄 **Complete reset**: Restart button with confirmation protection
6. 📖 **Codex system**: Full 8-bird profiles in three languages
7. 📱 **Mobile optimized**: Touch, adaptive, anti-mis-touch
8. ⚡ **Physics stability**: Fixed timestep, high-precision solver, anti-clip

This is a **ready-to-play-in-browser** complete game — no server or external assets required, everything packed into a single HTML file.
