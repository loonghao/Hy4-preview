# Agent Task Prompt: Ship a Deckbuilding Roguelike from Zero

> This is an English translation of the author-provided task card.
> It records the original technical intent; it is not an instruction to run in
> this repository.

## 1. Role and goal

You are an independent full-stack game-development agent. Design, build,
illustrate, test, and package an original **deckbuilding roguelike** inspired
by the genre of *Slay the Spire*. Do not copy its card names, characters, or
artwork.

The deliverable is a double-clickable Windows desktop game (`.exe`) that runs
without Godot or another runtime installed, provides a complete single-chapter
experience, and has automated playability and balance checks.

Work independently and keep moving. When a design choice is needed, choose the
most reliable option and record the assumption in the progress report. Stop
only for a genuine tool, permission, or platform blocker.

## 2. Tools and working rules

### `dcc-mcp-godot` — engine workflow

- Use `dcc-mcp-godot` for Godot project creation, scenes, nodes, scripts,
  resource import, debugging, and export.
- Start by listing the adapter's actual tools and parameters, confirm the
  Godot version (prefer Godot 4.x/GDScript), and record the result in
  `docs/TOOLING.md`. Do not assume capabilities that are not present.
- If a direct MCP operation is unavailable, use a documented file-based
  workaround and re-import the project.

### `imageGen` — visual assets

- Generate cards, enemies, relics, UI frames, backgrounds, and map icons with
  `imageGen`; do not use images of unknown origin.
- Lock a consistent visual style before producing final assets: make three
  candidate styles with three images each, choose one, and put its fixed prompt
  suffix in `docs/ART_BIBLE.md`.
- Possible directions include dark ink fantasy, gothic painterly art, or flat
  geometric symbols. The flat-symbol direction is usually easiest to keep
  consistent at scale.
- Crop or remove backgrounds as needed, convert to PNG, and place assets in
  the matching `assets/` subdirectory.

### `dcc-cua` — end-to-end testing

Use it to drive the running game window: click, drag cards, choose routes,
resolve battles, and verify the packaged build. The full test matrix is in
Section 6.

### Version control and documentation

Use Git throughout, with a focused commit at the end of each phase. Put design
decisions in `docs/` and keep them synchronized with the implementation.

## 3. Content target: a complete chapter

These are acceptance minimums.

### Combat core

- Turn-based single-player combat against multiple enemies; start each player
  turn with **3 energy** and draw **5 cards**.
- Maintain draw, discard, and exhaust piles; reshuffle discard when draw is
  empty.
- Clear **Block** at the start of the player's turn unless a relic or card
  explicitly preserves it.
- Show enemy intent (attack, defense, buff, debuff, or special) accurately so
  the player can plan.
- Include Strength, Dexterity, Vulnerable, Weak, Poison, Burn, Thorns,
  Disarm, and Artifact (negates one debuff), with the resolution order written
  down.

### Content counts

| Category | Minimum | Notes |
|---|---:|---|
| Playable classes | 2 | For example, a block/strength heavy warrior and a spell-chain/draw-order weaver; mechanics must differ, not just numbers. |
| Cards | 80+ | At least 35 class cards per class plus 10 neutral cards; attacks, skills, powers; common/rare/epic rarities; an upgrade form for every card (160+ forms). |
| Enemies | 30+ | At least 20 normal, 7 elite, and 3 bosses, each with a distinct intent pattern. |
| Relics | 40+ | Starting, boss, shop, and event relics with trigger, passive, and cost-based effects. |
| Potions | 15+ | Consumables usable in and out of combat. |
| Map floors | 15 | Normal fights, elites, events, shops, campfires, chests, and a boss, with balanced branching paths. |
| Random events | 20+ | Two or three choices each, with positive, negative, or risky outcomes. |

### Meta systems

Include three-card rewards with a skip option, card removal, a gold economy,
run saves and resume, a results screen (damage, turns, final deck, death
cause), a main menu, class selection, audio/resolution/fullscreen settings, and
quit.

## 4. Architecture requirements

1. Separate rules from presentation. Battle rules, card resolution, enemy AI,
   and map generation must be pure GDScript data-layer modules independent of
   `Node2D` or UI nodes. The UI subscribes to their signals. A headless
   `godot --headless --script` run must complete a full battle and print a log.
2. Drive content from `data/*.json` or Godot `Resource` files. Adding a card
   must not require changing the battle engine.
