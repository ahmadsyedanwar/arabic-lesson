# Arabic Verb Study App

A mobile-friendly single-page web app for studying Arabic verb conjugations, built for Bengali speakers. 🇸🇦 🇧🇩

**Live:** [arabic.ahmadsyed.net](https://arabic.ahmadsyed.net)

---

## Quick Start

1. **No installation needed** — just open `index.html` in any browser
2. Select a day (1-4) to filter verbs
3. Choose a study mode (ফ্ল্যাশকার্ড, আবৃত্তি, কুইজ, or অগ্রগতি)
4. Track your progress automatically (stored in browser)

---

## Features

### 4 Study Modes

**ফ্ল্যাশকার্ড (Flashcard)**
- Front: fully-vowelled ماضى / مضارع هو forms with harakat, masdar, Bengali meaning, and باب pattern
- Back: all 20 conjugation forms (ماضى، مضارع، أمر، نَهْي + مصدر)
- Self-grade buttons (✓ জানতাম / ✗ জানতাম না) feed into the progress tracker

**আবৃত্তি (Chant)**
- Cycle through all forms of a verb aloud, section by section: ماضى · مضارع · أمر · نَهْي
- Auto-play at three speeds (slow / normal / fast)
- RTL grid layout matching Arabic reading order

**কুইজ (Quiz)**
- 10-question MCQ per session covering all 4 tense categories including نَهْي
- Distractors drawn from the **same verb** (different tense or person) and other verbs for better learning
- Pronouns displayed in Bengali (আমি, তুমি, তারা…) with Arabic shown as secondary
- Instant feedback with correct answer revealed
- Question bank dynamically generated from all verbs or filtered by day

**অগ্রগতি (Progress)**
- Per-verb mastery tracking: 🔴 new → 🟡 learning → 🟢 mastered (≥3 correct, <30% error rate)
- Daily streak counter
- Full verb list with correct/wrong counts and last-seen date
- Reset button

### Data
- 16 verbs across 4 days, covering all 4 باب (verb class) patterns:
  - فَعَلَ / يَفْعَلُ · فَعَلَ / يَفْعِلُ · فَعَلَ / يَفْعُلُ · فَعِلَ / يَفْعَلُ
- All data lives in `verbs.json` — edit it to add or change verbs with no code changes needed

---

## File Structure

```
arabic/
├── index.html        # Single-file app (HTML + CSS + JS, ~940 lines)
├── verbs.json        # All verb, pronoun, and harakat data (source of truth)
├── server.py         # Optional: Python development server
└── README.md         # This file
```

### `verbs.json` schema

Top-level blocks:

```json
{
  "pronouns": {
    "madi": ["هو", "هي", "أنتَ", "أنا", "هن", "هم", "أنتن", "أنتم", "نحن"],
    "mudari": ["هو", "هي", "أنتَ", "أنا", "هن", "هم", "أنتن", "أنتم", "نحن"],
    "amr": ["أنتَ", "أنتِ", "أنتما", "أنتم", "أنتن"],
    "nahi": ["أنتَ", "أنتم"]
  },
  "harakaat": [
    { "on_ba": "َ", "name_ar": "فتحة", "name_bn": "ফতহা", "color": "#e91e63", "sound": "a", "sound_latin": "fatha" },
    ...
  ],
  "verbs": [
    {
      "day": 1,
      "root": "ذهب",
      "masdar": "ذَهَاب",
      "meaning_bn": "যাওয়া",
      "bab": "فَعَلَ / يَفْعَلُ",
      "bab_id": "aa",
      "madi":   ["ذَهَبَ", "ذَهَبَتْ", "ذَهَبْتَ", "ذَهَبْتُ", "ذَهَبْنَ", "ذَهَبُوا", "ذَهَبْتُنَّ", "ذَهَبْتُمْ", "ذَهَبْنَا"],
      "mudari": ["يَذْهَبُ", "تَذْهَبُ", "تَذْهَبُ", "أَذْهَبُ", "يَذْهَبْنَ", "يَذْهَبُونَ", "تَذْهَبْنَ", "تَذْهَبُونَ", "نَذْهَبُ"],
      "amr":    ["اِذْهَبْ", "اِذْهَبِي", "اِذْهَبَا", "اِذْهَبُوا", "اِذْهَبْنَ"],
      "nahi":   ["لا تَذْهَبْ", "لا تَذْهَبُوا"],
      "note_bn": null
    }
  ]
}
```

**Data blocks used by each feature:**

| Feature | `pronouns` | `verbs` | `harakaat` |
|---------|-----------|--------|-----------|
| ফ্ল্যাশকার্ড | ✗ | ✅ | ✅ (reference) |
| আবৃত্তি | ✅ | ✅ | ✗ |
| কুইজ | ✅ | ✅ | ✗ |
| অগ্রগতি | ✗ | ✅ | ✗ |

**`bab_id` values:** `aa` (fatha/fatha) · `ai` (fatha/kasra) · `au` (fatha/damma) · `ia` (kasra/fatha)

---

## Adding Verbs

Edit `verbs.json` and add an entry to the `verbs` array. Set `day` to 1–4 (or add a new day). Deploy the updated file — the app fetches it fresh on each load.

---

## Deployment

Served as static files via nginx on `arabic.ahmadsyed.net` with Let's Encrypt SSL.

```nginx
server {
    root /var/www/arabic;
    index index.html;
    location ~* \.(json|js|css|woff2?)$ {
        expires 7d;
        add_header Cache-Control "public, immutable";
    }
    gzip on;
    gzip_types application/json text/css application/javascript;
}
```

To deploy after editing:
```bash
sudo cp index.html verbs.json /var/www/arabic/
```

---

## Progress Tracking

All progress is stored in `localStorage` under the key `arabic_study_progress`. No server or account needed. Clearing browser data resets progress (or use the Reset button in the অগ্রগতি screen).

---

## Tech Stack

- **No build tools, no framework** — vanilla HTML/CSS/JavaScript
- **Single-file app** — all code in `index.html` (~940 lines)
- **Zero dependencies** — works offline, works everywhere
- **Fonts:** [Noto Naskh Arabic](https://fonts.google.com/noto/specimen/Noto+Naskh+Arabic) · [Hind Siliguri](https://fonts.google.com/specimen/Hind+Siliguri) (Google Fonts CDN)
- **Data:** Static JSON file, cached for 7 days

---

## Development

### Local Testing

```bash
# Option 1: Python dev server
python3 server.py
# Then visit http://localhost:8000

# Option 2: Any static server
npx http-server

# Option 3: Open directly in browser (works offline!)
open index.html
```

### Adding Verbs

Edit `verbs.json` and add a new entry to the `verbs` array:

```json
{
  "day": 1,
  "root": "فعل",
  "masdar": "فِعل",
  "meaning_bn": "বাংলা অর্থ",
  "bab": "فَعَلَ / يَفْعَلُ",
  "bab_id": "aa",
  "madi": ["...", "..."],
  "mudari": ["...", "..."],
  "amr": ["...", "..."],
  "nahi": ["...", "..."],
  "note_bn": "Optional Bengali note"
}
```

The app automatically picks up changes on next page load (cache expires in 7 days).

### Editing Harakat

To modify Arabic diacritical marks (harakat), edit the `harakaat` array in `verbs.json`. Each entry needs:
- `on_ba` — the symbol itself
- `name_ar` — Arabic name
- `name_bn` — Bengali name  
- `color` — hex color for the study card
- `sound` — phonetic sound
- `sound_latin` — romanization
