# Time-Series Foundation Model Benchmarks

This report surveys the evaluation ecosystem for time-series foundation models (TS FMs) — pre-trained forecasters designed to generalise across unseen time series in a zero-shot or few-shot manner. Unlike tabular FMs, TS FM benchmarking is relatively mature, with centralised hubs and living leaderboards. However, general-purpose TS FMs significantly underperform finance-specialised ones on financial data, making domain-specific evaluation critical.

---

## General-Purpose TS FM Benchmarks

### FEV Bench (Foundation Model Evaluation for Time Series)

| Attribute | Detail |
|---|---|
| **Type** | Zero-shot forecasting benchmark |
| **Focus** | Measures how well TS FMs generalise to completely unseen datasets without any fine-tuning or in-context adaptation |
| **Hosted on** | [TSFM.ai](https://tsfm.ai/benchmarks) |
| **Evaluation protocol** | Models receive a context window of historical values and must produce multi-step-ahead forecasts. Evaluated on held-out datasets not included in any known training corpus |
| **Metrics** | Skill score (composite metric normalising across datasets), MASE, SMAPE |
| **Current SOTA** | **Chronos-2** leads with a skill score of **35.50**, followed by Moirai 1.1 and TimesFM |

**Why it matters.** FEV Bench tests the purest form of the foundation model promise: can a pre-trained model forecast a series it has never seen, with no fine-tuning? The zero-shot setting is directly relevant to finance, where new instruments, markets, and regimes constantly appear and training data may be limited. A model that performs well on FEV Bench is more likely to generalise to novel financial series.

**Finance relevance.** FEV Bench includes some financial-adjacent series (economics, retail), but is not finance-focused. Performance on FEV Bench is a necessary but not sufficient condition for financial applicability — models need to additionally handle the non-stationarity, fat tails, and regime changes characteristic of financial time series.

---

### GIFT-Eval (Generalisation and Integration Framework for Time-series Evaluation)

| Attribute | Detail |
|---|---|
| **Type** | Probabilistic forecasting benchmark |
| **Focus** | Evaluates the quality of predictive distributions, not just point forecasts. Tests whether models produce well-calibrated uncertainty estimates |
| **Hosted on** | [TSFM.ai](https://tsfm.ai/benchmarks) |
| **Evaluation protocol** | Models must produce quantile forecasts (e.g., 10th, 50th, 90th percentiles) or full predictive distributions. Evaluated on calibration, sharpness, and coverage |
| **Metrics** | CRPS (Continuous Ranked Probability Score), calibration error, interval coverage |
| **Current SOTA** | **Moirai 1.1** leads on probabilistic accuracy, followed by Chronos-2 |

**Why it matters.** Point forecasts are insufficient for most financial applications. Risk management, portfolio optimisation, and regulatory capital calculations all require distributional forecasts — probability of loss exceeding a threshold, value-at-risk, expected shortfall. GIFT-Eval directly tests this capability, making it the most finance-relevant of the general-purpose benchmarks.

**Finance relevance.** High. Any TS FM deployed in a financial context must produce calibrated uncertainty estimates. A model that tops FEV Bench (point forecasts) but fails GIFT-Eval (probabilistic accuracy) would be unsuitable for most financial applications.

---

### BOOM (Benchmark for Operations and Observability Metrics)

| Attribute | Detail |
|---|---|
| **Type** | Infrastructure telemetry benchmark |
| **Focus** | Anomaly detection and forecasting in IT infrastructure metrics (CPU, memory, network, latency) |
| **Hosted on** | [TSFM.ai](https://tsfm.ai/benchmarks) |
| **Finance relevance** | **Low.** BOOM tests operational patterns (periodic server loads, traffic spikes) that are structurally different from financial time series. Included here for completeness as part of the TSFM.ai benchmark suite, but not recommended as a priority for the team |

---

### TSFM.ai — Central Hub

[TSFM.ai](https://tsfm.ai/benchmarks) serves as the central hub for all three benchmarks above, providing:
- **Living leaderboards** updated as new models and results are submitted
- **Standardised evaluation code** for reproducible comparisons
- **Model zoo** with pre-trained checkpoints for major TS FMs (Chronos, Moirai, TimesFM, Lag-Llama, etc.)

For the team, TSFM.ai is the starting point for understanding the general-purpose TS FM landscape. However, the leaderboard rankings should be interpreted cautiously for financial applications — as demonstrated below, finance-specific evaluation tells a very different story.

---

## Finance-Specific TS FM Evaluation

### Kronos — Purpose-Built Financial TSFM

| Attribute | Detail |
|---|---|
| **Type** | Domain-specific time-series foundation model for finance |
| **Reference** | [arXiv (2508.02739)](https://arxiv.org/html/2508.02739v1) |
| **Architecture** | Transformer-based, pre-trained on a curated corpus of financial time series (prices, volumes, volatility, macro indicators) |
| **Key result** | **93% improvement in price-series forecasting RankIC** over general-purpose TS FMs (Chronos, Moirai, TimesFM) on held-out financial evaluation sets |
| **Training data** | Curated financial time series — unlike general TS FMs that train on heterogeneous corpora (weather, energy, traffic, retail, etc.) |
| **Evaluation focus** | Financial-specific metrics: RankIC (rank information coefficient — measures the correlation between predicted and actual cross-sectional rankings), directional accuracy, financial return metrics |

**Why Kronos matters.** The 93% RankIC improvement over general TS FMs is a striking result. It demonstrates that:
1. **General TS FMs are not sufficient for finance.** Despite strong performance on FEV Bench and GIFT-Eval, models like Chronos-2 and Moirai dramatically underperform on financial series. This is attributable to the fundamentally different statistical properties of financial data: non-stationarity, volatility clustering, fat-tailed returns, mean-reversion/momentum regimes, and low signal-to-noise ratio.
2. **Domain-specific pre-training matters.** Kronos's advantage comes from pre-training on financial series specifically, suggesting that the features learned from weather, traffic, and retail data do not transfer well to finance.
3. **Financial evaluation requires financial metrics.** Standard TS metrics (MASE, SMAPE, CRPS) may not capture what matters in finance. RankIC, directional accuracy, and return-based metrics (Sharpe ratio, maximum drawdown) are more aligned with practical utility.

---

### timesfm_fin — Fine-Tuned TimesFM for Finance

| Attribute | Detail |
|---|---|
| **Type** | Fine-tuned version of Google's TimesFM, specialised for financial forecasting |
| **Reference** | [github.com/pfnet-research/timesfm_fin](https://github.com/pfnet-research/timesfm_fin) |
| **Base model** | TimesFM (Google) — a general-purpose TS FM |
| **Fine-tuning approach** | Domain adaptation on financial price series and related data |
| **Key result** | Achieves **Sharpe ratio of 1.68** on an S&P 500 trading strategy based on model forecasts — a strong risk-adjusted return that validates practical financial utility |
| **Evaluation** | Goes beyond forecast accuracy to test economic value: can the model's predictions be profitably traded after accounting for transaction costs and risk? |

**Why timesfm_fin matters.** While Kronos demonstrates that building a financial TS FM from scratch outperforms general models, timesfm_fin shows that **fine-tuning** a general TS FM on financial data is also effective. The Sharpe ratio of 1.68 is notable because:
- It evaluates the model on a metric that matters to practitioners (risk-adjusted return), not just statistical accuracy
- It suggests that even if the team starts with a general TS FM, domain-specific fine-tuning can unlock significant financial value
- The fine-tuning approach is more accessible than training Kronos from scratch, as it requires less data and compute

---

## General vs. Finance-Specific: Performance Gap Summary

| Model | Type | FEV Bench (general) | Financial RankIC | Financial Sharpe | Notes |
|---|---|---|---|---|---|
| Chronos-2 | General TS FM | **35.50** (SOTA) | Baseline | N/R | Strong general, weak on finance |
| Moirai 1.1 | General TS FM | ~33 | Baseline | N/R | Best probabilistic, weak on finance |
| TimesFM | General TS FM | ~31 | Baseline | N/R | Google's model, moderate general |
| Kronos | Financial TS FM | N/R | **+93%** vs. general | N/R | Purpose-built for finance |
| timesfm_fin | Fine-tuned general | N/R | N/R | **1.68** | Fine-tuned TimesFM on finance |

N/R = not reported in available literature.

**Key insight.** There is a wide and consistent gap between general-purpose TS FM performance and finance-specific performance. General models that dominate TSFM.ai leaderboards may significantly underperform on financial data. This makes domain-specific evaluation non-negotiable for the team.

---

## Evaluation Methodology Recommendations

For financial TS FM evaluation, the team should adopt:

1. **Financial metrics alongside statistical metrics.** Report both standard TS metrics (MASE, CRPS, SMAPE) and financial metrics (RankIC, directional accuracy, Sharpe ratio, maximum drawdown). A model that is statistically accurate but unprofitable is not useful; a model that is statistically mediocre but profitable may be valuable.

2. **Probabilistic evaluation.** Following GIFT-Eval's lead, require distributional forecasts and evaluate calibration. VaR and ES estimation depend on tail accuracy, not mean accuracy.

3. **Regime-aware evaluation.** Split evaluation by market regime (bull, bear, high-volatility, low-volatility, crisis). A model that forecasts well in calm markets but fails during stress periods has limited practical value.

4. **Rolling-window backtesting.** Use expanding or rolling training windows with forward-looking test periods. Never allow future data to leak into the training set.

5. **Cross-asset evaluation.** Test on equities, fixed income, forex, and commodities to assess cross-asset generalisation. Financial TS FMs that only work on equities have limited scope.

6. **Include both general and financial baselines.** Compare against (a) general TS FMs (Chronos-2, Moirai), (b) financial TS FMs (Kronos, timesfm_fin), and (c) classical baselines (ARIMA, exponential smoothing, random walk).

---

## Key References

- [TSFM.ai — benchmarks hub](https://tsfm.ai/benchmarks) (FEV Bench, GIFT-Eval, BOOM)
- [Kronos — financial TSFM](https://arxiv.org/html/2508.02739v1)
- [timesfm_fin — fine-tuned TimesFM](https://github.com/pfnet-research/timesfm_fin)
- Chronos: Ansari et al. (2024) — Amazon's TS FM
- Moirai: Woo et al. (2024) — Salesforce's universal TS FM
- TimesFM: Das et al. (2024) — Google's TS FM
