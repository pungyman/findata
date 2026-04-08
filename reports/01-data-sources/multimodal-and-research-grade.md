# Multimodal & Research-Grade Datasets

This report covers cross-modal and research-grade financial datasets that bridge multiple data types (text, tables, time series, images) or are assembled specifically for ML research. These datasets are especially relevant if the team pursues multimodal approaches or needs paradigm-bridging evaluation tasks.

---

## Source Summaries

### FNSPID (Financial News and Stock Price Integration Dataset)

| Attribute | Detail |
|---|---|
| **Provider** | Academic research (available on HuggingFace / Kaggle) |
| **Coverage** | 29.7 million stock price records paired with 15.7 million financial news headlines, time-aligned. Covers major US equities |
| **Time resolution** | Daily stock prices with corresponding news headlines from the same trading day |
| **Format** | Structured dataset with separate price and news tables linked by date and ticker. CSV/Parquet |
| **Access** | Free for download |
| **Licensing** | CC BY-NC — academic and non-commercial research only. Commercial use restricted |
| **History depth** | Multiple years of daily data (coverage period varies by ticker) |
| **Labels / targets** | No explicit labels, but the time-aligned price-news structure enables natural supervised setups: next-day return prediction conditioned on news sentiment, or news-driven stock movement classification |
| **Scale** | 29.7M price records + 15.7M headlines — large enough for pre-training experiments, not just evaluation |

**Strengths.** FNSPID's key value is the pre-aligned multimodal structure: price and news data are already linked by date and ticker, eliminating the data-integration burden that typically consumes weeks of preprocessing in financial ML projects. The scale (29M+ price records, 15M+ headlines) is sufficient for model training, not just evaluation. This makes FNSPID a strong candidate for prototyping multimodal financial models that fuse textual sentiment with numeric price features.

**Limitations.** The CC BY-NC license restricts commercial use. News headlines are short (typically <20 words), limiting the depth of textual analysis compared to full articles or filings. The dataset captures headline-level sentiment rather than detailed financial reasoning. Quality and source consistency of the news headlines should be verified before use.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Price + headline-derived features (sentiment scores, word counts) create a rich tabular representation per (stock, date) |
| Time-Series FMs | **High** | 29M+ daily price records are directly usable as TS input; news features can serve as exogenous covariates |
| Financial LLMs/SLMs | **High** | 15M+ headlines provide a domain-specific text corpus; the price alignment enables evaluation of news-driven prediction tasks |

---

### FinMultiTime

