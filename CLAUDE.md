# Robin Hoodwinked — Game Spec

Build a complete, playable browser game in a **single `index.html`** (inline CSS + JS, no build step, no dependencies). It must work by double-clicking the file. A Google Fonts link is fine, with fallback fonts offline.

**Time is tight (~1.5 hours).** Build in this order and keep it playable at every step:
1. Archery physics + one hard-coded round.
2. Puzzle logic + 10 hand-written rounds (fully offline).
3. Funny feedback (fainting, crowd, herald lines from a local list).
4. **Stretch only:** Claude-powered herald + generated puzzles via the API.

## Concept
One player. Each round, three villagers stand in a row with apples on their heads. Exactly **one** of them stole the king's pie. **The thief always lies; innocent villagers always tell the truth.** Read their statements, work out who the thief is, then shoot the apple off the thief's head with a bow.

Tone: funny and chaotic, medieval fair. A pompous royal herald narrates and roasts the player.

## Screen layout (canvas, 1280×720 logical, scaled to fit the window)
- **Left:** the archer (player) on a small hill.
- **Right:** three villagers standing in a row, each with an apple on their head and a name tag (A, B, C plus a silly name like "Sir Bartholomew", "Gwen the Baker").
- **Above each villager:** a speech bubble with their statement.
- **Top bar:** round number (1–10), score, arrows left this round (3), wind indicator (arrow + strength).
- **Bottom:** herald banner — a scroll with the herald's current line.
- Background: sky, a castle in the distance, bunting flags (flags flutter with the wind direction).

## Puzzle rules
Each villager's statement is stored as structured data so the game can **verify** it:
```js
{ speaker: 0, claim: { type: "is_thief" | "not_thief", target: 0|1|2 }, text: "Bob took it!" }
```
- Exactly one thief. The thief's claim is false; every innocent's claim is true.
- Write a `solve(round)` function: try each villager as thief, keep the assignments where every statement is consistent. A valid round has **exactly one** solution.
- On load, run `solve` on all 10 hand-written rounds and `console.error` any that aren't uniquely solvable. Fix any that fail.
- Rounds get harder: early ones have a direct accusation; later ones require ruling people out ("I didn't do it", "C is innocent").

## Archery
- **Aim:** press and drag backwards from the archer (slingshot style); show a dotted trajectory preview for the first ~25% of the arc only (not the full path — that would make it too easy).
- **Release:** arrow flies with gravity; wind adds horizontal acceleration. Arrow rotates to match its velocity.
- **Hit detection:** circle hit on each apple; also detect hitting a villager's body (bad!), the ground, or leaving the screen.
- **3 arrows per round.** Wind is 0 for rounds 1–3, then random each round and stronger over time.
- Mouse + touch (`pointerdown/move/up`). Keyboard optional.

## Outcomes (the funny part)
- **Thief's apple hit:** apple bursts, thief drops a pie, crowd cheers, confetti, herald praises you sarcastically. +100 (+50 bonus per unused arrow). Next round.
- **Innocent's apple hit:** apple bursts, the innocent villager faints dramatically (falls over), crowd gasps, herald roasts you. −50. Round continues if arrows remain.
- **Villager body hit:** arrow sticks in their hat, they yelp and hop. −25.
- **Miss:** arrow sticks in the ground. A 1-in-5 chance it hits the king's hat instead (king appears in a box at the top-right, outraged).
- **Out of arrows:** the thief runs off with the pie, herald mocks you. Next round.
- After round 10: end screen with score, rank, "Play again". Ranks: <400 "Village Idiot", <800 "Squire", <1200 "Royal Archer", else "Legend of the Forest". Best score in `localStorage` (wrapped in try/catch).

## Herald
- Local fallback: arrays of ~8 lines each for `win`, `wrongVillager`, `miss`, `kingHat`, `outOfArrows`, `roundStart`. Always works offline.

## Stretch: Claude inside the game
Only after everything above works.
- Settings gear opens a small panel with an **API key input**. Keep the key **in memory only** (not localStorage). Show a warning: "Don't share this file with your key in it."
- Call the Messages API from the browser:
  - `POST https://api.anthropic.com/v1/messages`
  - headers: `x-api-key`, `anthropic-version: 2023-06-01`, `content-type: application/json`, `anthropic-dangerous-direct-browser-access: true`
  - model: `claude-haiku-5-5` (fast), `max_tokens` small.
- **Herald lines:** send the event (e.g. "player shot the innocent baker's apple, round 4") and ask for one short, pompous, funny line (max 20 words). Timeout 3s → fall back to the local line.
- **Generated rounds:** ask Claude for a JSON round in the exact structure above (3 villagers with names, short funny statements, structured claims). **Always run `solve()` on it**; if it isn't uniquely solvable or the JSON is invalid, silently use the next hand-written round instead.
- Never block gameplay on the network.

## Look
- Bright storybook medieval: sky blue, grass green, warm parchment for UI, red/gold accents.
- Fonts: a medieval display font for titles (e.g. "MedievalSharp" or "Pirata One"), a readable font for statements.
- Villagers, archer, apples, castle drawn with simple canvas shapes — no image files.
- Web Audio blips: bow twang on release, splat on apple, "boing" on hat, cheer/gasp as short noise bursts. `M` toggles mute.

## Done when
1. Opening `index.html` directly shows a title screen ("ROBIN HOODWINKED — Shoot the liar") and a Play button.
2. All 10 offline rounds pass `solve()` with exactly one solution and are playable start to finish.
3. Aiming, wind, every outcome above, fainting, king's hat, scoring and the end screen work.
4. No console errors. Works on a laptop and at phone width.
5. (Stretch) With a key entered, herald lines and generated rounds come from Claude and fall back cleanly without one.

## Implementation notes (owner-approved changes to the spec above)
- **Aiming mode (⚙ / title toggle, saved in localStorage):** *Easy* (default) shows the full trajectory up to the first thing it would hit, wind included and exact. *Hard* is the original spec: first 25% of the arc, no wind.
- Arrow power raised (`MAXSPEED = 1400`). Flight and preview share `stepArrow()` (fixed 1/300 s step) and `arrowHit()`.
- All villager statements, herald lines and Claude prompts use simple everyday English.
- Hosted on GitHub Pages: https://babusovereign.github.io/robin-hoodwinked/
