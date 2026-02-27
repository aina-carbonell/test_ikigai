# Scoring Algorithm & Results Logic

This document explains exactly how user responses are transformed into scores, and how those scores drive the personalised results.

---

## 1. Data Model

Each of the 20 questions is tagged with one of four dimension codes:

| Code | Dimension |
|------|-----------|
| `L`  | Love — What You Love |
| `G`  | Good — What You're Good At |
| `P`  | Paid — What You Can Be Paid For |
| `W`  | World — What the World Needs |

Answers are stored in a flat `answers` object keyed by question index (0–19):
```js
answers = {
  0: 4,   // Q1 answer value
  1: 2,   // Q2 answer value
  // ...
}
```

---

## 2. Normalisation

Raw answer values are normalised to the range **[0, 1]** before scoring.

### Multiple Choice & Emoji Selection
Options have values 1–4. Normalisation:
```
normalised = (value - 1) / 3
```
| Raw value | Normalised |
|-----------|-----------|
| 1 | 0.000 |
| 2 | 0.333 |
| 3 | 0.667 |
| 4 | 1.000 |

### Likert Scale (1–5)
```
normalised = (value - 1) / 4
```
| Raw value | Normalised |
|-----------|-----------|
| 1 | 0.00 |
| 2 | 0.25 |
| 3 | 0.50 |
| 4 | 0.75 |
| 5 | 1.00 |

---

## 3. Dimension Scores

Each dimension score is the **simple average** of all normalised answers for that dimension:

```js
score_L = avg(normalised values for all L-tagged questions)
score_G = avg(normalised values for all G-tagged questions)
score_P = avg(normalised values for all P-tagged questions)
score_W = avg(normalised values for all W-tagged questions)
```

Each score is a float in [0, 1]. Multiplied by 100 for display as a percentage.

### Distribution of questions by dimension

| Dimension | Question IDs (0-indexed) | Count |
|-----------|--------------------------|-------|
| L (Love)  | 0, 4, 8, 12, 16 | 5 |
| G (Good)  | 1, 5, 9, 13, 17 | 5 |
| P (Paid)  | 3, 7, 11, 15, 19 | 5 |
| W (World) | 2, 6, 10, 14, 18 | 5 |

Balanced across all four dimensions (5 questions each).

---

## 4. Venn Diagram Rendering

The SVG Venn diagram uses dimension scores to modulate **fill opacity**:

```js
fillOpacity = 0.18 + score * 0.22
```

This gives a visual range from:
- **0.18** (score = 0) — very faint, dimension barely present
- **0.40** (score = 1) — full, dimension strongly expressed

The four circle positions are fixed (offset from centre), overlapping to form the classic four-circle Venn structure.

---

## 5. Tooltip & Reflection Text

Each dimension has **4 text variants**, selected by score thresholds:

| Score range | Variant |
|-------------|---------|
| ≥ 0.75 | Strong — dimension is well developed |
| ≥ 0.50 | Moderate — dimension present, room to grow |
| ≥ 0.25 | Emerging — dimension exists but underdeveloped |
| < 0.25  | Nascent — dimension needs most attention |

The same logic applies to the Ikigai centre tooltip, which uses the **overall average** score plus the **top-ranked dimension** to generate its narrative.

---

## 6. Synthesis Text

The synthesis paragraph is determined by two variables:
1. **Overall average** = (score_L + score_G + score_P + score_W) / 4
2. **Top dimension** = dimension with highest score (used to name the strongest anchor)
3. **Bottom dimension** = dimension with lowest score (used to name the growth area)

| Average range | Synthesis tone |
|---------------|----------------|
| ≥ 0.65 | Integration — dimensions are converging, Ikigai is near |
| ≥ 0.45 | Active process — meaningful development, clear direction |
| < 0.45  | Beginning — honest starting point, invite exploration |

The synthesis text names the top and bottom dimensions explicitly, making each result meaningfully personalised.

---

## 7. Extending the Algorithm

If you want to add more questions or change the scoring:

### Adding a question
1. Add an entry to the `QUESTIONS` array in `index.html`
2. Set `area` to `'L'`, `'G'`, `'P'`, or `'W'`
3. For `mc` and `emoji` types, assign values 1–4 to options (4 = strongest signal for that dimension)
4. For `likert` type, scale is always 1–5 with 5 = strongest signal

### Adjusting text thresholds
Modify the `if/else` chains in these functions:
- `getPassionText(score)` — Love dimension
- `getStrengthText(score)` — Good dimension
- `getProfessionText(score)` — Paid dimension
- `getMissionText(score)` — World dimension
- `getIkigaiText(scores)` — Centre summary
- `generateSynthesis(scores)` — Full synthesis paragraph

### Changing score weights
Currently all questions within a dimension are weighted equally. To add weighting:
```js
// Instead of simple average, use weighted average:
const weighted = {
  0: 1.5,  // question index: weight multiplier
  4: 1.0,
  // ...
};

const avg = area => {
  const relevant = QUESTIONS
    .map((q, i) => ({ q, i }))
    .filter(({ q }) => q.area === area);
  
  const totalWeight = relevant.reduce((sum, { i }) => sum + (weighted[i] || 1), 0);
  const weightedSum = relevant.reduce((sum, { i }) => {
    const norm = normalise(answers[i], QUESTIONS[i].type);
    return sum + norm * (weighted[i] || 1);
  }, 0);
  
  return weightedSum / totalWeight;
};
```

---

## 8. LocalStorage Schema

Progress is saved to `localStorage` under the key `ikigai_test_v2`:

```json
{
  "answers": {
    "0": 4,
    "1": 2,
    "3": 5,
    ...
  },
  "currentQ": 7
}
```

On page load, if a saved session is found and `currentQ < 20`, the user is offered the option to resume. If `currentQ >= 20`, results are shown directly.

The storage key is versioned (`_v2`) so that future structural changes to the question set don't cause legacy data conflicts — increment the version number if you change the question structure.
