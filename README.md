# TND-Intrinsic-Value-Model
# Real-Time Intrinsic TND Valuation Model

A real-time fair-value model for the Tunisian Dinar (USD/TND) built for the **IN 22-21 Times Series Analysis** course at HIDE Institute, under the supervision of Dr Eymen Errais.

---

## Overview

The Central Bank of Tunisia (BCT) publishes an official USD/TND fixing rate twice daily. In practice, banks trade at a different price — the interbank (IB) rate — which reflects real local market conditions. This model bridges the gap by estimating a continuously updating **intrinsic value** for USD/TND that accounts for both global currency fundamentals and local Tunisian liquidity conditions.

---

## Model Architecture

The model combines two components:

### 1. Basket-Based Baseline
Estimates where USD/TND should be based on movements in three international currency pairs:

```
Baseline(t) = FIX(t-1) × (1 + w₁×%ΔEUR/USD + w₂×%ΔGBP/USD + w₃×%ΔUSD/JPY)
```

Weights estimated via OLS regression on 6 years of historical data.

### 2. AR(1) Spread Nowcasting
The IB rate deviates from the BCT fixing due to local liquidity pressures. Since the official IB rate is published with a one-day lag, an AR(1) model nowcasts today's spread:

```
Spread(t) = 0.000807 + 0.934 × Spread(t-1)
```

### Final Intrinsic Value
```
Intrinsic Value(t) = Baseline(t) + Nowcasted Spread(t)
```

---

## Results

| Period | Regime | MAE (TND) | Correlation |
|---|---|---|---|
| 2020–2021 | Calm | 0.00549 | 0.9934 |
| 2022–2023 | Crisis (dollar scarcity) | 0.00846 | 0.9936 |
| 2024–2026 | New regime (dollar surplus) | 0.00727 | 0.9937 |

**The intrinsic value tracks the actual interbank rate with over 99.3% correlation across all market conditions — including during the 2022–2023 dollar scarcity crisis when the official BCT fixing significantly lagged the market.**

---

## Key Findings

- EUR/USD is the dominant driver of TND movements (weight = −0.352, p < 0.001)
- The IB-fixing spread has a lag-1 autocorrelation of 0.93 — making it highly predictable
- Three distinct market regimes were identified over 2020–2026
- The intrinsic value outperforms the BCT fixing as a real-time proxy for the IB rate

---

## Repository Structure

```
├── FX Data.xlsx                      # Raw data (4 sheets)
├── fx_clean.csv                      # Cleaned master dataset
├── TND_Model.ipynb                   # Full Jupyter notebook
├── TND_Intrinsic_Value_Report.docx   # Project report
├── 01_overview.png                   # Data overview chart
├── 02_basket_baseline.png            # Basket regression results
├── 03_spread_analysis.png            # Spread analysis chart
├── 04_nowcast.png                    # Nowcast performance chart
├── 06_backtest.png                   # Backtesting across regimes
└── README.md
```

---

## Methodology

| Step | Description |
|---|---|
| Data Loading & Cleaning | Aligned 4 data sources, 1,513 trading days (Apr 2020 – Jan 2026) |
| Basket Regression | OLS regression of USD/TND on EUR/USD, GBP/USD, USD/JPY |
| Spread Analysis | ADF test, autocorrelation analysis, regime identification |
| Nowcasting | AR(1) model trained on 2020–2024, tested on 2025–2026 |
| Integration | Baseline + nowcasted spread = intrinsic value |
| Backtesting | Out-of-sample evaluation across 3 distinct market regimes |

---

## Technologies

- **Python 3.12**
- **pandas** — data manipulation and alignment
- **numpy** — numerical computations
- **statsmodels** — OLS regression, AR(1) model, ADF test
- **matplotlib** — visualization
- **scikit-learn** — MAE and RMSE evaluation metrics

---

## Course

**IN 22-21 — Times Series Analysis**
HIDE Institute
Instructor: Dr Eymen Errais
