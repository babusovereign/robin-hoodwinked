# Running Notes — Robin Hoodwinked

## Current state (2026-10-12)
- `index.html` complete: build steps 1–3 (archery, 10 offline rounds, all funny outcomes) + step 4 stretch (Claude herald + generated rounds behind ⚙ settings, key in memory only).
- Verified headless (Chrome): all 10 rounds uniquely solvable; scripted full playthrough (innocent apple, hat hit, miss, thief hit, out-of-arrows, end screen) with zero console errors; Claude path tested with mocked fetch (good round used, unsolvable/invalid JSON → hand-written round, 3s timeout → local line).
- Owner changes: arrow power up (MAXSPEED 1400), Easy/Hard aiming mode — Easy (default) full exact path incl. wind; Hard = spec (25% arc, no wind), plain-English text.
- NOT yet tested: real API key against live API; real phone hardware (headless min width is 500px).

## Next
1. Owner: play-test on laptop + phone; try a real key in ⚙.
2. Tune difficulty (MAXSPEED / wind strength in `startRound`) if too easy/hard.

## Log
- 2026-10-12 — Published to GitHub Pages (public repo babusovereign/robin-hoodwinked); README + CLAUDE.md implementation notes added.
- 2026-10-12 — Added Easy/Hard aiming mode (⚙ panel radio, title-screen toggle, top-bar label; saved in localStorage). Easy = full exact path w/ wind; Hard = spec 25% arc, no wind.
- 2026-10-12 — Aim preview now shows the full path up to the first thing it would hit (reaches the farthest apple); flight + preview share arrowHit().
- 2026-10-12 — Precise range: arrow + aim preview share one fixed-step sim (1/300s, incl. wind); landing matches preview exactly, frame-rate independent.
- 2026-10-12 — Longer range (MAXSPEED 1400, preview 50% of arc — owner request, overrides spec 25%) + simpler English in statements/herald/Claude prompts.
- 2026-10-12 — Built and verified full game in single index.html (Opus 5.5, owner approved).