3. Use seeded, independent RNG streams for maps, rewards, battles, and events;
   a seed must reproduce a complete run.
4. Suggested layout:

   ```text
   res://
   ├─ core/          # battle_engine, card_resolver, enemy_ai, map_gen, rng
   ├─ data/          # cards.json, enemies.json, relics.json, events.json, potions.json
   ├─ ui/            # scenes and controls
   ├─ assets/        # art/cards, art/enemies, art/relics, art/ui, art/bg, audio/
   ├─ tests/         # unit tests and headless simulator
   ├─ tools/         # generation, asset processing, balance analysis
   └─ docs/          # design, tooling, art, and test reports
   ```

5. Keep combat at 60 FPS, scene changes under one second, and cold start under
   five seconds.

## 5. Delivery phases

Complete each acceptance gate before moving on, and report what changed, the
result, assumptions, blockers, and next step.

### P0 — tool survey and project bootstrap

Survey `dcc-mcp-godot`, `imageGen`, and `dcc-cua`; write `docs/TOOLING.md`;
create the Godot project and directory skeleton; prove the empty-scene to
Windows-executable loop. Acceptance: a runnable blank `.exe`.

### P1 — combat core

Implement energy, piles, Block, status effects, intent, and win/loss. Start with
10 test cards and 3 test enemies. Acceptance: 100 headless random battles
without a crash and unit coverage for every status rule.

### P2 — combat UI and interaction

Add fan-shaped hand layout, card dragging and targeting, intent/status icons,
damage numbers, and an end-turn button. Acceptance: 10 smooth manual battles
with readable information.

### P3 — full content

Implement the counts in Section 3, add `docs/CONTENT.md`, and verify that
headless runs visit every card and enemy at least once.

### P4 — map, meta loop, and saves

Implement map generation, route choice, shops, campfires, chests, events,
gold, saves/resume, and results. Acceptance: menu to boss and back, plus a
correct resume after quitting mid-run.

### P5 — art and audio

Generate and integrate final assets using `docs/ART_BIBLE.md`; polish UI,
transitions, card effects, sound effects, and music. Acceptance: no placeholders
and a coherent finished-game look.

### P6 — automated testing and balance

Meet every quantitative target in Section 6.

### P7 — packaging

Configure a Windows desktop export with icon, version, company, and window
title. Produce `build/CardRogue-v1.0-win64.zip` with executable, PCK, README,
and controls. Extract it into a clean directory and complete one smoke-test
run before calling the release ready.

## 6. Test system

### Unit tests

Cover every card effect, status stack/decay, Block calculation, relic trigger,
and legal map generation. Target at least 80% coverage in `core/`.

### Headless balance simulation

Build a simple heuristic AI and run 1,000 complete climbs for each class. Write
`docs/BALANCE_REPORT.md` with these targets:

| Metric | Target |
|---|---|
| AI completion rate | 20–45% |
| Deaths by floor | No floor above 35% |
| Average run length | 25–45 minutes |
| Card usage | No card below 1% usage |
| Card win-rate effect | No card shifts win rate above 25% |
| Relic effect | Positive, under 20% win-rate lift per relic |
| Crashes, loops, stuck turns | 0 |

Run at least three tuning rounds and record each change.

### Real-window end-to-end checks

Run the packaged game through `dcc-cua`:

1. Complete the full 15-floor climb for both classes.
2. Try invalid card targets, insufficient energy, rapid clicks, settings
   toggles, and result-screen clicks without a crash or illegal state.
3. Quit on floor 8 and confirm the save resumes correctly.
4. Capture combat, shop, event, and results views; check clipping and overlap.
5. Test 1920×1080, 1600×900, 2560×1440, and windowed mode.

Record all cases and fixes in `docs/E2E_REPORT.md`; every case must finish
green.

### Playability questions

Can a new player understand the rules within three fights? Are there meaningful
build choices rather than one automatic win line? Are losses caused by choices
rather than unexplained spikes? Does each class support at least two distinct
build archetypes?

## 7. Quality bar

Do not ship placeholders, copied game assets, UI-only combat logic, unverified
claims, or an untested clean-directory executable. Do not weaken a test to make
it pass.

## 8. Progress report format

```text
[Phase Px complete]
Output: (3–5 items)
Acceptance: (criterion-by-criterion result with key data/screenshots)
Assumptions and decisions: (...)
Issues: (blocker or “none”)
Next: (one sentence)
```
