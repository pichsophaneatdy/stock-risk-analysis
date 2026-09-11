# Stock Diversification & Risk Analysis

A hands-on project applying Python, statistics, and linear algebra to a real question: **does
diversifying a stock portfolio actually reduce risk, or do the stocks just move together anyway?**

## Overview

The project analyzes a 9-asset basket — 8 individual U.S. stocks spanning different sectors, plus
a gold ETF as a deliberate diversifier — using ~10 years of daily price data (Aug 2016 – Aug 2026).
Individual stocks (rather than sector ETFs) were chosen specifically to make the diversification
effect as visible as possible; sector ETFs are already internally diversified, which would flatten
the contrast.

**Basket:** AAPL, GOOG, COST, CVX, JNJ, JPM, MCD, NVDA, GLD

## Methodology

The analysis moves through six stages, each building on the last:

1. **Data Acquisition** — pull and clean daily price data via `yfinance`.
2. **Individual Risk** — mean, volatility, and skew per stock; probability of a >5% single-day drop.
3. **Correlation Between Stocks** — a full pairwise correlation matrix and heatmap.
4. **Portfolio Risk** — combining individual risk and correlation into real portfolio variance via
   the `wᵀΣw` quadratic form; testing concentrated vs. diversified weightings.
5. **Predicting Future Risk** — a linear regression forecasting future volatility from prior
   volatility and trading volume.
6. **Composite Risk Score** *(in progress)* — synthesizing the above into a single 1-10 risk score
   per stock.

## Key Findings

- **Diversification is measurable, not just a saying.** An equal-weighted 9-stock portfolio has an
  annualized volatility of **17.06%** — lower than 8 of the 9 individual holdings, and well below a
  naive ~30-35% guess based on averaging individual volatilities.
- **Correlation, not just weight, determines a stock's true contribution to risk.** Removing GLD
  from the portfolio *increases* volatility (17.06% → 18.93%), while removing the far more volatile
  AAPL actually *decreases* it slightly (17.06% → 16.59%) — because AAPL's risk overlaps with the
  rest of the portfolio's, while GLD's doesn't. GLD's removal has roughly **4x** the impact of
  AAPL's, despite both holding identical portfolio weight.
- **Volatility alone can be misleading.** GLD has the lowest day-to-day volatility in the basket,
  but its strongly negative skew (-0.72) means its rare bad days are disproportionately severe — a
  risk that standard deviation alone doesn't capture, and that a normal-distribution probability
  model likely underestimates.
- **Prior volatility and volume have real, if modest, predictive power.** A linear regression using
  only these two features achieves R² ≈ 0.378 — explaining meaningful variation in future
  volatility, while leaving room for the genuinely unpredictable (news, regulation, unprecedented
  events like COVID-19).

## Tools & Stack

- Python, Jupyter Notebook, `conda` environment (`stockrisk`)
- `yfinance` for data acquisition
- `pandas`, `numpy` for data wrangling and linear algebra
- `scipy.stats` for probability modeling
- `scikit-learn` for regression and feature scaling
- `matplotlib`, `seaborn` for visualization
- Git + GitHub with `nbstripout` for clean notebook version control

## Running This Project

```bash
conda create -n stockrisk python=3.11
conda activate stockrisk
conda install pandas numpy matplotlib seaborn jupyter scikit-learn scipy
pip install yfinance

jupyter notebook
```

Open `stock_risk_analysis.ipynb` and run all cells top to bottom. The notebook re-downloads price
data live via `yfinance` on each run, so exact figures may drift slightly day to day.

## Repo Structure

```
.
├── stock_risk_analysis.ipynb   # main analysis notebook (Stages 1-6)
├── README.md
└── .gitignore
```

## Next Steps

- Complete Stage 6's composite risk score.
- Add a VIX-based market-sentiment feature to the Stage 5 regression.
- Explore one-hot encoding stock identity as an additional regression feature.