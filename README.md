# High-Frequency Stock Volatility Forecasting

## Overview

This project investigates whether high-frequency stock market data can improve next-day volatility forecasting using Realized Volatility (RV) and the Heterogeneous Autoregressive Realized Volatility (HAR-RV) model.

## Research Question

Can HAR-RV outperform simple historical-volatility benchmarks in forecasting next-day realized variance?

## Objectives

* Obtain and clean intraday stock price data.
* Calculate realized variance from five-minute returns.
* Construct daily, weekly, and monthly volatility features.
* Train and evaluate HAR-RV.
* Compare its performance with naive RV, rolling-mean, and GARCH benchmarks.
* Evaluate forecasts using MAE, RMSE, and QLIKE.

## Planned Methodology

1. Data collection and preprocessing
2. Intraday return calculation
3. Realized variance estimation
4. HAR-RV model development
5. Chronological out-of-sample evaluation
6. Robustness checks across stocks and market conditions

## Tools

Python, Pandas, NumPy, Matplotlib, scikit-learn, Jupyter Notebook, and GitHub.

## Project Status

Planning and literature review.

## Research Integrity

The project will document data sources, preprocessing decisions, model configurations, and evaluation procedures. Results will be reported after experiments have been conducted.
## Timeline

oct 2026- Aug 2027
