# Robin Hoodwinked — Shoot the liar

A silly medieval archery logic game in a single `index.html`, with no build step and no dependencies.

**Play:** https://babusovereign.com/robin-hoodwinked/ (or just double-click `index.html`).

## How to play
- Each round, three villagers have apples on their heads. One of them stole the King's pie.
- **The thief always lies. Innocent villagers always tell the truth.** Read what they say and work out who the thief is.
- Drag back from the archer to aim, then let go to shoot. Hit the apple on the thief's head.
- You get 3 arrows per round and play 10 rounds. Wind starts in round 4. `M` mutes the sound.

| Outcome | Points |
|---|---|
| Thief's apple | +100, plus 50 for each arrow you didn't use |
| Innocent's apple (they faint) | −50 |
| Villager's hat | −25 |
| Miss (1 in 5 hits the King's hat!) | 0 |

Ranks: Village Idiot (<400), Squire (<800), Royal Archer (<1200), Legend of the Forest.

## Settings (⚙)
- **Aiming mode:** *Easy* shows the arrow's whole path, wind included. *Hard* shows only the first quarter of the path and leaves out wind. You can also switch modes from the title screen.
- **Claude (optional):** paste an Anthropic API key and Claude writes the herald's jokes and invents new puzzle rounds (`claude-haiku-5-5`). The key stays in memory only and is never saved. Every generated round is checked by `solve()`. If the key is missing or the network is slow or fails, the game uses its built-in lines and rounds.

## Tech notes
- 1280×720 canvas, scaled to fit the window. On a phone held upright, the statements also appear as text below the game.
- `solve(round)` tries each villager as the thief, and every round must have exactly one answer. All 10 built-in rounds are checked when the page loads.
- The arrow and the aim preview use the same fixed-step physics (1/300 s) and the same hit check, so the preview is exact.
