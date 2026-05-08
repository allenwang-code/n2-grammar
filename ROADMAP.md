# N2 Grammar App — What We Built & Roadmap

---

## What We Built

### Content Layer
Three source files covering JLPT N2 grammar:

| File | Purpose |
|------|---------|
| `jlpt_n2_grammar.md` | Broad reference — 100 patterns across 19 clusters, includes N3/N4 overlap for review |
| `jlpt_n2_specific.md` | Exam-focused — patterns exclusive to N2, comparison tables, top-20 frequency list |
| `index.html` | The app itself — everything below |

### App (`index.html`) — Single self-contained file, no dependencies
Deployed at: `https://allenwang-code.github.io/n2-grammer/`

**Content:**
- 11 grammar clusters (A–K) with 100 N2-specific patterns
- Each card: pattern, meaning, Japanese example, English translation, usage note
- ⚠️ high-frequency flag on patterns most likely to appear on the exam
- Comparison tables tab (⇄) — side-by-side nuance breakdowns for commonly confused pairs

**Navigation & Reading UX:**
- Dark theme — optimized for train/commute lighting
- Bottom tab bar: `Day 1 · わけ Family` through `Day 11 · Reproach & Commit` + Compare
- Active tab auto-scrolls into center when switching
- Cards expand/collapse on tap — see examples only when you want them
- Three font sizes (A⁻ / A / A⁺) for different viewing distances
- ⚠️ Only filter — cuts the list to high-frequency patterns for short sessions

**Study Mode:**
- Shuffled flip-card deck per cluster (or all clusters)
- Front: pattern + meaning | Back: example sentence + usage note
- `↩ Again` / `✓ Got it` rating
- Progress bar across the session
- Learned cards fade in browse view

**Progress:**
- `localStorage` persistence — learned state survives app close
- Learned count shown in header subtitle

---

## What Can Be Improved

### 1. Spaced Repetition System (SRS) — Highest Impact
> Currently: cards marked "learned" just fade out. There's no scheduling.

A real SRS would track *when* to show each card again based on how well you know it — the single biggest lever for retention.

**Plan:**
- Store per-card state: `{ interval, dueDate, easeFactor }` in localStorage
- On `✓ Got it`: multiply interval by ease factor (start at 2.5), schedule next review
- On `↩ Again`: reset interval to 1 day
- Add a **Due Today** badge on cluster tabs showing how many cards are due
- Add a **Review** mode that only shows due cards (separate from free study)
- Show estimated next review date on each card ("Review in 3 days")

This alone would transform the app from a reader into an Anki-equivalent.

---

### 2. Swipe Gestures in Study Mode
> Currently: tap buttons for Again/Got it.

On a train, one-handed swipe is more natural than tapping two buttons.

**Plan:**
- Swipe **right** → ✓ Got it
- Swipe **left** → ↩ Again
- Swipe **up** → flip card (reveal example)
- Show subtle visual hints on first launch (arrow overlays that fade)
- Use `touchstart`/`touchend` delta — no library needed (~30 lines of JS)

---

### 3. Daily Streak & Goal
> Currently: no sense of daily habit or progress over time.

Commute study works best when it's a daily ritual with visible momentum.

**Plan:**
- Set a daily goal: 5 / 10 / 20 cards
- Track streak in localStorage (`{ lastDate, streakCount }`)
- Show streak counter in header: `🔥 7 days`
- On app open, check if today's goal is met — show a subtle completion state
- Weekly summary view: heatmap of cards reviewed per day (simple grid, no library)

---

### 4. Multiple Example Sentences
> Currently: one example per pattern.

One sentence is enough to recognize a pattern; two or three builds real production ability.

**Plan:**
- Add `examples: [{ jp, en }, ...]` array to each pattern in the data
- Show first example by default
- "More examples" button reveals the rest inline
- Second example should show a contrasting context (e.g., different register or meaning)
- Third example ideally from authentic source (news headline, novel line)

---

