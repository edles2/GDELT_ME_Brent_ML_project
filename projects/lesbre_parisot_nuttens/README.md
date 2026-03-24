# GDELT × Brent — Geopolitical Tension as a Predictive Signal for Oil Prices

**Course:** Machine Learning for Finance
**Track:** Alternative Data
**Group:** lesbre / parisot / nuttens

---

## Research Question

Can a geopolitical tension score built from GDELT news events predict the short-term direction of Brent crude oil prices?

**Task:** Binary classification — will Brent close higher in 3 trading days?
**Period:** 2015–2024
**Target asset:** Brent crude futures (`BZ=F`)

---

## Approach

1. **Data collection** — Download GDELT 1.0 daily event files, filter to Middle East countries (IRN, IRQ, SAU, ISR, PSE, YEM, SYR, ARE, KWT). Fetch Brent prices via `yfinance`.
2. **Feature engineering** — Aggregate GDELT events into daily signals: Goldstein stability score, conflict event ratios, media mention intensity, rolling spikes.
3. **Modelling** — Logistic Regression and Random Forest with `TimeSeriesSplit` CV (no look-ahead bias).
4. **Evaluation** — Accuracy vs majority-class benchmark, feature importance, Sharpe ratio of a long/short backtest.

---

## Repository Structure

```
projects/lesbre_parisot_nuttens/
├── data/
│   ├── raw/gdelt/          # Per-day filtered GDELT CSV files (gitignored)
│   ├── raw/brent/          # Brent parquet (gitignored)
│   └── processed/          # Computed features + final dataset (gitignored)
├── src/
│   ├── data/
│   │   ├── download_gdelt.py   # GDELT bulk download + ME filter
│   │   ├── download_brent.py   # yfinance Brent download
│   │   └── build_dataset.py    # Merge + target construction
│   ├── features/
│   │   └── gdelt_features.py   # Daily aggregation + derived features
│   ├── models/
│   │   ├── benchmark.py        # Majority class + rolling volatility benchmarks
│   │   └── train.py            # CV training, evaluation
│   └── visualization/
│       └── plots.py            # All figures used in the presentation
└── notebooks/
    └── presentation.ipynb      # Final HTML presentation
```

---

## Installation

```bash
uv sync   # or: pip install -e .
```

## Reproduce

```bash
# 1. Download GDELT events (~several hours for 10 years)
python src/data/download_gdelt.py

# 2. Download Brent prices
python src/data/download_brent.py

# 3. Aggregate GDELT into daily features
python src/features/gdelt_features.py

# 4. Merge features + prices, construct target
python src/data/build_dataset.py

# 5. Train and evaluate models vs benchmarks
python src/models/train.py

# 6. Generate all presentation figures
python src/visualization/plots.py
```

---

## Key Limitations

- No transaction costs in the backtest — results are illustrative only.
- GDELT article coverage has grown over time (normalize by `n_articles` where possible).
- 2020 (COVID) and 2022 (Ukraine) are structural breaks — results should be tested with and without these periods.
- `TimeSeriesSplit` is mandatory: standard KFold introduces look-ahead bias.
