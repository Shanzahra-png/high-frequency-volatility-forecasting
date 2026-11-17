# High-Frequency Volatility Forecasting | VolatilityLab

[![Open In Colab]([https://colab.research.google.com/assets/colab-badge.svg](https://colab.research.google.com/drive/12Ca9dIlyGcwCaPr5YR_BV-XGPJIPE22o#scrollTo=be9d5f18

**A research-driven quantitative finance and forecasting project.**
Research: high-frequency volatility forecasting with realized volatility and HAR-RV models.
Application (planned): VolatilityLab, an interactive volatility research and forecasting platform.

**Status: Phase 1 (research experiments) in progress. Results below are preliminary.**
Project timeline: Oct 2026 to Aug 2027.

---

## Phase 1 results (preliminary)

Phase 1 studies next-day and multi-day volatility forecasting for **BTCUSDT and ETHUSDT** using Binance 1-minute data (2020 to Oct 2026), with chronological out-of-sample evaluation. The whole pipeline is one reproducible Colab notebook: `notebooks/research_experiments/HAR_RV_pipeline_v1_2.ipynb`.

### What the experiments show so far

1. **HAR-RV beats the historical benchmarks.** One-day-ahead, HAR has about 22% lower log-MSE and 34% lower QLIKE than the naive (yesterday) forecast, and clearly beats the 22-day rolling mean (table below).
2. **Calendar effects matter most at the 1-day horizon.** Adding Saturday, Sunday and Monday dummies to HAR roughly halves the log-MSE and cuts QLIKE by about 40%. In the full sample, Saturday volatility is about 40% lower for BTC (32% for ETH) than on Tuesday to Friday, and Monday is about 19% higher (14% for ETH).
3. **The weekday pattern is not stable.** The Saturday effect strengthened between 2020-21 and 2023 and then stayed roughly constant; the Sunday effect faded in 2025-26.
4. **A fixed 2-year estimation window beats an expanding window** on log-MSE in both assets, with no detectable QLIKE gain.
5. **The weekly and monthly HAR terms add a small but significant gain** over AR(1) in log-MSE at 1 and 7 days. At 28 days the weekly average alone is nearly as good as the full HAR (exploratory).
6. **QLIKE supports the lag structure much less than log-MSE.** Both losses are reported.

### One-day-ahead results (BTCUSDT, test period 2025-01 to 2026-10, 730-day rolling window)

| Model | log-MSE | QLIKE |
|---|---|---|
| Naive (yesterday) | 0.6956 | 0.3942 |
| Mean of last 22 days | 0.7222 | 0.3507 |
| AR(1) | 0.5548 | 0.2565 |
| HAR | 0.5436 | 0.2589 |
| HAR + weekend | 0.3657 | 0.2049 |
| **HAR + Sat/Sun/Mon** | **0.2664** | **0.1579** |

Lower is better. QLIKE forecasts use the variance-corrected prediction `exp(p + s²/2)`.

### Replication scorecard (five primary tests, Holm-corrected within each asset and period)

| Primary test | BTC 2023-24 | BTC 2025-26 | ETH 2023-24 | ETH 2025-26 |
|---|---|---|---|---|
| 1. Weekday dummies vs HAR (h = 1) | pass | pass | pass | pass |
| 2. Fixed window vs expanding (h = 1) | pass | pass | pass | pass |
| 3. HAR vs AR(1), nested (h = 1) | pass | pass | pass | pass |
| 4. HAR vs weekly-only, nested (h = 7) | pass | pass | pass | pass |
| 5. HAR vs weekly-only, nested (h = 28), exploratory | pass | FAIL | pass | pass |

"Pass" = positive statistic and Holm-adjusted p < 0.05. The pooled 2023-26 column agrees (`reports/phase1_results/replication_scorecard.csv`).

### Limitations

* Preliminary: Phase 1 covers two assets. More assets (BNBUSDT, XRPUSDT, SOLUSDT) and a GARCH benchmark are planned.
* BTC and ETH volatility move together, so they are not independent samples.
* The weekday-dummy specification and the window grid were chosen after looking at this data. Forecasts use only past data, but the choice of specification is not fully out-of-sample, so other assets and periods are treated as replications.
* At the 28-day horizon the effective number of independent test windows is small (about 25 in two years); p-values there are indicative only.
* Days with missing minutes (older data) are flagged, not dropped.
* No jump separation, microstructure-noise correction or intraday seasonality yet.
* Calendar effects in crypto volatility have been discussed before. This repository contributes an out-of-sample forecasting comparison with replication tests, not the discovery of the effect.

---

## Research motivation and question

Accurate volatility forecasting matters for understanding market uncertainty and risk. High-frequency observations contain information that daily closing prices alone do not capture.

**Research question:** Can high-frequency realized volatility measures and the HAR-RV model improve volatility forecasting compared with conventional historical-volatility benchmarks, and which model ingredients (lag structure, calendar effects, estimation window, horizon) account for the improvement?

## Methodology (as implemented in Phase 1)

* **Data:** Binance spot 1-minute klines from `data.binance.vision` (monthly files; handles the 2025 switch from millisecond to microsecond timestamps). Raw files are not stored in this repository; the notebook downloads them.
* **Realized variance:** sum of squared 1-minute log returns per UTC day; returns across data gaps and month boundaries are kept.
* **Models:** naive, 22-day rolling mean, AR(1), HAR (daily, weekly, monthly log-RV), HAR + weekend dummy, HAR + Sat/Sun/Mon dummies. Horizons 1, 7 and 28 days.
* **Estimation:** OLS re-fitted every day on data fully known at the forecast origin (no look-ahead); rolling 730-day window by default, with 365, 1095 and expanding windows as sensitivity checks.
* **Evaluation:** chronological out-of-sample forecasts scored with log-MSE and QLIKE (Patton, 2011); Diebold-Mariano and Clark-West tests with Newey-West standard errors; Holm correction over five pre-declared primary hypotheses.
* **One run for all periods:** forecasts are made once for the whole span, and each test period only selects which forecasts are scored (identical to separate runs).

## Roadmap

| Item | Status |
|---|---|
| Data pipeline, daily realized variance, validation | Done |
| Baselines, HAR-RV, calendar effects, window and horizon experiments | Done (BTC, ETH) |
| Replication on more assets (BNB, XRP, SOL) | Planned next |
| GARCH benchmark | Planned |
| MAE / RMSE added to the evaluation | Planned |
| Jump-robust RV, time-varying weekday effects | Planned |
| Stock-market data | Not started |
| VolatilityLab web application | Planned (see below) |

## VolatilityLab: application vision (planned, not yet built)

A custom web application connecting the research workflow with interactive analytics. Planned modules: Research Dashboard, Dataset Explorer, Volatility Analysis, Model Experiments, Forecasting Engine, Model Evaluation, Research Reports. These will be implemented incrementally after the research models are validated.

**Planned stack:** Python (FastAPI backend), React frontend, MySQL, Plotly. 
**Used so far:** Python, NumPy, pandas, SciPy, statsmodels, Matplotlib, Google Colab, Git and GitHub.

## Repository structure

```
notebooks/research_experiments/   HAR_RV_pipeline_v1_2.ipynb (the whole Phase 1 pipeline)
reports/phase1_results/           result tables and figures (CSV, PNG), one folder per asset
README.md
```

Further folders (`src/`, `app/`, `tests/`) will be added as the code is refactored into modules and the application is built.

## How to run

1. Click the Colab badge above (or upload the notebook to Colab).
2. Edit the configuration cell if needed (`SYMBOLS`, `TEST_PERIODS`).
3. Runtime, then Run all. The first run takes 10 to 25 minutes (downloads); re-runs use the cache and take a few minutes.

Requires Python 3, pandas, numpy, scipy, statsmodels, matplotlib (all preinstalled in Colab).

## Research integrity

Data sources, preprocessing decisions, model configurations, evaluation procedures and limitations are documented here and in the notebook. All reported performance numbers are chronological out-of-sample results. Results are preliminary and will be updated as experiments are extended. Nothing in this repository is investment advice, and the results do not demonstrate a profitable trading strategy.

## References

* Corsi, F. (2009). A simple approximate long-memory model of realized volatility. *Journal of Financial Econometrics*.
* Andersen, Bollerslev, Diebold & Labys (2003). Modeling and forecasting realized volatility. *Econometrica*.
* Patton, A. (2011). Volatility forecast comparison using imperfect volatility proxies. *Journal of Econometrics*.
* Diebold, F. & Mariano, R. (1995). Comparing predictive accuracy. *Journal of Business & Economic Statistics*.
* Clark, T. & West, K. (2007). Approximately normal tests for equal predictive accuracy in nested models. *Journal of Econometrics*.

**Author:** Shan Zahra · GitHub: https://github.com/Shanzahra-png

**License:** MIT for the code (add a `LICENSE` file). https://github.com/Shanzahra-png

oct 2026- Aug 2027
