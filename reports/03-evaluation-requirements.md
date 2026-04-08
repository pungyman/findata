# Evaluation Requirements Framework

This report defines cross-cutting criteria for dataset selection and evaluation design, applicable regardless of which model paradigm the team ultimately pursues. It synthesises requirements from the data source reports (`01-data-sources/`) and benchmark reports (`02-benchmarks/`) into a unified framework.

---

## Dataset Selection Criteria Matrix

The following criteria should be assessed for every candidate dataset before including it in an evaluation suite. Criteria are adapted from the [Unidata blog post](https://unidata.pro/blog/best-financial-ml-datasets/) and expanded based on the findings from our data source and benchmark surveys.

| Criterion | What to assess | Why it matters | Scale |
|---|---|---|---|
| **Time resolution** | Tick / minute / hourly / daily / weekly / monthly / quarterly / annual | Determines which model architectures are applicable. Sub-daily data supports TS FMs and HFT models; annual data suits panel tabular models | Continuous |
| **Completeness** | % of missing values; pattern of missingness (random vs. systematic vs. stale) | Missing data degrades all paradigms. Systematic missingness (e.g., survivorship bias in delisted stocks) introduces bias. Random missingness is more manageable | 0–100% |
| **Domain fit** | Market / macro / corporate / consumer / alternative | Determines which financial questions can be answered. Market data for trading, macro for forecasting, corporate for credit/valuation | Categorical |
| **Label availability** | None / derived / expert-annotated / programmatic | Directly determines whether supervised evaluation is possible. Most financial datasets lack labels (see key gap in data source reports) | Categorical |
| **Label quality** | Inter-annotator agreement (for text); signal-to-noise ratio (for derived labels); label lag | Low-quality labels create noisy evaluation — a model might appear weak simply because the labels are unreliable | Qualitative |
| **Licensing** | Public domain / CC BY / CC BY-NC / academic-only / proprietary | Determines reproducibility, shareability, and commercial viability. Public domain (EDGAR) > CC BY (World Bank) > proprietary (Bloomberg) for open research | Categorical |
| **Update frequency** | Real-time / daily / monthly / quarterly / annual / frozen | Frozen datasets (Quandl WIKI, 2018) cannot evaluate recent-period performance. Living datasets enable temporal out-of-sample testing | Categorical |
| **Scale** | Rows × features × time span | Different paradigms have different scale sweet spots. Tabular FMs excel at 1K–100K rows; TS FMs need long series; LLMs need large text corpora | Varies |
| **Multimodal compatibility** | Can text, numeric, and image data be aligned by entity and timestamp? | Critical if the team pursues multimodal approaches. Pre-aligned datasets (FinMultiTime, FNSPID) save weeks of data engineering | Binary |
| **Geographic scope** | US-only / developed markets / global / regional (EU, APAC) | Models trained on US data may not generalise to other markets. Cross-geography evaluation tests robustness | Categorical |
| **Staleness risk** | How quickly does the data become unrepresentative of current conditions? | Financial regimes shift. Models evaluated on 2010-era data may not reflect 2025 market dynamics | Qualitative |

---

## Paradigm-Specific Requirements

Each model paradigm imposes additional requirements beyond the general criteria above.

### Tabular Foundation Models

| Requirement | Detail | Priority |
|---|---|---|
| **Diverse feature types** | Mix of numeric (continuous) and categorical features. Tabular FMs are designed to handle heterogeneous schemas without manual encoding | Must have |
| **Moderate scale** | Current tabular FM SOTA (TabPFN, TabICLv2) excels at 1K–100K rows. Performance advantage over gradient boosting narrows above 100K | Important |
| **Labelled classification/regression targets** | Tabular FMs are supervised — they need explicit targets (default/no-default, return quintile, claim amount) | Must have |
| **Cross-sectional structure** | Rows represent independent or near-independent observations at a point in time (e.g., all companies in Q4 2024). Temporal dependencies should be handled through feature engineering, not model architecture | Should have |
| **Standardised train/test splits** | Time-based splits (train on past, test on future) to prevent lookahead bias. TabArena's multi-setting protocol (default/tuned/ensembled) should be adopted | Must have |
| **Multiple metrics** | AUC-ROC, F1, calibration (Brier score, ECE) for classification; RMSE, MAE for regression. Financial tasks are often imbalanced | Should have |

**Best candidate datasets:** FRED-MD (macro regime classification), yfinance + derived technicals (stock movement), LendingClub (credit default), EUROFIDAI (European corporate events).

### Time-Series Foundation Models

| Requirement | Detail | Priority |
|---|---|---|
| **Consistent temporal resolution** | Regular sampling (daily, monthly) with minimal gaps. Irregular series require interpolation or specialised architectures | Must have |
| **Sufficient history length** | TS FMs need long context windows (hundreds to thousands of timesteps) for effective pattern learning. Short series (<100 points) are problematic | Must have |
| **Multiple related series** | Multivariate evaluation: can the model leverage cross-series correlations (e.g., sector co-movement, macro factor structure)? | Should have |
| **Point and distributional targets** | Evaluate both point forecast accuracy (MASE, RMSE) and probabilistic forecast quality (CRPS, calibration). Risk management requires distributional forecasts | Must have |
| **Financial metrics** | RankIC, directional accuracy, Sharpe ratio, maximum drawdown. Standard TS metrics alone are insufficient — see Kronos evaluation (93% RankIC improvement invisible to MASE) | Must have |
| **Regime-aware splits** | Separate evaluation across market regimes (bull/bear, high/low volatility, crisis). A model that works in calm markets but fails during stress has limited value | Should have |
| **Cross-asset coverage** | Test on equities, bonds, forex, commodities. Single-asset evaluation overestimates generalisability | Should have |

**Best candidate datasets:** yfinance daily prices (equities), FRED-MD (macro multivariate), Alpha Vantage intraday (sub-daily), GFD (long-history stress testing), FinMultiTime OHLCV component.

### Financial LLMs / SLMs

| Requirement | Detail | Priority |
|---|---|---|
| **Text corpora** | Filings (EDGAR), news, transcripts, reports. Scale matters — BloombergGPT used 363B tokens | Must have (for training) |
| **QA pairs** | Financial question-answer pairs with ground truth, ideally requiring numerical reasoning (FinQA, TATQA, SEC-QA) | Must have (for evaluation) |
| **Sentiment labels** | Sentence or document-level sentiment annotations from financial domain experts (FPB, FiQA-SA) | Should have |
| **Numerical reasoning examples** | Questions that require multi-step arithmetic over financial tables (FinQA is the gold standard) | Must have |
| **Hallucination ground truth** | Known-correct financial facts against which model outputs can be checked for fabricated numbers, dates, or entities | Should have |
| **Multilingual coverage** | Financial text in languages beyond English — Chinese, Japanese, German, French, Spanish. MultiFinBen addresses this | Nice to have |
| **Sub-domain diversity** | Evaluation across FinBen's 8 domains (IE, textual analysis, QA, generation, forecasting, risk, decision-making, multilingual) rather than aggregated scores | Must have |

**Best candidate datasets:** SEC EDGAR (pre-training), FinBen suite (evaluation), SEC-QA (document QA), FinNLI (reasoning), FPB (sentiment), ECTSum/EDTSum (summarisation).

---

## Cross-Paradigm Evaluation: The "Meta-Evaluation" Concept

A unique opportunity in this survey is the possibility of defining evaluation tasks that can be formulated across all three paradigms, enabling direct comparison of model types on the same financial question. This is the "meta-evaluation" concept.

### Stock Movement Prediction as a Paradigm Bridge

Stock movement prediction (predicting whether a stock will go up or down tomorrow) can be formulated for each paradigm:

| Paradigm | Formulation | Input | Model type |
|---|---|---|---|
| **Tabular FM** | Binary classification on (stock, date) rows | Technical indicators, fundamentals as features | TabPFN, TabICLv2 |
| **Time-Series FM** | Directional forecast from price history | OHLCV series as input context | Chronos, Kronos, timesfm_fin |
| **LLM/SLM** | Text-conditioned prediction | News headlines, filing excerpts, analyst reports | FinGPT, GPT-4 with financial prompt |
| **Multimodal** | Fusion of all above | Price + text + fundamentals | FinMultiTime-based evaluation |

**Data sources for this task:** yfinance (price), FNSPID (price + news), FinMultiTime (all modalities).

This design enables the team to answer the question: *For a specific financial prediction task, which paradigm adds the most value?* It also reveals whether multimodal fusion outperforms any single paradigm.

### Other Candidate Paradigm-Bridging Tasks

| Task | Tabular FM | TS FM | LLM/SLM |
|---|---|---|---|
| **Macro recession forecasting** | FRED-MD features as tabular classification | FRED-MD series as multivariate TS forecast | FOMC statements + macro news as text input |
| **Corporate credit rating** | Financial ratios as tabular features | Revenue/earnings series as TS | Filing text + analyst reports as LLM input |
| **Earnings surprise prediction** | Historical EPS + fundamentals as tabular | Quarterly earnings series as TS | Earnings call transcript analysis |

---

## Evaluation Philosophy

### Guiding Principles

1. **Financial utility over statistical accuracy.** A model that is statistically mediocre but produces profitable trading signals or accurate risk estimates is more valuable than one with low RMSE but no practical utility. Always report financial metrics alongside standard ML metrics.

2. **Regime robustness over average performance.** Financial models are tested in the worst moments (2008, 2020 March, rate shock cycles). Split evaluation by regime and report worst-case performance explicitly.

3. **Reproducibility as a hard constraint.** Any benchmark the team builds should be reproducible by others without proprietary data access. This means building on open data (EDGAR, FRED, yfinance) and publishing data processing code.

4. **Honest baselines.** Include simple baselines (random walk for TS, logistic regression for tabular, zero-shot GPT for LLM) to calibrate expectations. Many sophisticated models fail to beat simple baselines on financial data.

5. **Temporal integrity.** Never allow future information to leak into training or features. Use strict time-based splits, point-in-time features, and forward-looking test windows.

---

## Minimum Viable Evaluation Suite

For a team at the early exploration stage, the following minimum viable evaluation suite covers all three paradigms with freely available data:

| Component | Dataset | Task | Paradigm tested |
|---|---|---|---|
| **Tabular evaluation** | FRED-MD (monthly panel) | Recession classification (NBER dates as labels) | Tabular FMs |
| **Tabular evaluation** | yfinance + derived technicals | Next-day return direction (S&P 500) | Tabular FMs |
| **TS evaluation** | yfinance daily prices | Multi-step price forecasting (S&P 500 constituents) | TS FMs |
| **TS evaluation** | FRED-MD (128 series) | Multivariate macro forecasting | TS FMs |
| **LLM evaluation** | FPB | Sentiment classification | LLMs/SLMs |
| **LLM evaluation** | FinQA | Numerical reasoning over financial tables | LLMs/SLMs |
| **Cross-paradigm** | FNSPID or FinMultiTime | Stock movement prediction (tabular + TS + text formulations) | All three |

This suite uses only free, open data and covers the core evaluation dimensions. It can be expanded as the team commits resources.
