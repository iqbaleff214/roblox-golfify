# Golfify — Game Design Document

**Version:** 0.1  
**Platform:** Roblox  
**Genre:** Casual / Sports  
**Target Audience:** All ages (8+), casual players, competitive players  
**Max Players per Server:** 8  

---

## Table of Contents

1. [Vision & Concept](#1-vision--concept)
2. [Core Loop](#2-core-loop)
3. [Gameplay Mechanics](#3-gameplay-mechanics)
4. [Controls (Cross-Platform)](#4-controls-cross-platform)
5. [Game Modes](#5-game-modes)
6. [Course Design](#6-course-design)
7. [Progression & Retention](#7-progression--retention)
8. [Monetization](#8-monetization)
9. [Social & Multiplayer](#9-social--multiplayer)
10. [Localization](#10-localization)
11. [Asset Configuration](#11-asset-configuration)
12. [UI/UX](#12-uiux)
13. [Audio](#13-audio)
14. [Technical Notes](#14-technical-notes)
15. [Nuance & Feel](#15-nuance--feel)
16. [Out of Scope (v1)](#16-out-of-scope-v1)

---

## 1. Vision & Concept

**One-line pitch:** Mini-golf reimagined for Roblox — casual enough for anyone, deep enough for grinders.

**Core fantasy:** Player is a golfer traveling themed worlds, sinking impossible trick shots, unlocking flashy gear, and competing with friends for the lowest score.

**Differentiators from existing Roblox golf games:**
- Tight, satisfying shot feel (power indicator, arc preview, impact effects)
- Strong cosmetic identity (themed clubs, balls, trails, emotes)
- Async competition (ghost replays of friends' best runs)
- Live tournaments with leaderboards reset weekly

---

## 2. Core Loop

```
Enter Lobby
    │
    ▼
Select Course / Mode
    │
    ▼
Play Hole (aim → set power → shoot → ball resolves)
    │
    ├── Ball in hole → Score recorded → Next Hole
    │
    └── Max strokes reached → Hole ends (over par)
    │
    ▼
Course Complete → Score Summary
    │
    ▼
Rewards (XP, Coins, Star rating per hole)
    │
    ▼
Progression unlocks → Shop / Customize → Back to Lobby
```

**Session length target:** 5–15 minutes per course (9-hole courses ~8 min, 18-hole ~15 min).

**Dopamine hooks per hole:**
- Hole-in-one fireworks + announcement
- Near-miss particle effect on lip-out
- Streak bonus (3 pars in a row = bonus coins)
- Daily first-completion bonus per course

---

## 3. Gameplay Mechanics

### 3.1 Aiming

- Camera orbits ball; player rotates aim direction horizontally
- Aim arrow rendered on ground plane
- Optional "aim assist" toggle (default ON for mobile, OFF for PC)

### 3.2 Power

- Click/hold activates power bar; release fires
- Power bar oscillates (grows → shrinks → repeats) at medium speed
- Precise release timing is the core skill expression
- Power bar speed scales with hole difficulty tier

### 3.3 Ball Physics

- Roblox physics + custom velocity damping for consistent feel
- Spin system: tap left/right during power hold to apply side spin
- Bounce coefficient and roll friction configurable per surface material (see §11)
- Ball comes to full rest before next shot (or 3-second timeout → auto-rest)

### 3.4 Obstacles

| Type | Behavior |
|---|---|
| Windmill | Rotating blocker, timing-based |
| Moving platform | Horizontal/vertical slide, predictable period |
| Bumper | Elastic bounce, coefficient in config |
| Water hazard | Ball resets to last dry position, +1 stroke penalty |
| Sand trap | High friction zone, reduces roll distance |
| Ramp | Launches ball along incline normal |
| Teleporter | Ball exits at paired teleporter |
| Reverse gravity zone | Ball floats, gravity flipped |

### 3.5 Stroke Limit

- Each hole has a par value (2–6) and a max strokes cap (par + 3)
- Reaching cap auto-sinks ball (score = max cap)
- Prevents infinite-session griefing in multiplayer

---

## 4. Controls (Cross-Platform)

### 4.1 PC (Mouse + Keyboard)

| Action | Input |
|---|---|
| Rotate aim | Mouse move left/right |
| Set power (hold) | Left mouse button hold |
| Fire | Left mouse button release |
| Apply spin | A / D while holding LMB |
| Camera zoom | Scroll wheel |
| Camera reset | Middle mouse / R |
| Emote menu | E |
| Scoreboard | Tab |

### 4.2 Mobile (Touch)

| Action | Input |
|---|---|
| Rotate aim | One-finger drag on aim zone |
| Set power | Tap + hold power button |
| Fire | Release power button |
| Apply spin | Two-finger swipe during hold |
| Camera | Two-finger drag (pan) / pinch (zoom) |
| Emote menu | Emote button (bottom-right) |
| Scoreboard | Scoreboard button (top-right) |

**Mobile UX rules:**
- All interactive buttons ≥ 44px hit area
- Power bar vertical on mobile (thumb-friendly)
- Aim assist enabled by default; player can disable in Settings
- No input that requires simultaneous mouse + keyboard equivalent

### 4.3 Console (Gamepad)

| Action | Input |
|---|---|
| Rotate aim | Left stick |
| Power bar | Right trigger (hold → release) |
| Apply spin | Left trigger (hold) + left stick |
| Camera | Right stick |
| Emote menu | D-pad up |
| Scoreboard | Select / View |

**All three platforms must reach full feature parity before launch.**

---

## 5. Game Modes

### 5.1 Stroke Play (default)
Fewest total strokes across all holes wins. Standard scoring. Available solo and multiplayer.

### 5.2 Quickplay / Casual
No scoring pressure. XP and coins reduced 50%. Infinite retries per hole. Good for course exploration.

### 5.3 Race
First player to sink on each hole scores a point. Simultaneous turns. Most points wins. 9-hole only.

### 5.4 Daily Challenge
One randomly seeded course per day. Global leaderboard. One attempt per account per day. Rewards exclusive badge.

### 5.5 Weekly Tournament
Bracket-style across 7 days. 18-hole course, fixed seed. Top 3 get exclusive cosmetic reward. Resets Sunday midnight UTC.

### 5.6 Practice Mode
Solo only. Free retry, no scoring, no rewards. Used for learning courses.

---

## 6. Course Design

### 6.1 Themes

Each theme is a self-contained visual world with matching obstacles, terrain materials, ambient audio, and skybox.

| Theme | Setting | Unique mechanic |
|---|---|---|
| Classic Green | Traditional golf | Bunkers, rough, water |
| Space Station | Zero-gravity zones | Gravity flipper pads |
| Pirate Cove | Ocean cliffs, ships | Cannon launchers |
| Candy Land | Sweet factory | Sticky surfaces, bouncy floors |
| Ancient Ruins | Temple maze | Pressure plate doors |
| Neon City | Cyberpunk rooftops | Speed boost rails |

### 6.2 Hole Anatomy

- **Tee box** — flat start zone, ball spawns here
- **Fairway** — main path, normal friction
- **Green** — area around hole, low friction, subtle slope
- **Hazards** — water, sand, OOB zones
- **Obstacles** — themed mechanical elements
- **Hole** — sinkable part, slightly magnetized at < 0.5 studs radius

### 6.3 Difficulty Tiers

| Tier | Par range | Obstacles | Unlock |
|---|---|---|---|
| Beginner | 2–3 | 0–1 moving | Default |
| Intermediate | 3–4 | 2–3 | Level 5 |
| Advanced | 4–5 | 3–5, precise timing | Level 15 |
| Expert | 5–6 | Complex, multiple | Level 30 |

### 6.4 Course Construction Rules

- Minimum sightline to hole from tee (player can always see the goal or a clear path)
- No impossible shots — every hole must be solvable in par by a skilled player
- Holes playtested on all three control schemes before shipping
- Each hole has at least one "trick shot" angle discoverable by experimentation

---

## 7. Progression & Retention

### 7.1 Player Level (XP)

- XP earned per hole: base XP × difficulty multiplier × score multiplier
- Score multiplier: Hole-in-one 3×, Birdie 2×, Par 1×, Bogey 0.5×, Over 0.1×
- Level gates: cosmetic unlocks, new course themes, new game modes

### 7.2 Stars

- Each hole awards 0–3 stars based on score vs par
- Stars per course summed for global profile display
- Milestone star totals unlock exclusive items

### 7.3 Daily / Weekly Quests

Examples:
- "Sink 3 holes-in-one today" → 200 coins
- "Complete any 18-hole course" → 500 XP
- "Win a Race mode match" → exclusive avatar item rental (3 days)

### 7.4 Streak System

- Login streak tracked; day 7 = premium reward
- Hole-in-a-row streak during a round: +50 coins per consecutive hole-in-one

### 7.5 Leaderboards

- Per-course best score (all-time + weekly)
- Friends leaderboard (prioritized display)
- Ghost replay of top score available to watch before playing

---

## 8. Monetization

**Philosophy:** Cosmetic-only. Zero pay-to-win. No gameplay advantage purchasable.

### 8.1 Currency

| Currency | Earn | Spend |
|---|---|---|
| Coins | Gameplay, quests, daily login | Basic shop items, ball trails |
| Gems | Robux purchase, rare quest reward | Premium shop, exclusive bundles |

Exchange rate: 80 Robux = 100 Gems (example; tune to Roblox economy norms).

### 8.2 Shop Categories

| Category | Examples | Currency |
|---|---|---|
| Golf balls | Color, pattern, glow, material skin | Coins / Gems |
| Club skins | Handle wrap, head design, holographic | Coins / Gems |
| Ball trails | Flame, rainbow, stardust, bubble | Gems |
| Emotes | Celebrate dance, fail reaction, taunt | Gems |
| Caddies | Companion pet that follows player | Gems / Robux bundle |
| Course pass | Early access to new theme | Robux |
| VIP Pass (GamePass) | +25% XP, +25% coins, exclusive lobby area | Robux (one-time) |

### 8.3 VIP Game Pass (Robux, one-time)

- 25% XP and coin boost (stacks with quests)
- Exclusive VIP lobby lounge with private practice area
- VIP badge on scoreboard and chat
- Monthly exclusive cosmetic drop (auto-granted)

### 8.4 Battle Pass (Seasonal)

- 50 tiers per season (~60 days)
- Free track: coins, XP boosts, basic items
- Premium track (Robux): exclusive cosmetics, emotes, caddie, seasonal title
- Seasonal theme ties into new course release

### 8.5 Limited Items

- Rotating limited cosmetics (48-hour window)
- Holiday event bundles (Christmas, Halloween, Summer)
- Drives FOMO without being predatory — items are cosmetic only

### 8.6 Revenue Targets (design guidance, not hard goals)

- Primary: Battle Pass (recurring, predictable)
- Secondary: VIP GamePass (one-time, high conversion)
- Tertiary: Limited items, direct Gem purchases

---

## 9. Social & Multiplayer

### 9.1 Lobby

- Shared lobby space with practice putting green
- Players visible to each other, can emote, chat
- Course selection board (vote system in public lobbies)

### 9.2 Private Servers

- Standard Roblox private server (Robux or free based on policy)
- Host can lock course, mode, settings

### 9.3 Party System

- Party leader invites up to 7 friends
- Party teleports together to selected course
- Party chat channel persists across teleports

### 9.4 Ghost Replays

- Top 3 scores per course stored as ghost data
- Player's personal best ghost always available
- Ghosts rendered as translucent ball with trail, no collision

### 9.5 Spectator Mode

- After finishing course, player can spectate remaining players
- Spectators can use reaction emotes (visible as floating icons)

---

## 10. Localization

### 10.1 Supported Languages (launch)

| Language | Code | Notes |
|---|---|---|
| English | en | Source language |
| Indonesian | id | Developer's locale, high Roblox usage |
| Spanish | es | Large Roblox demographic |
| Portuguese (BR) | pt-BR | Brazil = top Roblox market |
| French | fr | EU coverage |
| German | de | EU coverage |
| Russian | ru | Large Roblox player base |
| Chinese (Simplified) | zh-CN | Growing market |

Post-launch priority: Japanese (`ja`), Thai (`th`), Turkish (`tr`).

### 10.2 Localization System

- All user-facing strings stored in `LocalizationTable` (Roblox built-in)
- String keys use namespace prefix: `ui.button.play`, `hole.name.windmill_valley`
- No hardcoded strings in UI scripts — all via `LocalizationService:GetTranslator()`
- Numeric formatting respects locale (decimal separator, thousands separator)
- Date/time displayed in player's local timezone

### 10.3 Cultural Sensitivity

- No themes that reference specific religious symbols
- Golf terminology localized (not transliterated) where natural local equivalent exists
- Avatar items reviewed per market before release

### 10.4 Right-to-Left (RTL)

- Arabic / Hebrew not in v1 scope but UI layout must not hard-code LTR assumptions
- Use anchored layouts, not absolute pixel offsets, to allow future RTL flip

---

## 11. Asset Configuration

> **Rule: All tunable values live in one config file per domain. No magic numbers in gameplay scripts.**

### 11.1 Config File Structure

```
src/
  shared/
    config/
      BallConfig.lua       -- ball physics, materials, cosmetic IDs
      CourseConfig.lua     -- course metadata, hole par values, difficulty
      ObstacleConfig.lua   -- obstacle parameters (speed, coefficient, period)
      ShopConfig.lua       -- item definitions, prices, bundle contents
      ProgressionConfig.lua -- XP tables, level thresholds, reward mapping
      AudioConfig.lua      -- sound IDs, volumes, pitch ranges
      MonetizationConfig.lua -- currency exchange rates, GamePass IDs, product IDs
      UIConfig.lua         -- colors, font sizes, layout constants
      LocalizationConfig.lua -- supported locales, fallback chain
```

### 11.2 BallConfig.lua (example shape)

```lua
return {
  Physics = {
    DefaultBounce     = 0.4,   -- CoefficientOfRestitution
    DefaultFriction   = 0.5,
    RestThreshold     = 0.05,  -- studs/s below which ball is "at rest"
    AutoRestTimeout   = 3,     -- seconds before forced rest
    MaxPowerVelocity  = 80,    -- studs/s at full power bar
    SpinMultiplier    = 0.3,
  },
  Surfaces = {
    Fairway  = { Friction = 0.5,  Bounce = 0.3 },
    Green    = { Friction = 0.3,  Bounce = 0.2 },
    Sand     = { Friction = 0.85, Bounce = 0.1 },
    Ice      = { Friction = 0.05, Bounce = 0.5 },
    Rubber   = { Friction = 0.6,  Bounce = 0.8 },
    Water    = nil,  -- triggers hazard, no physics
  },
}
```

### 11.3 MonetizationConfig.lua (example shape)

```lua
return {
  Robux = {
    VIPGamePassId   = 0,  -- fill before launch
    BattlePassId    = 0,
  },
  GemProducts = {
    { ProductId = 0, Gems = 100,  Robux = 80  },
    { ProductId = 0, Gems = 550,  Robux = 400 },
    { ProductId = 0, Gems = 1200, Robux = 800 },
  },
  CoinToGemRate = nil,  -- coins NOT convertible to gems (one-way economy)
  VIPBoostXP    = 0.25,
  VIPBoostCoins = 0.25,
}
```

### 11.4 Config Access Pattern

Scripts must import config via a central accessor, never `require` config files directly from multiple places without a wrapper:

```lua
-- shared/ConfigService.lua
local Config = {
  Ball         = require(script.Parent.config.BallConfig),
  Course       = require(script.Parent.config.CourseConfig),
  Obstacle     = require(script.Parent.config.ObstacleConfig),
  Shop         = require(script.Parent.config.ShopConfig),
  Progression  = require(script.Parent.config.ProgressionConfig),
  Audio        = require(script.Parent.config.AudioConfig),
  Monetization = require(script.Parent.config.MonetizationConfig),
  UI           = require(script.Parent.config.UIConfig),
  Localization = require(script.Parent.config.LocalizationConfig),
}
return Config
```

---

## 12. UI/UX

### 12.1 HUD (in-round)

```
┌─────────────────────────────────────────┐
│ [Hole 3/9]  Par 3   Stroke: 2     [⏱]  │
│                                          │
│              [3D view]                   │
│                                          │
│ [Aim zone]         [Power btn] [Spin]   │
│ Leaderboard mini                        │
└─────────────────────────────────────────┘
```

- HUD hides non-essential elements during shot for clean focus
- Scoreboard auto-collapses; expand via button

### 12.2 Menus

- Lobby → Course Select → Mode Select → Ready → Load
- All menus accessible with back button (no dead ends)
- Settings: Audio, Controls, Graphics Quality, Aim Assist toggle, Language

### 12.3 Feedback Principles

- Every shot has: visual (trail, impact effect), audio (swing, contact, roll)
- Hole-in-one: full-screen celebration, sound, server announcement
- Near-miss lip-out: specific audio cue + "ooh" reaction from server
- Score delta shown immediately after sinking (+1, -1, HIO)

---

## 13. Audio

All IDs in `AudioConfig.lua`.

| Event | Sound |
|---|---|
| Club swing | Whoosh (varies by power) |
| Ball impact (grass) | Soft thud |
| Ball impact (hard surface) | Sharp click |
| Ball in hole | Plunk + crowd cheer |
| Hole-in-one | Fanfare |
| Water hazard | Splash |
| Power bar full | Tension sting |
| UI click | Soft tap |
| Background music | Theme per course world, loops |

- Music volume separate from SFX in settings
- Ambient sounds per theme (birds, space ambience, waves)
- No autoplaying audio on Roblox main menu (follows Roblox audio policy)

---

## 14. Technical Notes

### 14.1 Architecture

- **Client:** Rendering, input, local UI, camera, ball trail FX
- **Server:** Authoritative physics resolution, score recording, anti-cheat
- **Shared:** Config, types, utility modules

Ball shot fired client-side for responsiveness → server validates final position → reconcile if delta > threshold.

### 14.2 Anti-Cheat

- Server owns score; client cannot write scores
- Shot power capped server-side (cannot exceed `BallConfig.Physics.MaxPowerVelocity`)
- Stroke count validated server-side per hole

### 14.3 Data Persistence

- `DataStoreService` for: player level, XP, coins, gems, inventory, quest progress, course bests
- Auto-save on round end and lobby entry
- Backup store write on first save failure

### 14.4 Performance

- LOD system for course props at distance
- Ball trail particle count limited on mobile (configurable in `UIConfig`)
- Max 8 players per server; physics complexity bounded
- Course loads via streaming (not full model load on join)

---

## 15. Nuance & Feel

These details separate a game that feels good from one that just works.

- **Power bar tempo:** Slightly non-linear — fast at extremes, slower in middle — rewarding precise release timing without making it frustrating
- **Ball magnet radius:** Small (0.5 studs) so holes-in-one feel earned, not gifted
- **Camera lag:** Slight follow delay (0.1s lerp) makes ball movement feel weighty
- **Spin feedback:** Ball visually spins in direction of applied spin during flight
- **Lip-out:** If ball circles the rim without sinking, play specific animation and sound — feels cinematic
- **Course ambience:** Wind particles and ambient sound react to course theme; subtle movement makes static courses feel alive
- **Multiplayer turns:** In stroke play, all players shoot simultaneously (no waiting turns) — reduces dead time, keeps energy up
- **Failure softening:** Over-par finish has no harsh sound or penalty UI; tone stays lighthearted
- **Hole preview:** 3-second flyover camera of hole before tee shot on first play — skippable
- **Victory screen:** Shows stroke-by-stroke replay of player's best hole; shareable screenshot frame

---

## 16. Out of Scope (v1)

Features explicitly deferred to avoid scope creep:

- Course creator / user-generated levels
- AR mode
- Ranked / ELO matchmaking
- Clan / guild system
- Real-money auction house
- Voice chat integration
- Replay export to video
- Arab / Hebrew RTL layout

---

*Document maintained by: M. Iqbal Effendi*  
*Last updated: 2026-05-24*
