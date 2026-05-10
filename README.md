# Arabic Verb Study App

A mobile-friendly single-page web app for studying Arabic verb conjugations, built for Bengali speakers.

**Live:** [arabic.ahmadsyed.net](https://arabic.ahmadsyed.net)

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
- Distractors drawn from the **same verb** (different tense or person) — not random other verbs
- Pronouns displayed in Bengali (আমি, তুমি, তারা…) with Arabic shown as secondary

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
├── index.html      # Single-file app (HTML + CSS + JS)
├── verbs.json      # All verb and harakat data (source of truth)
└── README.md
```

### `verbs.json` schema

```json
{
  "pronouns": { "madi": [...], "mudari": [...], "amr": [...], "nahi": [...] },
  "verbs": [
    {
      "day": 1,
      "root": "ذهب",
      "masdar": "ذَهَاب",
      "meaning_bn": "যাওয়া",
      "bab": "فَعَلَ / يَفْعَلُ",
      "bab_id": "aa",
      "madi":   ["ذَهَبَ", "ذَهَبَتْ", "ذَهَبُوا", "ذَهَبْتَ", "ذَهَبْتُ", "ذَهَبْنَا"],
      "mudari": ["يَذْهَبُ", "تَذْهَبُ", "يَذْهَبُونَ", "تَذْهَبُ", "أَذْهَبُ", "نَذْهَبُ"],
      "amr":    ["اِذْهَبْ", "اِذْهَبِي", "اِذْهَبَا", "اِذْهَبُوا", "اِذْهَبْنَ"],
      "nahi":   ["لا تَذْهَبْ", "لا تَذْهَبُوا"],
      "note_bn": null
    }
  ]
}
```

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

- Vanilla HTML/CSS/JS — no build tools, no framework
- Fonts: [Noto Naskh Arabic](https://fonts.google.com/noto/specimen/Noto+Naskh+Arabic) · [Hind Siliguri](https://fonts.google.com/specimen/Hind+Siliguri)
- Data served as static JSON by nginx
