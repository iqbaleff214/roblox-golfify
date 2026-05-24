# Golfify — Game Design Document

**Version:** 0.1  
**Platform:** Roblox  
**Genre:** Casual / Sports / Multiplayer  
**Target Audience:** All ages (8+), casual and competitive players  
**Max Players per Server:** 8  

---

## Table of Contents

1. [Vision & Concept](#1-vision--concept)
2. [Core Loop](#2-core-loop)
3. [Gameplay Mechanics](#3-gameplay-mechanics)
4. [Controls (Cross-Platform)](#4-controls-cross-platform)
5. [Game Modes](#5-game-modes)
6. [Course Design](#6-course-design)
7. [Chaos Modifiers](#7-chaos-modifiers)
8. [Progression & Retention](#8-progression--retention)
9. [Monetization](#9-monetization)
10. [Social & Multiplayer](#10-social--multiplayer)
11. [Localization](#11-localization)
12. [Asset Configuration](#12-asset-configuration)
13. [UI/UX](#13-uiux)
14. [Audio](#14-audio)
15. [Technical Notes](#15-technical-notes)
16. [Nuance & Feel](#16-nuance--feel)
17. [Out of Scope (v1)](#17-out-of-scope-v1)

---

## 1. Vision & Concept

**One-line pitch:** Creative, unpredictable mini golf for everyone — easy to pick up, satisfying to master.

**Core fantasy:** A golfer traveling wild themed worlds, pulling off perfect trick shots, reacting to chaotic course events, and competing with friends across increasingly creative obstacle courses.

**Design pillars:**
1. **Accessible** — anyone can play a hole in 30 seconds without a tutorial
2. **Unpredictable** — every round feels fresh via chaos modifiers, event hazards, and dynamic obstacles
3. **Social** — spectating, reacting, and competing is as fun as playing
4. **Mastery ceiling** — optimal angles, spin, and trick shots reward deep play without gating casual players

**Differentiators:**
- Wide variety of themed environments, each with unique gimmicks, not just reskins
- Optional chaos modifiers create memorable, funny moments
- Secret shortcuts discoverable through experimentation
- Zero pay-to-win; cosmetic depth drives long-term spending

---

## 2. Core Loop

```
Play Courses
    │
    ▼
Earn Rewards (Coins, XP, Stars)
    │
    ▼
Unlock Cosmetics
    │
    ▼
Master Difficult Maps
    │
    ▼
Compete With Friends
    │
    ▼
Discover New Challenges ──► back to Play Courses
```

### Per-Round Loop

```
Lobby (socialize, customize, vote on course)
    │
    ▼
Hole Preview (3s flyover, skippable)
    │
    ▼
Aim → Set Power → Shoot → Ball Resolves
    │
    ├── Sinks → Score recorded → XP/coin pulse → Next Hole
    └── Max strokes hit → Auto-sink (over par) → Next Hole
    │
    ▼
Course Complete → Score Summary → Reward Screen
    │
    ▼
Return to Lobby
```

**Session length target:** 5–15 min per course (9-hole ~8 min, 18-hole ~15 min).

**Dopamine hooks per hole:**
- Hole-in-one: full server announcement + fireworks
- Near-miss lip-out: cinematic slow-mo + crowd "ooh"
- Trick shot detection: shows "Trick Shot!" overlay when ball bounces off 2+ surfaces
- Shortcut discovery: first-time bonus coins for finding secret path
- Streak bonus: 3 consecutive pars or better → +50 coins

---

## 3. Gameplay Mechanics

### 3.1 Aiming

- Camera orbits ball; player rotates aim arrow horizontally
- Arrow rendered on ground with distance guide dots
- Optional **Aim Assist** toggle (default ON for mobile, OFF for PC/console)
- Trajectory preview arc shows for first ~30% of projected path (not full — preserves skill)

### 3.2 Power

- Hold input activates oscillating power bar; release fires
- Bar grows → shrinks → repeats at medium tempo
- Slight non-linear speed: fast at extremes, slightly slower in middle (rewards precision)
- Power level capped server-side (no client exploit bypass)

### 3.3 Ball Physics

- Roblox physics engine + custom damping for consistent, satisfying roll
- **Spin system:** hold spin input + directional input during power-hold applies side/back/topspin
- Spin visible as ball rotation in flight
- Surface material determines friction and bounce (all values in `BallConfig.lua`)
- Ball "at rest" when velocity < threshold for 0.5s, or after 3s timeout (auto-rest)

### 3.4 Obstacles & Hazards

| Type | Behavior |
|---|---|
| Moving platform | Horizontal/vertical slide, predictable period |
| Rotating barrier (windmill) | Timing-based blocker, variable RPM by hole |
| Jump pad | Launches ball on contact, fixed angle and force |
| Portal | Ball exits at paired portal, preserves velocity direction |
| Wind zone | Applies constant lateral force to airborne ball |
| Collapsing bridge | Activates on ball contact, resets after 5s |
| Gravity flipper | Reverses gravity within zone boundary |
| Bumper | Elastic bounce, configurable coefficient |
| Water hazard | Ball resets to last dry position, +1 stroke penalty |
| Sand trap | High friction, kills roll |
| Ramp | Launches ball along incline normal |
| Secret shortcut | Hidden path that bypasses obstacles — rewards exploration |

### 3.5 Trick Shot System

A trick shot is detected when the ball contacts 2+ non-green surfaces before sinking.

| Trick | Condition | Bonus |
|---|---|---|
| Bank Shot | 1 wall bounce before sink | +10 coins |
| Double Bank | 2 wall bounces before sink | +25 coins |
| Ricochet | Ball contacts obstacle then sinks | +20 coins |
| Portal Ace | Sink via portal on first try | +50 coins |
| Gravity Ace | Hole-in-one through gravity zone | +75 coins |

### 3.6 Stroke Limit

- Each hole has a par value (2–6) and a max strokes cap (par + 3)
- Reaching cap auto-sinks ball; score = cap value
- Prevents stalling in multiplayer matches

---

## 4. Controls (Cross-Platform)

All three platforms must reach full feature parity before launch. No feature locked to one platform.

### 4.1 PC (Mouse + Keyboard)

| Action | Input |
|---|---|
| Rotate aim | Mouse move left/right |
| Hold power | Left mouse button hold |
| Fire | Left mouse button release |
| Apply spin | A / D while holding LMB |
| Camera zoom | Scroll wheel |
| Camera reset | R |
| Emote menu | E |
| Scoreboard | Tab |
| Settings | Escape |

### 4.2 Mobile (Touch)

| Action | Input |
|---|---|
| Rotate aim | One-finger drag on aim zone (left half of screen) |
| Hold power | Tap + hold power button (bottom-right) |
| Fire | Release power button |
| Apply spin | Two-finger swipe during hold |
| Camera pan | Two-finger drag |
| Camera zoom | Pinch gesture |
| Emote menu | Emote button (bottom-right corner) |
| Scoreboard | Scoreboard icon (top-right) |

**Mobile UX rules:**
- All interactive buttons ≥ 44px tap target
- Power bar rendered vertically on mobile (thumb-friendly)
- Aim Assist ON by default; disable in Settings
- No input combination that requires simultaneous mouse + keyboard equivalent

### 4.3 Console (Gamepad)

| Action | Input |
|---|---|
| Rotate aim | Left stick |
| Hold power | Right trigger (hold → release) |
| Apply spin | Left trigger (hold) + left stick direction |
| Camera | Right stick |
| Camera zoom | D-pad up/down |
| Emote menu | D-pad left |
| Scoreboard | Select / View |
| Settings | Start / Menu |

---

## 5. Game Modes

### 5.1 Stroke Play (Default)
Fewest total strokes across all holes wins. Standard scoring. Available solo and multiplayer (up to 8 players). All players shoot simultaneously — no turn-waiting.

### 5.2 Casual / Quickplay
No competitive scoring. Infinite retries per hole. XP and coins reduced 50%. Ideal for course exploration and socializing.

### 5.3 Race Mode
First to sink each hole scores a point. Simultaneous turns. Most points after 9 holes wins. Tie on points → compare total strokes.

### 5.4 Daily Challenge
One randomly seeded course per day. Global leaderboard. One attempt per account. Exclusive badge for top 10 finishers. Resets midnight UTC.

### 5.5 Weekly Tournament
18-hole course, fixed seed, 7-day window. Bracket-style; top 3 earn exclusive seasonal cosmetic. Resets Sunday midnight UTC.

### 5.6 Chaos Mode
Standard stroke play with active **Chaos Modifiers** (see §7). Opt-in per lobby. Rewards standard XP/coins + chaos bonus if modifier was active all round.

### 5.7 Practice Mode
Solo, free retry, no rewards. Used for learning holes, testing trick shots, or exploring shortcuts.

---

## 6. Course Design

### 6.1 Themes

Each theme is a self-contained visual world with matching obstacles, terrain materials, ambient audio, skybox, and unique gameplay gimmick.

| Theme | Setting | Unique Gimmick |
|---|---|---|
| Peaceful Forest | Calm woodland | Windmills, leaf blowers, log ramps |
| Tropical Island | Beach paradise | Wave surge zones, coconut cannons |
| Haunted Village | Spooky graveyard | Ghost bumpers, gravestones that rise |
| Snowy Mountain | Alpine slopes | Ice surfaces, avalanche hazard, snowdrift sand traps |
| Floating Sky Islands | Clouds, sky bridges | Wind currents, sky falls (OOB fast) |
| Volcano Arena | Lava world | Heat updraft zones, lava hazard replacing water |
| Fantasy Castle | Medieval | Drawbridge collapse, catapult launchers |
| Neon Cyber World | Cyberpunk city | Speed boost rails, laser walls, gravity zones |
| Mysterious Night | Moonlit courses | Reduced visibility, glow-in-dark hazards |

### 6.2 Hole Anatomy

- **Tee box** — flat start, ball spawns here, camera previews hole
- **Fairway** — main path, standard friction
- **Green** — area around hole, low friction, subtle directional slope
- **Hazards** — water/lava, sand, OOB zones, theme-specific traps
- **Obstacles** — theme-matched mechanical elements
- **Shortcuts** — hidden paths discoverable by experimentation, bonus coins on first find
- **Hole** — small magnet pull at < 0.5 studs radius so sinks feel earned

### 6.3 Difficulty Tiers

| Tier | Par Range | Obstacles | Unlock |
|---|---|---|---|
| Beginner | 2–3 | 0–1 moving | Default |
| Intermediate | 3–4 | 2–3 mixed | Level 5 |
| Advanced | 4–5 | 3–5, precise timing required | Level 15 |
| Expert | 5–6 | Complex, multi-obstacle, tight margins | Level 30 |

### 6.4 Course Construction Rules

- Every hole must be completable at par by a skilled player — no impossible shots
- Player must always see hole or a clear directional clue from tee box
- At least one discoverable trick shot angle per hole
- At least one secret shortcut per course (not per hole)
- Every hole playtested on all three control schemes before release
- Holes must be fun to watch as a spectator (obstacle visibility matters)

---

## 7. Chaos Modifiers

Optional, opt-in per lobby. Host enables before match starts. Creates unpredictable, funny moments.

| Modifier | Effect |
|---|---|
| Low Gravity | Ball floats slowly, air time x3 |
| Slippery World | All surfaces reduced friction to near-zero |
| Giant Fans | Large fans placed randomly across course, active during shots |
| Moving Hazards | All static obstacles gain slow random movement |
| Tiny Ball | Ball hitbox 50% smaller, rolls faster |
| Big Ball | Ball hitbox 200% larger, knocks obstacles |
| Random Wind | Gusts fire in random directions every 5s |
| Surprise Events | Random event triggers mid-hole (quake, rain, disco lights + slippery) |

- Maximum 2 modifiers active simultaneously (performance bound, clarity bound)
- Chaos matches award standard coins/XP + 10% chaos bonus
- Chaos Mode has its own weekly leaderboard (separate from clean stroke play)

---

## 8. Progression & Retention

### 8.1 Player Level (XP)

XP per hole = `BaseXP × DifficultyMultiplier × ScoreMultiplier`

| Score | Multiplier |
|---|---|
| Hole-in-one | 3× |
| Eagle (par - 2) | 2.5× |
| Birdie (par - 1) | 2× |
| Par | 1× |
| Bogey (par + 1) | 0.5× |
| Double bogey+ | 0.1× |

Level gates unlock: new course themes, game modes, cosmetic slots, seasonal challenges.

### 8.2 Stars

- Each hole: 0–3 stars based on strokes vs par
- Stars totaled per course for profile display
- Milestone totals (50, 150, 300, 500 stars) unlock exclusive permanent items

### 8.3 Shortcut Discovery

- First time ball travels a secret shortcut path → popup "Shortcut Discovered!" + 100 coins
- Tracked per player per shortcut; can only earn once per shortcut

### 8.4 Daily / Weekly Quests

Daily examples:
- "Sink 3 holes-in-one" → 200 coins
- "Complete 2 different themed courses" → 300 XP
- "Trigger a trick shot" → 150 coins

Weekly examples:
- "Win a Race mode match" → exclusive avatar item rental (3 days)
- "Complete the Daily Challenge 5 days this week" → rare cosmetic crate
- "Find 2 secret shortcuts" → 1000 coins

### 8.5 Streak System

- Login streak tracked; day 7 = premium drop
- Hole-in-a-row within a round: +50 coins per consecutive HIO after the first

### 8.6 Seasonal Events

- Tied to real-world calendar (Halloween, Christmas, Summer)
- Event-exclusive courses active for duration
- Limited cosmetics, badges, titles available only during event window

### 8.7 Leaderboards

- Per-course best score: all-time and weekly
- Friends leaderboard (prioritized in display)
- Ghost replay of top 3 scores available before playing hole
- Chaos Mode separate weekly leaderboard

---

## 9. Monetization

**Philosophy:** Cosmetic-only. Zero pay-to-win. No gameplay stat, speed, or advantage purchasable with real money.

### 9.1 Currency

| Currency | Earn | Spend |
|---|---|---|
| Coins | Gameplay, quests, login, shortcuts | Basic shop items |
| Gems | Robux purchase, rare quest reward | Premium items, bundles |

One-way economy: coins cannot convert to gems. Prevents pay-to-win bypass.

### 9.2 Cosmetic Categories

| Category | Examples | Currency |
|---|---|---|
| Golf balls | Color, pattern, glow, holographic, animated | Coins / Gems |
| Club skins | Handle color, head design, particle on swing | Coins / Gems |
| Ball trails | Flame, rainbow, stardust, bubble, lightning | Gems |
| Hit effects | Explosion, sparkle, confetti burst on impact | Gems |
| Shot animations | Wind-up style, power pose, comedic swing | Gems |
| Emotes | Celebrate dance, fail reaction, taunt, fist pump | Gems |
| Caddies | Companion pet that follows player on course | Gems / Bundle |
| Titles | Displayed under username ("Trick Shot King") | Quest / Event |
| Course Pass | Early access to upcoming theme | Robux |

### 9.3 VIP Game Pass (Robux, one-time)

- +25% XP and +25% coin gain (stacks with quests)
- Exclusive VIP lounge in lobby with private practice putting green
- VIP badge on scoreboard and above head
- Monthly exclusive cosmetic drop (auto-granted on purchase month + each subsequent month active)

### 9.4 Battle Pass (Seasonal, ~60 days)

- 50 tiers per season
- Free track: coins, XP boosts, basic cosmetics
- Premium track (Robux): exclusive skins, emotes, caddie, trail, seasonal title
- Seasonal theme matches current event course release

### 9.5 Limited Items

- Rotating 48-hour limited cosmetics
- Holiday bundles (Halloween, Christmas, Summer Splash)
- Items never return after window closes — drives FOMO without being predatory
- All limited items are cosmetic only

### 9.6 Cosmetic Crates (optional, post-launch)

- Earned via gameplay or purchased with gems
- Fixed drop table published publicly (no hidden odds)
- Duplicate protection: 5th duplicate converts to coins
- No Robux direct-to-crate path to comply with Roblox UGC policies

---

## 10. Social & Multiplayer

### 10.1 Lobby

- Shared cozy lobby space with a practice putting green, benches, and ambient music
- Players visible, can emote, chat, inspect others' cosmetics
- Course vote board: players vote on next course; most votes wins (host override available)
- VIP area for GamePass holders (separate lounge connected to main lobby)

### 10.2 Simultaneous Play

All players shoot on the same turn simultaneously — no waiting. Reduces dead time, increases energy, makes spectating more interesting.

### 10.3 Spectating & Reactions

- After finishing course, player enters Spectator Mode
- Camera follows active players (cycle with button)
- Spectators see floating reaction icons: 👏 🤣 😮 😬 🔥
- Reactions visible in-world above spectator and over ball on impact

### 10.4 Party System

- Party leader invites up to 7 friends
- Party teleports together to selected course
- Party chat channel persists across teleports and course loads

### 10.5 Ghost Replays

- Top 3 scores per course stored as ghost playback data
- Player's personal best ghost always available
- Ghosts render as translucent ball with trail; no collision with live ball

### 10.6 Private Servers

- Standard Roblox private server (Robux or free per platform policy)
- Host sets: course, mode, chaos modifiers, stroke limit override

### 10.7 Social Hooks Per Shot

- Ball approaches lip without sinking → slow-mo camera + "oh no" sound
- Trick shot detected → "TRICK SHOT!" overlay for all spectators
- Hole-in-one → server-wide announcement banner + fireworks
- Shortcut found → local discovery animation, quiet (doesn't spoil for others)

---

## 11. Localization

### 11.1 Supported Languages (Launch)

| Language | Code | Notes |
|---|---|---|
| English | en | Source language |
| Indonesian | id | Developer locale, strong Roblox market |
| Spanish | es | Large Roblox demographic (LatAm + ES) |
| Portuguese (BR) | pt-BR | Brazil = top Roblox market |
| French | fr | EU coverage |
| German | de | EU coverage |
| Russian | ru | Large Roblox player base |
| Chinese Simplified | zh-CN | Growing market |

Post-launch priority: Japanese (`ja`), Thai (`th`), Turkish (`tr`), Korean (`ko`).

### 11.2 Localization System

- All user-facing strings in Roblox `LocalizationTable`
- String keys use namespace prefix: `ui.button.play`, `hole.name.windmill`, `hud.label.stroke`
- No hardcoded strings in UI scripts — always via `LocalizationService:GetTranslator()`
- Numeric formatting respects locale (decimal/thousands separators)
- Date/time in player's local timezone
- Golf terminology localized where a natural local equivalent exists; not just transliterated

### 11.3 Cultural Sensitivity

- No themes referencing specific religious symbols
- Avatar items reviewed per major market before release
- Humor in chaos modifiers reviewed for cultural neutrality

### 11.4 RTL Preparedness

- Arabic/Hebrew not in v1 scope
- UI layout must use anchored/relative positioning — no hardcoded LTR pixel offsets
- Allows future RTL flip without layout rewrite

---

## 12. Asset Configuration

> **Rule: All tunable values live in one config file per domain. No magic numbers in gameplay or UI scripts. All asset IDs centralized.**

### 12.1 Config File Structure

```
src/
  shared/
    config/
      BallConfig.lua          -- ball physics per surface, spin, rest threshold
      CourseConfig.lua        -- course metadata, hole par values, difficulty tiers
      ObstacleConfig.lua      -- obstacle speed, bounce coefficient, period, force values
      ChaosConfig.lua         -- modifier definitions, strength values, max active count
      ShopConfig.lua          -- item definitions, prices, bundle contents, Gem product IDs
      ProgressionConfig.lua   -- XP formula, level thresholds, reward table, streak config
      AudioConfig.lua         -- all sound asset IDs, volumes, pitch ranges
      MonetizationConfig.lua  -- Robux product IDs, GamePass IDs, currency exchange rates
      UIConfig.lua            -- colors, font sizes, layout constants, particle limits
      LocalizationConfig.lua  -- supported locales, fallback chain
      TrickShotConfig.lua     -- trick shot detection rules, reward values
```

### 12.2 BallConfig.lua (example shape)

```lua
return {
  Physics = {
    DefaultBounce     = 0.4,   -- CoefficientOfRestitution
    DefaultFriction   = 0.5,
    RestThreshold     = 0.05,  -- studs/s below which ball is "at rest"
    AutoRestTimeout   = 3,     -- seconds before forced rest
    MaxPowerVelocity  = 80,    -- studs/s at full power bar
    SpinMultiplier    = 0.3,
    MagnetRadius      = 0.5,   -- studs; hole magnet pull radius
  },
  Surfaces = {
    Fairway  = { Friction = 0.50, Bounce = 0.30 },
    Green    = { Friction = 0.30, Bounce = 0.20 },
    Sand     = { Friction = 0.85, Bounce = 0.10 },
    Ice      = { Friction = 0.05, Bounce = 0.50 },
    Rubber   = { Friction = 0.60, Bounce = 0.80 },
    SpeedRail= { Friction = 0.01, Bounce = 0.10, SpeedBoost = 1.5 },
    Water    = nil,  -- triggers hazard reset, no physics
    Lava     = nil,  -- triggers hazard reset (volcano theme), no physics
  },
}
```

### 12.3 MonetizationConfig.lua (example shape)

```lua
return {
  GamePasses = {
    VIPPassId       = 0,  -- fill before launch
    BattlePassId    = 0,
  },
  GemProducts = {
    { ProductId = 0, Gems = 100,  Robux = 80  },
    { ProductId = 0, Gems = 550,  Robux = 400 },
    { ProductId = 0, Gems = 1200, Robux = 800 },
  },
  CoinToGemRate = nil,   -- coins NOT convertible to gems (one-way economy)
  VIPBoostXP    = 0.25,  -- 25% bonus
  VIPBoostCoins = 0.25,
}
```

### 12.4 ChaosConfig.lua (example shape)

```lua
return {
  MaxActiveModifiers = 2,
  ChaosRewardBonus   = 0.10,  -- 10% extra coins/XP when modifier active all round
  Modifiers = {
    LowGravity    = { GravityScale = 0.3, AirTimeMultiplier = 3.0 },
    SlipperyWorld = { GlobalFrictionOverride = 0.02 },
    GiantFans     = { FanCount = 3, FanForce = 40, RandomPlacement = true },
    TinyBall      = { SizeScale = 0.5, SpeedMultiplier = 1.2 },
    BigBall       = { SizeScale = 2.0, KnocksObstacles = true },
    RandomWind    = { GustInterval = 5, GustForce = 25, RandomDirection = true },
  },
}
```

### 12.5 AudioConfig.lua (example shape)

```lua
return {
  SFX = {
    Swing       = { Id = 0, Volume = 0.8, PitchRange = { 0.9, 1.1 } },
    ImpactGrass = { Id = 0, Volume = 0.7 },
    ImpactHard  = { Id = 0, Volume = 0.8 },
    BallInHole  = { Id = 0, Volume = 1.0 },
    HoleInOne   = { Id = 0, Volume = 1.0 },
    Splash      = { Id = 0, Volume = 0.9 },
    LipOut      = { Id = 0, Volume = 0.8 },
    TrickShot   = { Id = 0, Volume = 0.9 },
    UIClick     = { Id = 0, Volume = 0.5 },
    PowerFull   = { Id = 0, Volume = 0.6 },
  },
  Music = {
    -- one entry per theme
    Forest      = { Id = 0, Volume = 0.4, Looped = true },
    Tropical    = { Id = 0, Volume = 0.4, Looped = true },
    Haunted     = { Id = 0, Volume = 0.3, Looped = true },
    Snowy       = { Id = 0, Volume = 0.4, Looped = true },
    SkyIsland   = { Id = 0, Volume = 0.4, Looped = true },
    Volcano     = { Id = 0, Volume = 0.4, Looped = true },
    Castle      = { Id = 0, Volume = 0.4, Looped = true },
    Neon        = { Id = 0, Volume = 0.45, Looped = true },
    Night       = { Id = 0, Volume = 0.35, Looped = true },
    Lobby       = { Id = 0, Volume = 0.3, Looped = true },
  },
}
```

### 12.6 Config Access Pattern

Scripts import config via a central accessor, not by requiring individual files scattered across scripts:

```lua
-- src/shared/ConfigService.lua
local Config = {
  Ball         = require(script.Parent.config.BallConfig),
  Course       = require(script.Parent.config.CourseConfig),
  Obstacle     = require(script.Parent.config.ObstacleConfig),
  Chaos        = require(script.Parent.config.ChaosConfig),
  Shop         = require(script.Parent.config.ShopConfig),
  Progression  = require(script.Parent.config.ProgressionConfig),
  Audio        = require(script.Parent.config.AudioConfig),
  Monetization = require(script.Parent.config.MonetizationConfig),
  UI           = require(script.Parent.config.UIConfig),
  Localization = require(script.Parent.config.LocalizationConfig),
  TrickShot    = require(script.Parent.config.TrickShotConfig),
}
return Config
```

---

## 13. UI/UX

### 13.1 HUD (in-round)

```
┌──────────────────────────────────────────────────────┐
│  [Hole 3/9]  Par 3   Strokes: 2        [Scoreboard]  │
│                                                        │
│                  [3D course view]                      │
│                                                        │
│  [Aim zone (drag here)]      [Power Btn]  [Spin BTN]  │
│  [Mini leaderboard]                     [Emote BTN]   │
└──────────────────────────────────────────────────────┘
```

- HUD collapses non-essential elements during shot (clean focus)
- Power bar vertical on mobile, horizontal on PC
- Score delta shown immediately after sink (–1 Birdie, HIO!, +1 Bogey)
- Trick shot overlay appears for 2s then fades

### 13.2 Menus

Flow: Lobby → Course Select → Mode Select → Chaos Modifier (optional) → Ready → Load

- All menus have back navigation (no dead ends)
- Settings: Audio volume (music/SFX separate), Control scheme, Graphics quality, Aim Assist toggle, Language

### 13.3 Feedback Principles

- Every shot: visual (trail, impact particle) + audio (swing whoosh, surface-specific impact)
- Hole-in-one: full-screen celebration, server announcement banner, fireworks
- Lip-out: slow-mo camera + distinct "clank" audio + crowd "ooh"
- Near-miss: subtle red flash on hole rim
- Trick shot: overlay + distinct sound played for all spectators
- Shortcut found: local discovery animation (doesn't announce to others)

---

## 14. Audio

All sound asset IDs live in `AudioConfig.lua`. No IDs anywhere else in code.

| Event | Sound |
|---|---|
| Club swing | Whoosh, pitch varies with power |
| Ball impact grass | Soft thud |
| Ball impact hard surface | Sharp click |
| Ball impact ice | High-pitched slide |
| Ball in hole | Plunk + crowd cheer |
| Hole-in-one | Full fanfare |
| Lip-out | Metallic clank |
| Trick shot | Crowd "ooh" + whoosh |
| Water/lava hazard | Splash / sizzle |
| Shortcut found | Discovery chime |
| Chaos modifier activated | Comedic event sound |
| UI click | Soft tap |
| Power bar at full | Tension sting |
| Background music | Per-theme loop |
| Ambient sounds | Per-theme (birds, wind, lava rumble, ocean) |

- Music and SFX volumes independently adjustable in Settings
- No audio autoplays on Roblox game page (follows Roblox audio policy)

---

## 15. Technical Notes

### 15.1 Architecture

- **Client:** Rendering, input handling, local UI, camera, ball trail FX, ghost replay
- **Server:** Authoritative physics result, score validation, anti-cheat, DataStore writes
- **Shared:** Config (via ConfigService), utility modules, types

Shot fired client-side for responsiveness → server validates final position → reconcile if delta exceeds threshold.

### 15.2 Anti-Cheat

- Server owns all score writes; client cannot increment scores
- Shot power capped server-side (`BallConfig.Physics.MaxPowerVelocity`)
- Stroke count validated per hole server-side
- Trick shot bonuses validated server-side (surface contact log, not client assertion)

### 15.3 Data Persistence

- `DataStoreService` stores: level, XP, coins, gems, inventory, quest progress, course bests, shortcuts found, streak
- Auto-save on round end and lobby return
- Retry with exponential backoff on DataStore failure; backup store on second failure

### 15.4 Performance Targets

| Platform | Target FPS | Notes |
|---|---|---|
| PC | 60 | Full particles, shadows |
| Mobile | 30+ | Reduced trail particle count (via `UIConfig`), simplified shadows |
| Console | 60 | Full particles, dynamic resolution allowed |

- LOD system for course props at distance
- Ball trail particle limit in `UIConfig.lua` per quality tier
- Max 8 players; physics complexity bounded by design
- Course loads via Roblox streaming (not full upfront load)

### 15.5 Ghost Replay Storage

- Ghost data = array of timestamped `{Position, Velocity}` snapshots at 20Hz
- Top 3 per course stored in DataStore; personal best always stored
- Playback interpolates between snapshots; no full physics re-sim

---

## 16. Nuance & Feel

Small details that separate "feels great" from "just works":

- **Power bar tempo:** Non-linear speed — precision zone in middle, forgiving at extremes — feels intentional, not random
- **Hole magnet:** 0.5-stud radius only, so hole-in-ones feel earned, not magnetized in
- **Camera lag:** 0.1s lerp follow delay makes ball movement feel weighty and real
- **Spin visibility:** Ball visibly rotates in direction of applied spin during flight
- **Lip-out slow-mo:** Ball circling hole rim triggers brief slow-mo + unique audio — cinematic, memorable
- **Simultaneous shots:** All players shoot same turn; no waiting around watching others; more energy
- **Failure tone:** Over-par finish has no harsh punishment sound or red screen; keeps mood light
- **Hole preview:** 3s flyover camera on first visit to hole; skippable; on revisit auto-skipped
- **Shortcut discovery:** Silent local notification (no announcement to server) — respects the hunt
- **Obstacle telemetry:** Moving obstacles have slight anticipatory animation cues (windmill arm flash) so timing isn't pure memorization
- **Spectator reactions:** Floating emoji reactions appear above players and over ball on impact — lobby energy stays high even while watching
- **Chaos modifier intro:** 3-second countdown + "CHAOS ACTIVATED: Low Gravity" screen on modifier activation
- **Victory screen:** Shows stroke-by-stroke replay of player's lowest-score hole; shareable screenshot frame via Roblox screenshot API
- **Lobby putting green:** Playable; sinking putts in lobby earns small coin trickle; keeps players engaged while waiting

---

## 17. Out of Scope (v1)

Deferred to prevent scope creep:

- Course creator / user-generated levels
- ELO / ranked matchmaking
- Clan / guild system
- Real-money auction house for cosmetics
- Voice chat integration
- Replay export to video
- Arabic / Hebrew RTL layout
- Spectator betting / prediction mini-game
- Mobile AR mode

---

*Document maintained by: M. Iqbal Effendi*  
*Last updated: 2026-05-24*