### 5. Wrong Usage / Contrast Notes
> Currently: usage notes explain what the pattern means.

N2 grammar questions often test whether you can identify *wrong* usage.

**Plan:**
- Add `trap` field to high-frequency patterns: common wrong usage shown in red with explanation
- Example for `だけあって` trap: ❌ `3ヶ月だけあって、まだ下手だ` (can't be used for negative unexpected results — that's `にしては`)
- Show traps collapsed under a "⚠️ Common mistake" toggle on the card

---

### 6. Search
> Currently: you must know which cluster a pattern is in.

On the train you might remember part of a pattern but not which day it's in.

**Plan:**
- Search bar in header (hidden by default, tap 🔍 to expand)
- Searches across: pattern text, meaning, example JP/EN, notes
- Results shown as cards with their cluster badge
- Highlight matched text in results
- Clear button resets to cluster view

---

### 7. PWA — True Offline + Install Prompt
> Currently: works offline after first load, but no install prompt and no service worker.

**Plan:**
- Add `manifest.json`: app name, icon, theme color, `display: standalone`
- Add `service-worker.js`: cache-first strategy for the HTML file
- Show "Add to Home Screen" prompt on first visit (deferred prompt API)
- Add a simple app icon (kanji 文 on dark background works well)
- This makes it indistinguishable from a native app on iOS/Android

---

### 8. Progress Sync Across Devices
> Currently: progress only lives in the browser's localStorage on one device.

**Plan (no backend required):**
- Export progress as a JSON string → copy to clipboard
- Import by pasting the JSON string
- This covers the phone ↔ laptop sync case manually
- Longer term: use GitHub Gist as a free key-value store (one API call to save/load)

---

### 9. JLPT Practice Questions
> Currently: the app teaches patterns but doesn't test in exam format.

**Plan:**
- Add 2–3 multiple-choice questions per pattern in the data
- Question types mirror the real N2 grammar section:
  - Fill in the blank: `彼が来ない___、何かあったのだろう。`
  - Identify correct usage from 4 options
  - Sentence ordering (putting scrambled segments in order)
- A **Quiz Mode** separate from Study Mode
- Score tracked per cluster

---

### 10. Content Expansion
> Currently: grammar only, N2 only.

| Expansion | Notes |
|-----------|-------|
| N2 Vocabulary | Top 3,000 N2 words with example sentences, same card UI |
| N1 Grammar | Same structure, separate cluster set — natural next step after passing N2 |
| Reading passages | Short N2-level texts with grammar patterns highlighted in-context |
| Kanji readings | N2 kanji with reading/meaning/example, same SRS system |

Each expansion can be a separate tab group without changing the core UI.

---

## Priority Order

| Priority | Feature | Effort | Impact |
|----------|---------|--------|--------|
| 🔴 1 | Spaced Repetition (SRS) | High | Very High |
| 🔴 2 | Swipe gestures | Low | High |
| 🟡 3 | Daily streak & goal | Medium | High |
| 🟡 4 | Multiple examples | Medium | Medium |
| 🟡 5 | Search | Medium | Medium |
| 🟢 6 | Wrong usage / traps | Low | Medium |
| 🟢 7 | PWA manifest + service worker | Low | Medium |
| ⚪ 8 | Progress sync | Medium | Low |
| ⚪ 9 | Practice questions | High | High |
| ⚪ 10 | Content expansion | Very High | Very High |

---

## Technical Notes

The entire app is a **single HTML file** — intentionally. This keeps deployment trivial (just push one file) and works offline without a build step.

Before adding features that require multiple files (service worker, manifest, separate JS/CSS), the HTML should be refactored:
```
n2-grammer/
├── index.html
├── manifest.json
├── sw.js
├── data/
│   ├── clusters.js
│   └── compares.js
└── css/
    └── app.css
```

The data (grammar patterns) is currently embedded in the HTML. Extracting it to `data/clusters.js` would make content updates independent of UI changes and allow others to contribute grammar corrections via pull requests.
