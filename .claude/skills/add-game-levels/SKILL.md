---
name: add-game-levels
description: Guide for adding new topics (levels) to the Muncher browser game. Use when the user wants to add a new topic, category, or subject area to the game in muncher/index.html.
---

# Adding New Topics to Muncher

In Muncher, "levels" are **topics** — each topic generates an infinite series of procedurally-shuffled boards. Adding a new level means adding a new topic to `muncher/index.html`.

## How the Game Works

- The 6×5 grid has 30 cells: **20 correct** + **10 incorrect** per board
- Each board is randomly generated from the topic's data pools
- The level counter increments every time the player eats all 20 correct cells
- There is no fixed end — the same topic replays with reshuffled positions

## Two Topic Types

### Type 1 — List-based Topic

Use `listTopic()` (defined at line 383). Requires two arrays:
- `correct[]` — items that satisfy the challenge (aim for 25+ entries)
- `incorrect[]` — thematically plausible wrong answers (aim for 25+ entries)

```js
const myTopic = listTopic({
  id:        'my-topic',         // unique key, no spaces
  label:     'My Topic',         // shown in dropdown
  icon:      '🎯',               // emoji shown next to label
  challenge: '🎯  Eat the X!',   // instruction shown above the grid
  correct: [
    'ItemA', 'ItemB', 'ItemC',   // 25+ items
  ],
  incorrect: [
    'WrongA', 'WrongB',          // 25+ plausible-but-wrong items
  ],
});
```

**Good incorrect lists** cause genuine hesitation — wrong answers from the same domain (e.g. landlocked countries when the challenge is coastal countries), not random noise.

**Minimum data thresholds:**
- `correct.length` must be ≥ 20 (the board fills 20 correct cells)
- `incorrect.length` must be ≥ 10 (the board fills 10 incorrect cells)
- Aim for 30+ in each so boards vary across replays

### Type 2 — Math Topic

Use `multiplesTopic(n, maxVal)` (defined at line 311) for multiples-based math, or build a custom topic object directly:

```js
// Built-in factory: multiples of n up to maxVal
const myMathTopic = multiplesTopic(7, 70);  // multiples of 7, range 1–70

// Custom math topic (manual object)
const customMathTopic = {
  id:        'custom-math',
  label:     'Even Numbers',
  icon:      '🔢',
  challenge: '🔢  Eat the even numbers!',
  generate(nc, ni) {
    const correct   = [];
    const incorrect = [];
    for (let i = 1; i <= 60; i++) {
      (i % 2 === 0 ? correct : incorrect).push(i);
    }
    return shuffle([
      ...pickN(correct,   nc).map(v => ({ label: String(v), isCorrect: true  })),
      ...pickN(incorrect, ni).map(v => ({ label: String(v), isCorrect: false })),
    ]);
  }
};
```

`generate(nc, ni)` receives `nc=20` (correct cells needed) and `ni=10` (incorrect cells needed), and must return a shuffled array of `{ label, isCorrect }` objects.

## Step-by-Step: Add a New Topic to an Existing Category

**1. Define the topic** — add your topic constant before the `CATEGORIES` block (around line 600):

```js
// ─────────────────────────────────────────
//  TOPIC: My New Topic
// ─────────────────────────────────────────

const myNewTopic = listTopic({
  id:        'my-new-topic',
  label:     'My New Topic',
  icon:      '🎭',
  challenge: '🎭  Eat the correct items!',
  correct:   ['A', 'B', 'C', /* ... 25+ items */],
  incorrect: ['X', 'Y', 'Z', /* ... 25+ items */],
});
```

**2. Register the topic** — add it to the appropriate array in `TOPICS_BY_CAT` (line 610–616):

```js
const TOPICS_BY_CAT = {
  'simple-math':    [...],
  'difficult-math': [...],
  'geo':            [...],
  'tv':             [gilmoreGirlsTopic, simpsonsTopic, myNewTopic],  // ← added here
  'lang':           [...],
};
```

## Step-by-Step: Add a Whole New Category

**1. Add the category to `CATEGORIES`** (line 600–606):

```js
const CATEGORIES = [
  { id: 'simple-math',    label: 'Simple Math',    icon: '🔢' },
  { id: 'difficult-math', label: 'Difficult Math',  icon: '🧮' },
  { id: 'geo',            label: 'Geography',       icon: '🌍' },
  { id: 'tv',             label: 'TV Shows',        icon: '📺' },
  { id: 'lang',           label: 'Languages',       icon: '🗣️' },
  { id: 'science',        label: 'Science',         icon: '🔬' },  // ← new
];
```

**2. Add the topics array to `TOPICS_BY_CAT`** (line 610–616):

```js
const TOPICS_BY_CAT = {
  ...existing entries...,
  'science': [myFirstScienceTopic, mySecondScienceTopic],
};
```

Every key in `TOPICS_BY_CAT` must match an `id` in `CATEGORIES`.

## Checklist Before Finishing

- [ ] Topic `id` is unique (search the file for it)
- [ ] `correct[]` has ≥ 25 entries
- [ ] `incorrect[]` has ≥ 25 entries (thematically plausible, not random)
- [ ] `challenge` string is short and clear (fits on one line)
- [ ] Topic is registered in `TOPICS_BY_CAT`
- [ ] If new category: entry added to both `CATEGORIES` and `TOPICS_BY_CAT`
- [ ] Open `muncher/index.html` in a browser, select the new topic, play a board to verify it works

## Commit and Deploy

```bash
git add muncher/index.html
git commit -m "Add <topic name> topic to Muncher"
git push -u origin <branch>
```

GitHub Actions (`.github/workflows/pages.yml`) deploys automatically on every push to `main`.
