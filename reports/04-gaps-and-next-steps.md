# Gaps & Next Steps

This report synthesises the identified blind spots from the data source and benchmark surveys and translates them into prioritised action items for the team.

---

## Identified Gaps

### 1. No Unified Cross-Paradigm Financial Benchmark Exists

**The gap.** There is no established benchmark that evaluates tabular FMs, time-series FMs, and financial LLMs on the same financial tasks using the same data. Each paradigm has its own evaluation ecosystem (TabArena, TSFM.ai, FinBen), but these operate in isolation. This makes it impossible to answer the team's central question — *which paradigm should we invest in?* — without building custom cross-paradigm evaluation infrastructure.

**Impact.** High. This is the single most important gap for the team's decision-making. Without cross-paradigm comparison, the team risks committing to a paradigm based on intra-paradigm leaderboard performance rather than head-to-head evaluation on the same financial task.

**Mitigation.** The "meta-evaluation" concept in [03-evaluation-requirements.md](03-evaluation-requirements.md) outlines how stock movement prediction and other tasks can be formulated across all three paradigms using free data (yfinance, FNSPID, FinMultiTime).

---

### 2. Tabular FM Benchmarks Lack Finance-Specific Datasets

**The gap.** TabArena and TALENT contain zero finance-specific datasets. The tasks closest to finance (income prediction, insurance) are generic tabular tasks that do not capture the domain-specific challenges of financial data: temporal dependencies, regime shifts, extreme class imbalance (defaults, fraud), and regulatory constraints on feature usage.

**Impact.** High for teams considering the tabular FM paradigm. Current tabular FM leaderboard results do not predict financial performance. A model that tops TabArena may struggle on credit scoring because TabArena doesn't test the specific challenges of financial tabular data.

**Mitigation.** The team would need to curate finance-specific tabular evaluation tasks. Candidate datasets and task designs are outlined in [02-benchmarks/tabular-foundation-models.md](02-benchmarks/tabular-foundation-models.md): credit default (LendingClub), stock movement (yfinance-derived), macro regime (FRED-MD), and corporate rating prediction.

---

### 3. Most Financial Text Datasets Are English-Only

**The gap.** The financial NLP ecosystem is overwhelmingly English-centric. FPB, FinQA, TATQA, SEC-QA, ECTSum — all English. FinBen includes one Spanish-language task but nothing in Chinese, Japanese, German, French, or other financially important languages. The 2025 survey (arXiv 2504.07274) confirms this: of 374 reviewed papers, the vast majority use English-only data.

**Impact.** Medium. If the team analyses only US/UK markets, this gap is manageable. If the team operates in or analyses non-English markets (European, Asian), the lack of evaluation infrastructure is a significant limitation.

**Mitigation.** MultiFinBen (2025) is beginning to address this with multilingual evaluation. The team should monitor its development and consider contributing non-English financial datasets if relevant to their scope.

---

### 4. Proprietary Data Is a Major Blind Spot

**The gap.** Bloomberg Terminal, Refinitiv/LSEG, S&P Capital IQ, and ICE Data Services collectively contain the richest, cleanest, and most comprehensive financial data available. None of it can be used in open benchmarks or shared in published research. This means:
- Open benchmarks systematically underrepresent the data quality available in production financial ML
- Models evaluated on open data (yfinance, FRED) may perform differently on institutional-quality data
- Any evaluation built on proprietary data is irreproducible by teams without the same subscriptions

**Impact.** Medium-High. The gap is structural — it cannot be fully closed without institutional data access. However, the team can partially mitigate it by using open data for reproducible benchmarks and proprietary data (if accessible) for internal validation.

**Mitigation.** If the team has access to Bloomberg via an institutional subscription or university lab:
- Use Bloomberg data to construct labelled evaluation sets (rating changes, estimate revisions) that free sources cannot provide
- Build open benchmarks on free data (EDGAR, yfinance, FRED) so that results are reproducible
- Use Bloomberg as a quality reference to validate whether open-data findings generalise

---

### 5. ESG and Alternative Data Coverage Is Thin in Open Benchmarks

**The gap.** ESG scores, climate risk indicators, supply chain data, satellite imagery, and social media signals are increasingly important in institutional finance, but virtually no open benchmarks include them. EUROFIDAI provides some ESG data but is academic-only and European-focused. FinBen does not include ESG tasks. TabArena and TSFM.ai have no ESG-related datasets.

**Impact.** Low-Medium at present, but rising. ESG integration is a regulatory mandate in the EU (SFDR, CSRD) and a growing institutional priority globally. If the team's work involves sustainable finance, this gap becomes high-priority.

**Mitigation.** Limited options in the open ecosystem. EUROFIDAI (academic access) is the best available source. Commercial ESG data providers (MSCI, Sustainalytics, Bloomberg ESG) are proprietary. The team could explore the LSEG ESG dataset on Kaggle as a starting point.

---

### 6. Label Scarcity Across All Data Sources

**The gap.** A recurring theme across all four data source reports: most financial datasets do not provide labelled targets. Market data (yfinance, Alpha Vantage, GFD) provides raw prices. Macro data (FRED, World Bank, IMF) provides raw indicators. Even EDGAR provides raw text without labels. Researchers must derive labels — directional returns, recession indicators, sentiment scores, default flags — from raw data, creating a reproducibility problem (different labelling schemes yield different leaderboard outcomes).

**Impact.** High. This affects all three paradigms equally. The team cannot run supervised evaluation without investing in label engineering. The lack of standardised labels also makes cross-study comparison difficult.

