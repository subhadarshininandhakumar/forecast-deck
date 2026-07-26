# Forecast Deck — Interactive Predictive Analytics

A live, in-browser forecasting dashboard. Pick a dataset, pick a model, drag the
horizon slider — the chart, the confidence cone, and the accuracy score all update
instantly. No backend, no build step, just one HTML file.

## What it does

- **3 synthetic datasets** (retail sales, website visits, energy demand), each with
  its own trend and seasonality pattern
- **3 forecasting models**, computed live in JavaScript:
  - **Linear trend** — least-squares regression line
  - **Seasonal-naive** — repeats last week's pattern forward
  - **Trend + seasonality** — regression combined with a day-of-week seasonal index
- **Backtested accuracy (MAPE)** for whichever model is selected, computed on the
  last 30 known days
- **Adjustable forecast horizon** (7–60 days) via slider
- **Toggleable confidence cone** showing the forecast's uncertainty band

## Run it locally

No install needed — it's a single static HTML file.

```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080` in a browser. Or just double-click `index.html`.

## Deploy to Vercel (easiest path)

1. Go to **vercel.com** and sign in (you can use your GitHub account)
2. Click **"Add New" → "Project"**
3. Choose **"Deploy without Git"** / drag-and-drop, or drag this whole folder onto
   the upload area
4. Vercel detects it's a static site automatically — no configuration needed
5. Click **Deploy**

You'll get a live URL (like `forecast-deck.vercel.app`) in under a minute.

### Deploying via GitHub instead

If you'd rather connect it to a GitHub repo (so it auto-redeploys on every push):

1. Push this folder to a new GitHub repository
2. In Vercel, click **"Add New" → "Project"** → **"Import Git Repository"**
3. Select the repo, leave all settings as default (it's a static site, no build
   command needed), click **Deploy**

## How the forecasting works

All math lives in `index.html` inside a single `<script>` tag — no external ML
library. It's deliberately simple and readable:

- **Trend** is fit with ordinary least-squares regression (`y = slope·x + intercept`)
- **Seasonality** is a day-of-week additive offset — the average deviation from
  the overall mean for each weekday
- **Confidence bands** are based on the standard deviation of backtest residuals,
  widening slightly further out in the forecast
- **Accuracy (MAPE)** is computed by holding out the last 30 days, refitting on
  the rest, and comparing predictions to what actually happened

## Extending it

- Swap in real data: replace `genSeries()` with a `fetch()` call to your own CSV/API,
  as long as you map it to `{ date, value, dow }` objects
- Add more models (e.g., exponential smoothing) by adding another entry to the
  `MODELS` object — each just needs a `run(series, horizon)` function
- Add more datasets by adding entries to the `DATASETS` object

## Tech

Vanilla JavaScript + [Chart.js](https://www.chartjs.org/) (via CDN). No build tools,
no npm install, no framework.
