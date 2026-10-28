# Overfitting-Diagnostics

![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![License: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)
[![nbviewer](https://img.shields.io/badge/notebook-nbviewer-F37626?style=flat-square&logo=jupyter&logoColor=white)](https://nbviewer.org/github/jettjolliffe/Overfitting-Diagnostics/blob/main/overfitting_diagnostics.ipynb)

Strategy research is a search, and the best result of a search is biased upwards even when every backtest in it is computed correctly. This note works through the statistics that correct for the search: the probabilistic and deflated Sharpe ratios, the probability of backtest overfitting, White's Reality Check and Hansen's test for superior predictive ability. It derives each one, checks it by simulation and applies all of them to a research program of well-known market-neutral strategies on real data, net of costs.

| File | Contents |
|---|---|
| [`overfitting_diagnostics.ipynb`](overfitting_diagnostics.ipynb) | The note: derivations, code, simulations, figures and the case study. Every estimator is implemented next to its derivation. |
| [`overfitting_diagnostics.pdf`](overfitting_diagnostics.pdf) | The same note as a paper, without the code. |

GitHub renders the notebook with its equations and figures; the nbviewer badge above opens the same notebook in nbviewer, which loads large notebooks faster.

## Contents

1. A search on pure noise, and why the best result of it looks significant.
2. The sampling distribution of the Sharpe ratio, derived with the delta method for non-normal returns; the probabilistic Sharpe ratio and minimum track record length.
3. The expected maximum of $N$ Sharpe ratios: the exact expectation, the extreme-value approximation behind the deflated Sharpe ratio, and the effect of correlation between trials.
4. The deflated Sharpe ratio.
5. The probability of backtest overfitting: combinatorially symmetric cross-validation, and what PBO does and does not measure.
6. White's Reality Check and Hansen's SPA: the composite null, the stationary bootstrap and its long-run variance, studentisation and recentring, block length, and the size and power of every method.
7. A calibration study on 200 simulated markets.
8. A research program on real data: 44 variants of time-series momentum, industry momentum, betting against beta, short-term reversal and low volatility, each beta-hedged and managed to a 15% volatility target with daily GARCH(1,1) forecasts, gross and net of trading, financing and hedging costs, on multi-asset ETFs (2008–2026) and US industry portfolios (1973–2026).
9. Practical notes and limitations.

Appendix A checks the computational shortcuts in each estimator against brute-force calculations.

## Main findings

**Ignoring the search is a large error.** With 50 independent strategies and no edge anywhere, the best one passes a 5% significance test on its own in more than 90% of simulated searches. Bonferroni, the Reality Check and SPA keep false positives near 5%; the deflated Sharpe ratio keeps them near zero, at a substantial cost in power.

**Correct tests still need data.** A strategy with a true Sharpe ratio of 1.0, hidden among 49 without an edge, is detected in at best about a quarter of five-year searches by the corrected tests.

**PBO measures the selection step, not profitability.** When every strategy in a search shares the same edge, choosing the in-sample winner is a coin toss and PBO stays near 0.5 however large the edge is.

**On real data, nothing survives the recent sample.** Every book is market-neutral (beta-hedged with index futures) and managed to a 15% annual volatility target: its volatility is forecast every day with a GARCH(1,1) model estimated only on earlier data, and exposure is adjusted when it drifts more than 10% from target. All figures are net of trading, financing and hedging costs.

| | 2008–2026, all 44 books | 1973–2026, 34 industry books |
|---|---|---|
| Best book | low volatility (252 days) | industry momentum (12 months) |
| Net Sharpe ratio (same book at constant exposure) | 0.49 (0.26) | 0.66 (0.45) |
| $t$-statistic, dependence-adjusted | 1.9 | 3.9 |
| Reality Check $p$ / SPA $p$ | 0.29 / 0.33 | 0.001 / 0.002 |
| PBO | 0.44 | 0.02 |
| Deflated Sharpe ratio, trials' variance / $1/T$ | 0.03 / 0.44 | 0.000 / 0.997 |

Over 54 years industry momentum passes the bootstrap tests and PBO, whether the books are managed with GARCH, EWMA or a 63-day window or held at constant exposure. But those years include the decades in which it was discovered, its Sharpe ratio since 2008 is 0.16, and at constant exposure its dependence-adjusted $t$-statistic is only 3.0.

**The deflated Sharpe ratio needs care on real searches.** It builds its luck threshold from the variance of the trials' Sharpe ratios. When a search mixes good and bad strategies, that variance also contains their real differences, and the DSR rates even the 54-year momentum result as luck. With the null variance $1/T$ it agrees with the other tests on the managed books.

**Costs decide fast strategies.** Short-term reversal turns over 48 to 228 times its capital a year; every variant loses money net of costs, and the best breaks even at a trading cost of about 1 basis point.

## Running the notebook

```bash
python -m venv .venv
source .venv/bin/activate
pip install numpy scipy pandas matplotlib jupyterlab yfinance
jupyter lab overfitting_diagnostics.ipynb
```

Python 3.10 or later. Every simulation is seeded, so a fresh run reproduces every number and figure; it takes about five minutes on one core. Section 8 downloads the 49 industry portfolios and the Fama–French factors from Kenneth French's data library and ETF prices from Yahoo Finance on the first run, and caches them in `data/`, which is not tracked. The published results use French's release built from the September 2026 CRSP file. Both sources revise past data (French occasionally, Yahoo whenever a fund pays a distribution), so a later download can move the Section 8 numbers slightly.

## References

The full list, including the references for the five strategies, is at the end of the note. The main sources are Bailey & López de Prado (2012, 2014), Bailey, Borwein, López de Prado & Zhu (2017), White (2000), Hansen (2005), Politis & Romano (1994), Lo (2002), Mertens (2002) and Harvey, Liu & Zhu (2016). Industry and factor data: Kenneth R. French, Data Library.

## License

MIT. See [LICENSE](LICENSE).