| Attribute | Detail |
|---|---|
| **Provider** | Academic research ([HuggingFace](https://huggingface.co/datasets/Wenyan0110/Multimodal-Dataset-Image_Text_Table_TimeSeries-for-Financial-Time-Series-Forecasting)) |
| **Coverage** | 4-modal dataset spanning **5,586 stocks** from both S&P 500 (US) and HS 300 (China) indices. Modalities: (1) news text, (2) fundamental tables, (3) candlestick chart images, (4) OHLCV time series |
| **Time resolution** | Daily |
| **Format** | Available on HuggingFace. Structured with separate modality files per stock, pre-aligned by date |
| **Access** | Free download via HuggingFace |
| **Licensing** | Research use (check HuggingFace dataset card for specific terms) |
| **History depth** | 2009–2025 — 16 years of daily data |
| **Scale** | **112.6 GB** total. 5,586 stocks × 16 years × 4 modalities |
| **Labels / targets** | Stock price forecasting is the primary intended task. Returns and directional labels can be derived from OHLCV |
| **Geographic coverage** | US (S&P 500) + China (HS 300) — provides cross-market generalisation testing |

**Strengths.** FinMultiTime is arguably the most comprehensive open multimodal financial dataset available. Four modalities pre-aligned by date and ticker eliminate the multimodal data fusion challenge. The dual-market coverage (US + China) enables cross-market generalisation experiments. At 112.6 GB, it is large enough for serious model development. The inclusion of candlestick chart images is unique — it enables research on visual pattern recognition in finance, a niche but growing area. Most importantly for this project, FinMultiTime is the strongest candidate for a paradigm-bridging evaluation dataset: the same stocks can be evaluated via tabular features, time-series forecasting, text-driven LLM prediction, or multimodal fusion.

**Limitations.** The 112.6 GB size may be a practical barrier for teams without adequate storage and compute. Quality and consistency of the news text component across two markets (English vs. Chinese) should be validated. The dataset is recent and academic adoption is still emerging, so there are few established baselines to compare against.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | Fundamental tables + derived features from other modalities create rich tabular representations |
| Time-Series FMs | **High** | OHLCV series for 5,586 stocks over 16 years — directly usable for TS forecasting benchmarks |
| Financial LLMs/SLMs | **High** | News text modality enables text-driven prediction and multimodal LLM evaluation |

---

### Google Trends (Financial Topics)

| Attribute | Detail |
|---|---|
| **Provider** | Google |
| **Coverage** | Relative search interest (0–100 scale) for any search term or topic, globally or by country/region. Financial applications: search interest for company names, ticker symbols, financial terms ("recession", "inflation", "stock market crash"), product categories |
| **Time resolution** | Weekly (for queries >5 years), daily (for queries <90 days), hourly (for queries <7 days) |
| **Format** | Web interface with CSV export; unofficial Python API (`pytrends`). No official REST API |
| **Access** | Free. No API key. `pytrends` is rate-limited by Google (informal, ~10–20 queries/minute before throttling) |
| **Licensing** | Google Terms of Service. Data is freely available for research. Redistribution of bulk data is technically against ToS but widely practiced in academic research |
| **History depth** | 2004–present |
| **Labels / targets** | No labels. Search interest is an alternative data signal, not a target |
| **Scale** | One series per query term per geography. Building a useful dataset requires querying many terms systematically |

**Strengths.** Google Trends provides a unique signal: real-time public attention as a proxy for sentiment, fear, and information demand. Academic research has shown that abnormal search interest for company names or financial terms can predict short-term volatility, trading volume, and returns (Da, Engelberg & Gao 2011; Preis, Moat & Stanley 2013). As an alternative data source, it captures a dimension that price and fundamental data cannot — retail investor attention and information-seeking behaviour.

**Limitations.** The data is relative (0–100 normalised scale), not absolute. Comparability across time periods and geographies requires careful normalisation. The unofficial `pytrends` API is fragile and subject to breaking changes. Data granularity coarsens with lookback window (daily becomes weekly beyond 90 days). Building a systematic dataset requires hundreds of queries, each individually rate-limited.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Search interest per stock or topic per week can be a feature in tabular models, but constructing the dataset is labour-intensive |
| Time-Series FMs | Medium | Weekly attention series can be modelled as exogenous covariates or standalone forecasting targets |
| Financial LLMs/SLMs | Medium | Search trends can contextualise text analysis (spike in "recession" searches alongside pessimistic filings), but the connection is indirect |

---

### FinBen Dataset Collection

| Attribute | Detail |
|---|---|
| **Provider** | The FinAI research group ([thefin.ai](https://www.thefin.ai/dataset-benchmark/finben)) |
| **Coverage** | **42 datasets** spanning **24 tasks** across **8 financial domains**: information extraction, textual analysis, question answering, text generation, forecasting, risk management, decision-making, and Spanish-language finance |
| **Format** | Centralised benchmark suite with standardised evaluation protocols. Individual datasets in various formats (JSON, CSV, text), accessible via HuggingFace and the FinBen hub |
| **Access** | Free. Living leaderboard on HuggingFace |
| **Licensing** | Varies by individual dataset — ranges from public domain (for SEC-derived data) to CC BY-NC for academic datasets |
| **Scale** | Aggregate: hundreds of thousands of labelled examples across all 42 datasets |

**Strengths.** FinBen is the most comprehensive financial NLP benchmark suite available. By aggregating 42 datasets under a unified evaluation framework, it enables systematic comparison of LLMs across the full spectrum of financial NLP tasks — from basic sentiment classification to complex numerical reasoning and forecasting. The living leaderboard provides a continuously updated competitive landscape. For the team, FinBen is the default starting point for understanding what financial LLM evaluation looks like today.

**Limitations.** FinBen is primarily a benchmark, not a training dataset. Individual datasets within the collection vary widely in quality, scale, and licensing. The aggregation introduces evaluation complexity (weighting across 24 tasks, handling dataset-specific preprocessing). Some included datasets are small enough that leaderboard results may be noisy.

**Note:** FinBen is discussed in more detail as a benchmark in [02-benchmarks/financial-llm-slm.md](../02-benchmarks/financial-llm-slm.md). It is included here for completeness as a data source.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Some FinBen datasets (credit scoring, fraud detection tasks) involve structured tabular data |
| Time-Series FMs | Medium | Forecasting tasks in FinBen include stock movement prediction, which overlaps with TS forecasting |
| Financial LLMs/SLMs | **High** | FinBen is the primary LLM/SLM evaluation benchmark in finance |

---

### EUROFIDAI (European Financial Data Institute)

| Attribute | Detail |
|---|---|
| **Provider** | EUROFIDAI (European Financial Data Institute), supported by CNRS (French National Centre for Scientific Research) |
| **Coverage** | European financial data including: equity prices, corporate events (M&A, dividends, splits), ESG scores, corporate governance indicators, and historical European market data |
| **Time resolution** | Daily for prices. Event-level for corporate actions. Varies for ESG and governance |
| **Format** | Proprietary database with academic access portal. Data delivered in structured formats (CSV, database exports) |
| **Access** | Academic subscription required. Available to European research institutions through CNRS partnerships. Pricing is institutional (not publicly listed) |
| **Licensing** | Academic use only. Redistribution prohibited. Data may be used in published research results but raw data cannot be shared |
| **History depth** | Varies by dataset — European equity prices from 1990s, corporate events from 2000s |
| **Labels / targets** | Corporate event labels (M&A announcements, dividend changes, governance changes) are natural supervised targets. ESG scores provide labeled features |
| **Geographic coverage** | European markets — fills the geographic gap left by US-centric free sources |

**Strengths.** EUROFIDAI fills two gaps simultaneously: (1) European market coverage that complements the US-centric free data ecosystem, and (2) ESG and corporate event data that is scarce in open sources. The corporate event labels (M&A, dividend changes) are rare — most free sources provide raw prices without any labelled events. Academic quality is high (curated by a CNRS-backed institute).

**Limitations.** Academic subscription only — not available to industry teams without institutional affiliation. European-only scope. The proprietary access model creates reproducibility barriers. ESG data methodology and coverage periods should be verified against commercial providers (MSCI, Sustainalytics).

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | ESG scores, corporate governance indicators, and event labels create rich cross-sectional tabular features with natural supervised targets |
| Time-Series FMs | Medium | European equity price series complement US data. Daily resolution is usable for TS forecasting |
| Financial LLMs/SLMs | Low | Primarily structured data, not text |

---

## Comparative Summary

| Source | Cost | Modalities | Scale | Geographic scope | Paradigm bridge? |
|---|---|---|---|---|---|
| FNSPID | Free | Price + news | 29M prices, 15M headlines | US | Yes (TS + LLM) |
| FinMultiTime | Free | Price + news + tables + images | 112.6 GB, 5,586 stocks | US + China | **Yes (all three)** |
| Google Trends | Free | Search interest | One series per query | Global | Partial (auxiliary) |
| FinBen collection | Free | Mixed (42 datasets) | Varies | Primarily US/English | Yes (LLM + partial tabular/TS) |
| EUROFIDAI | Academic sub | Price + events + ESG | European markets | Europe | Partial (tabular + TS) |

## Key Takeaways

1. **FinMultiTime is the top candidate for cross-paradigm evaluation.** Its four modalities, dual-market coverage, and 16-year history make it the strongest open dataset for testing whether a tabular, time-series, or LLM approach (or their combination) best predicts stock returns. The team should prioritise downloading and inspecting this dataset.

2. **FNSPID provides the lowest-friction entry point for multimodal prototyping.** Pre-aligned price-news pairs at scale enable quick experiments without data engineering.

3. **Google Trends is a valuable auxiliary signal but not a standalone dataset.** It requires significant curation to transform into a systematic feature set and is best used as a supplement to price or text data.

4. **FinBen bridges data and benchmarks.** Its 42-dataset collection serves double duty as both a data source and an evaluation framework, especially for teams focused on the LLM/SLM paradigm.

5. **EUROFIDAI is the only source with event labels and ESG data**, but its academic-only access model limits broad adoption. Teams with institutional access should leverage it for European tabular model evaluation.

6. **These datasets collectively make the strongest case for multimodal approaches.** If the team's evaluation reveals that single-paradigm models underperform on these datasets compared to multimodal baselines, it would shift the strategic direction toward fusion architectures.
