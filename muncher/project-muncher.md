# Project Muncher

A browser game inspired by the classic 90s game Number Munchers, reimagined with fresh topics and modern web tech. All original design — no copyrighted assets, characters, or code.

---

## Game Overview

A grid-based game where the player navigates a character (the "muncher") through a matrix and eats cells that match the current challenge. Wrong moves cost lives.

---

## Grid

- **Dimensions:** 6 rows × 5 columns = 30 cells
- **Correct answers per board:** 20 cells
- **Incorrect answers per board:** 10 cells
- **Starting position:** top-left corner (row 0, col 0)

---

## Controls

- **Single tap / click:** Move the muncher to that cell
- **Double tap / double click:** Eat the cell (attempt answer)
- Wrong cell eaten → lose a life, muncher returns to start
- Eating all 20 correct cells → level complete, gain a life

---

## Lives

- **Starting lives:** 5
- **Max lives:** 7
- **On level complete:** +1 life (capped at 7)
- **On wrong eat:** −1 life
- **On 0 lives:** Game over

---

## Topics

Selectable via a dropdown. Two categories of topic data source:

### Type 1 — List-based Topics

A topic provides two lists:
- `correct[]` — answers that satisfy the challenge (e.g. coastal countries)
- `incorrect[]` — plausible-but-wrong answers (e.g. landlocked countries, not random noise)

The incorrect list should be thematically close to avoid trivial guessing. Both lists must be long enough to generate varied boards across replays.

**Example topics:**
- Coastal countries (correct) vs. landlocked countries (incorrect)
- Mammals (correct) vs. birds or reptiles (incorrect)
- European capitals (correct) vs. non-capital European cities (incorrect)
- Primary colors (correct) vs. secondary/tertiary colors (incorrect)

### Type 2 — Math Expression Topics

A topic provides:
- `generate()` → produces a candidate number or expression (e.g. `random(1,70)`, `random(1,40)+random(1,30)`)
- `isCorrect(value)` → boolean (e.g. `value % 7 === 0` for multiples of 7)

The generator must be able to produce enough variety for 20 correct + 10 incorrect values per board.

**Example topics:**
- Multiples of 7: `generate = random(1,70)`, `isCorrect = n % 7 === 0`
- Even numbers: `generate = random(1,60)`, `isCorrect = n % 2 === 0`
- Greater than 50: `generate = random(30,80)`, `isCorrect = n > 50`
- Sum expressions: displayed as `a + b`, correct if result > 20

---

## Topic Interface

All topics conform to a shared interface so the game engine is data-source agnostic:

```js
{
  id: String,            // unique key
  label: String,         // display name in dropdown
  challenge: String,     // shown at top of board, e.g. "Eat the coastal countries"
  type: 'list' | 'math',
  generate(count): { value, label, isCorrect }[]
  // Returns `count` cells with value, display label, and correctness flag
}
```

---

## Display & UX

- **Single HTML file** — all CSS and JS inline, no external dependencies at runtime
- **Offline capable** — no network calls during play (fonts can be bundled or use system fallbacks)
- **Kid-friendly design:**
  - Cute, rounded font (e.g. Nunito, Fredoka One, or system emoji fallback)
  - Emoji used as visual accents (topic icon, muncher character, lives indicator)
  - High contrast, readable cell text
  - Word breaking enabled for long labels (e.g. country names)
- **Responsive:** playable on tablet and desktop

---

## Deployment

- Published as a **GitHub Pages** site
- `muncher/index.html` is the complete game (single file, no build step)
- `.github/workflows/pages.yml` deploys the `muncher/` folder to GitHub Pages on every push to `main`
- The live URL will be `https://<username>.github.io/<repo>/` once Pages is enabled in the repo settings

---

## Future Considerations (not in scope for v1)

- Animated muncher character (replace emoji with sprite/CSS animation)
- Sound effects
- High score / local storage persistence
- Timer / speed challenge mode
- Multilingual support
- Mobile swipe navigation

---

## v1 Decisions (resolved)

1. **Keyboard navigation:** Arrow keys to move + Enter/Space to eat ✅
2. **v1 Topics:** Multiples of 2–9 (math), Coastal Countries vs Landlocked (geography) ✅
3. **Language:** English only for now; multilingual planned for later ✅
4. **Hosting:** GitHub Pages (not GitLab) ✅
