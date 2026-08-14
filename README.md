# Health Statistics Calculator

A standalone health statistics and calorie tracking web app built with vanilla HTML, CSS, and JavaScript. No backend, no accounts, no setup — just open the page and start tracking.

**Live demo:** https://umexrn.github.io/health-statistics-calculator/

## Features

- **BMI calculation** from height and weight
- **Estimated TDEE (Total Daily Energy Expenditure)** based on age, height, weight, and activity level
- **Goal-based calorie targets** for weight loss, muscle gain, maintenance, and more
- **Current-intake comparison** — tells you exactly how much to increase or decrease your daily calories to hit your goal
- **Per-meal calorie breakdown** based on your number of meals per day
- **Historical tracking** — every saved entry builds a personal record over time, nothing is overwritten
- **Progress charts** — toggle between Weight and BMI trends, with a highlighted healthy BMI range (18.5–24.9) on the BMI chart
- **Persistent storage** via browser `localStorage` — no login, no database
- **Responsive design** — works on desktop, tablet, and mobile

## Tech Stack

- HTML5
- CSS3 (custom, no frameworks)
- Vanilla JavaScript (no build tools)
- [Chart.js](https://www.chartjs.org/) for data visualization
- Browser `localStorage` for persistence

## How It Works

All calculations run entirely client-side. BMR is estimated using a sex-neutral variant of the Mifflin-St Jeor equation, scaled by an activity multiplier to estimate TDEE, then adjusted based on the selected goal to produce a recommended daily calorie target.

Data is stored locally in the browser via `localStorage` — there's no server or external database, keeping the app fully self-contained and privacy-respecting. The tradeoff: your history is tied to the specific browser/device you entered it on.

## Data Model

Each saved entry stores:

| Field | Description |
|---|---|
| Date | Auto-recorded on save |
| Name | User's name |
| Age | In years |
| Height | In cm |
| Weight | In kg |
| Lifestyle | Activity level (5 tiers, Sedentary → Athlete) |
| Meals | Meals per day |
| Goal | Selected fitness goal |
| Daily Calories | Optional — current intake for comparison |

BMI, TDEE, and calorie targets are calculated on the fly from these raw values rather than stored redundantly.

## Disclaimer

BMI and calorie estimates are approximations for general informational purposes only — not medical advice or a clinical diagnosis.

## Running Locally

```bash
git clone https://github.com/umexrn/health-statistics-calculator.git
cd health-statistics-calculator
open index.html
```

No build step or install required.

## Roadmap / Future Ideas

- Export/import history as JSON
- Edit existing history entries
- Additional metrics (body fat %, waist-to-hip ratio)
- Optional cloud sync for cross-device access

## Author

Built by [Umer Nauman](https://github.com/umexrn) as a portfolio project.