**Mitigation.** Adopt standardised label definitions and publish them alongside any benchmark. For specific tasks:
- Stock movement: next-day directional return (binary, derived from close prices)
- Recession: NBER recession dates (public, well-defined)
- Credit default: LendingClub loan status flags
- Sentiment: FPB and FiQA-SA provide existing labels for LLM evaluation

---

### 7. General TS FMs Significantly Underperform on Financial Data

**The gap.** Kronos's 93% RankIC improvement over general TS FMs (Chronos-2, Moirai, TimesFM) on financial series demonstrates that TSFM.ai leaderboard rankings do not transfer to finance. Models that dominate general-purpose benchmarks may significantly underperform on financial time series due to non-stationarity, volatility clustering, fat tails, and low signal-to-noise ratios.

**Impact.** High for teams considering the TS FM paradigm. Naively selecting a TS FM based on general benchmark performance would be misleading.

**Mitigation.** Finance-specific TS evaluation is mandatory. Use Kronos and timesfm_fin as financial baselines. Evaluate on financial metrics (RankIC, Sharpe) rather than only standard TS metrics (MASE, CRPS).

---

## Recommended Next Steps (Prioritised)

### Priority 1 — Immediate (Week 1–2)

| # | Action | Effort | Outcome |
|---|---|---|---|
| **1.1** | Register for free-tier APIs: FRED (API key), Alpha Vantage (API key), install `yfinance` | 1 hour | Data access established |
| **1.2** | Pull sample data from each to assess quality firsthand: 5 years of S&P 500 daily OHLCV (yfinance), FRED-MD monthly panel, Alpha Vantage intraday for 5 tickers | 2–4 hours | Hands-on familiarity with data quality, gaps, and format |
| **1.3** | Download and inspect the FinBen task list on HuggingFace. Map the 42 datasets to the team's interests. Identify which of the 24 tasks align with the team's use cases | 2–3 hours | Prioritised subset of FinBen tasks for evaluation |

### Priority 2 — Near-Term (Week 2–4)

| # | Action | Effort | Outcome |
|---|---|---|---|
| **2.1** | Download and inspect FinMultiTime on HuggingFace. Validate the four modalities (news, tables, candlestick images, OHLCV) for quality and completeness. Assess storage/compute requirements (112.6 GB) | 4–8 hours | Decision on whether FinMultiTime is viable as a cross-paradigm evaluation dataset |
| **2.2** | Identify 2–3 "paradigm-bridging" evaluation tasks (per [03-evaluation-requirements.md](03-evaluation-requirements.md)). Stock movement prediction is the strongest candidate. Design the tabular, TS, and LLM formulations for the same task | 1–2 days | Cross-paradigm evaluation design |
| **2.3** | Implement the minimum viable evaluation suite from [03-evaluation-requirements.md](03-evaluation-requirements.md): FRED-MD recession classification (tabular), yfinance price forecasting (TS), FPB sentiment + FinQA (LLM), and FNSPID stock movement (cross-paradigm) | 3–5 days | Working baseline evaluation infrastructure |

### Priority 3 — Medium-Term (Month 2)

| # | Action | Effort | Outcome |
|---|---|---|---|
| **3.1** | Survey proprietary data access options. Check institutional subscriptions (Bloomberg terminal at university/employer, Refinitiv, Capital IQ). Assess cost-benefit of paid datasets (GFD, Quandl premium) | 1 week | Clarity on data access budget and constraints |
| **3.2** | Draft a short-list of candidate evaluation datasets per paradigm for team review. Include at least 3 datasets per paradigm with pros/cons assessment | 2–3 days | Actionable dataset recommendations for team decision |
| **3.3** | Run initial cross-paradigm comparison: evaluate one tabular FM (TabPFN or XGBoost), one TS FM (Chronos-2 or TimesFM), and one LLM (GPT-4 or open 7B model) on the paradigm-bridging task | 1–2 weeks | First empirical evidence to inform paradigm selection |

### Priority 4 — Longer-Term (Month 3+)

| # | Action | Effort | Outcome |
|---|---|---|---|
| **4.1** | If tabular FM paradigm is selected: curate finance-specific tabular benchmark (credit, fraud, macro regime tasks). Publish as open benchmark | Ongoing | Contribution to community + internal evaluation infrastructure |
| **4.2** | If TS FM paradigm is selected: evaluate Kronos and timesfm_fin against general TS FMs on team's specific financial series. Consider fine-tuning a general TS FM | Ongoing | Domain-adapted TS forecasting model |
| **4.3** | If LLM/SLM paradigm is selected: evaluate 7B open-source models (FinMA, FinGPT) against GPT-4 on FinBen tasks relevant to team's use case. Consider domain fine-tuning | Ongoing | Right-sized financial LLM selection |
| **4.4** | Explore multimodal approaches if cross-paradigm evaluation shows complementary strengths across paradigms | Ongoing | Potential for fusion architecture that combines the best of each paradigm |

---

## Decision Framework

The recommended decision process for paradigm selection:

```
1. Complete Priority 1 & 2 actions
        ↓
2. Run cross-paradigm comparison (Priority 3.3)
        ↓
3. Evaluate results across three dimensions:
   a. Predictive performance (which paradigm best predicts financial outcomes?)
   b. Practical feasibility (data availability, compute cost, latency requirements)
   c. Team capability (existing expertise in tabular ML vs. TS modelling vs. NLP)
        ↓
4. Select primary paradigm + decide whether to pursue multimodal fusion
        ↓
5. Commit resources to paradigm-specific evaluation and model development
```

The key insight from this survey is that the paradigm decision should be empirically grounded — not based on general benchmark leaderboards, which do not predict financial performance — and it should be informed by the cross-paradigm evaluation tasks designed in the requirements framework.
