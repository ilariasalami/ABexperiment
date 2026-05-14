# A/B Experiment: Button Color and Click-Through Time
**Group 4 — Evaluation of Interactive Systems**

## Research Question
Does the color of a call-to-action (CTA) button (blue vs. green) affect the time users take to click it on a webpage?

## Hypotheses
- **H₀:** Button color has no effect on click-through time (μ_blue − μ_green = 0)
- **H₁:** Button color does have an effect (μ_blue − μ_green ≠ 0)

## How it works
The experiment runs entirely in a single `index.html` file. When a participant visits the page, the following happens automatically:

1. **Consent screen** — participant is informed about the study and clicks Start
2. **Random assignment** — JavaScript randomly assigns the user to condition A (blue button) or condition B (green button) with equal probability (`Math.random() < 0.5`)
3. **Task** — participant sees a realistic webpage mockup and clicks the CTA button; reaction time is recorded in milliseconds using `performance.now()`
4. **Likert question** — participant rates the visual prominence of the button on a 7-point scale
5. **Submission** — data is sent anonymously to Google Forms and stored in a linked Google Sheet

## Variables
| | |
|---|---|
| **Independent variable** | Button color — Level A: Blue `#1a56db`, Level B: Green `#1a7a45` |
| **Dependent variable (primary)** | Reaction time in milliseconds (page render → button click) |
| **Dependent variable (secondary)** | Perceived salience (7-point Likert scale) |

## Data collection
Data is collected via Google Forms and stored automatically in a Google Sheet with three columns:

| condition | time_ms | likert |
|-----------|---------|--------|
| Blue / Green | integer (ms) | 1–7 |

Submission is triggered client-side using a fire-and-forget image pixel request to the Google Forms endpoint — no server required.

## Key JavaScript
```javascript
// 1. Random assignment
condition = Math.random() < 0.5 ? 'A' : 'B';
btn.style.backgroundColor = COLORS[condition].hex;

// 2. Timing — starts after first rendered frame
requestAnimationFrame(() => {
  requestAnimationFrame(() => { startTime = performance.now(); });
});

// 3. On click — record reaction time
reactionMs = Math.round(performance.now() - startTime);

// 4. Submit to Google Forms
const img = new Image();
const qs  = new URLSearchParams({
  [ENTRY_COND]  : COLORS[condition].name,
  [ENTRY_TIME]  : reactionMs,
  [ENTRY_LIKERT]: likert
});
img.src = `${GFORM_URL}?${qs.toString()}`;
```

## Repository structure
```
/
└── index.html      # Full experiment (consent, task, Likert, thank-you)
└── README.md       # This file
```

## Links
- **Live experiment:** _[https://github.com/ilariasalami/ABexperiment.git]_
- **Response data (Google Sheet):** _[https://docs.google.com/spreadsheets/d/1Jbr0KCWt3bE1l5olpVVoV00BhF1uBni9-C-jp2UPdn0/edit?usp=sharing]_
