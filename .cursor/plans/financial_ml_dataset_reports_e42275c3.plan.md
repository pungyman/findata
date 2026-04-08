---
name: Financial ML Dataset Reports
overview: Generate a structured set of markdown reports surveying financial ML datasets, evaluation benchmarks across three model paradigms (tabular FMs, time-series FMs, financial LLMs/SLMs), and a requirements framework -- all positioned as early-stage groundwork for a research team that hasn't yet committed to a specific architecture.
todos:
  - id: readme
    content: Write README.md -- master index with research context, ToC, and dataset-paradigm relevance matrix
    status: completed
  - id: market-data
    content: Write 01-data-sources/market-and-price-data.md -- Yahoo, Alpha Vantage, Quandl, GFD with paradigm relevance
    status: completed
  - id: macro-data
    content: Write 01-data-sources/macroeconomic-institutional.md -- FRED (incl FRED-MD/QD), World Bank, IMF, OECD, EU
    status: completed
  - id: text-data
    content: Write 01-data-sources/financial-text-and-filings.md -- SEC/EDGAR, SEC-QA, FinNLI, FPB, earnings calls
    status: completed
  - id: multimodal-data
    content: Write 01-data-sources/multimodal-and-research-grade.md -- FNSPID, FinMultiTime, Google Trends, EUROFIDAI
    status: completed
  - id: tabular-bench
    content: Write 02-benchmarks/tabular-foundation-models.md -- TabArena, TALENT, finance gap analysis
    status: completed
  - id: ts-bench
    content: Write 02-benchmarks/time-series-foundation-models.md -- FEV Bench, GIFT-Eval, Kronos, timesfm_fin
    status: completed
  - id: llm-bench
    content: Write 02-benchmarks/financial-llm-slm.md -- FinBen, MultiFinBen, FinQA, BloombergGPT context
    status: completed
  - id: requirements
    content: Write 03-evaluation-requirements.md -- cross-cutting criteria matrix, paradigm-specific requirements
    status: completed
  - id: gaps
    content: Write 04-gaps-and-next-steps.md -- gap analysis, prioritized action items
    status: completed
isProject: false
---

# Financial ML Dataset Survey Reports

## Context and Framing

The team is exploring three potentially disjoint frontiers -- tabular foundation models, time-series foundation models, and financial LLMs/SLMs -- and needs early-stage groundwork on available datasets and evaluation benchmarks before committing to a direction. The deliverables below expand on the [Unidata blog post](https://unidata.pro/blog/best-financial-ml-datasets/) (excluding crypto/blockchain) and branch into benchmark ecosystems, model-specific evaluation, and gap analysis.

## Directory Structure

```
reports/
  README.md
  01-data-sources/
    market-and-price-data.md
    macroeconomic-institutional.md
    financial-text-and-filings.md
    multimodal-and-research-grade.md
  02-benchmarks/
    tabular-foundation-models.md
    time-series-foundation-models.md
    financial-llm-slm.md
  03-evaluation-requirements.md
  04-gaps-and-next-steps.md
```

**10 files total.** Rationale: data sources are split by modality (4 reports), benchmarks are split by model paradigm (3 reports), plus a requirements framework, a gap analysis, and a README index. This keeps each file focused and navigable while covering all three paradigms.

---

## Report Descriptions

### `README.md` -- Master Index

- Research context: team positioning, the three paradigms under consideration, stage of exploration
- Table of contents linking all sub-reports with one-line descriptions
- Reading order guidance (requirements first vs. data-first depending on reader)
- Quick-reference matrix: dataset x paradigm relevance (which datasets matter for which model type)

### `01-data-sources/market-and-price-data.md`

Expands the Unidata article's "Market & Stock Data" section. Covers:

- **Yahoo Finance / yfinance** -- OHLCV for S&P 500+, free, commercial-OK, no labels
- **Alpha Vantage** -- intraday + daily, forex, fundamentals, free tier rate-limited
- **Quandl (Nasdaq Data Link)** -- WIKI/FRED collections free, premium for options/ETFs
- **Global Financial Data (GFD)** -- historical back to 1800s, paid/academic
- For each: format, access, licensing, time resolution, relevance to each of the 3 paradigms
- Key gap: none of these provide labeled targets (buy/sell, regime labels) -- implications for supervised evaluation

### `01-data-sources/macroeconomic-institutional.md`

Expands the Unidata article's "Macroeconomic & Banking" section. Covers:

- **FRED** -- 800K+ series, API, commercial-OK. Highlight **FRED-MD** (monthly, 128+ series since 1959) and **FRED-QD** (quarterly) as ML-ready curated subsets
- **World Bank Open Data** -- GDP, trade, education, CC BY 4.0
- **IMF International Financial Statistics** -- reserves, fiscal balance, CC BY
- **OECD Public Finance** -- tax, spending, sovereign risk
- **EU Open Data Portal** -- subnational European data
- For each: format, granularity, coverage, relevance to time-series FMs and tabular FMs specifically
- FRED-MD/FRED-QD are particularly important as they are already structured for factor models and ML research

### `01-data-sources/financial-text-and-filings.md`

Goes beyond the Unidata article into the NLP/text data ecosystem. Covers:

- **SEC filings corpus** (EDGAR) -- 10-K, 10-Q, 8-K filings, free, public domain
- **SEC-QA** -- systematic evaluation corpus for financial QA (ACL 2025)
- **FinNLI** -- 21K premise-hypothesis pairs from SEC filings, annual reports, earnings calls (expert-annotated)
- **FinancialPhraseBank (FPB)** -- sentiment-labeled financial news sentences
- **Earnings call transcripts** -- various sources (Seeking Alpha, S&P Capital IQ)
- **Bloomberg training data context** -- BloombergGPT used 363B tokens of financial text (news, filings, press); relevant as a reference point for scale
- Relevance: primarily LLM/SLM paradigm, but text features can feed tabular models too

### `01-data-sources/multimodal-and-research-grade.md`

Covers cross-modal and research datasets. Expands from the Unidata article:

- **FNSPID** -- 29M stock records + 15M news headlines, time-aligned, CC BY-NC academic only
- **FinMultiTime** -- 4-modal (news, tables, candlestick charts, OHLCV), 5,586 stocks (S&P 500 + HS 300), 112.6 GB, 2009-2025, available on HuggingFace
- **Google Trends (financial topics)** -- search interest as sentiment proxy, free
- **FinBen dataset collection** -- 42 datasets across 24 tasks (overlaps with benchmark report, cross-referenced)
- **EUROFIDAI** -- ESG & corporate events, European, academic subscription
- Relevance: these datasets bridge paradigms and are especially relevant if the team pursues multimodal approaches

### `02-benchmarks/tabular-foundation-models.md`

Surveys the evaluation ecosystem for tabular FMs:

- **TabArena** -- continuously maintained living benchmark, public leaderboard at tabarena.ai, manually curated datasets, tests default/tuned/ensembled settings
- **TALENT** -- 200+ classification datasets
- **Current SOTA**: TabICLv2 > RealTabPFN-2.5 on TabArena/TALENT; TabPFN excels < 10K samples; gradient boosting still competitive at scale
- **Finance-specific gap**: no dedicated financial tabular benchmark exists; credit scoring, fraud detection, and risk datasets are scattered and often proprietary
- Open question: team would need to curate finance-specific tabular evaluation tasks (credit default, loan performance, claim prediction)

### `02-benchmarks/time-series-foundation-models.md`

Surveys TS foundation model evaluation:

- **FEV Bench** -- zero-shot generalization, Chronos-2 leads (35.50 skill score)
- **GIFT-Eval** -- probabilistic forecasting robustness, Moirai 1.1 strong
- **BOOM** -- infrastructure telemetry (less finance-relevant)
- **TSFM.ai** -- central hub with living leaderboards for all three benchmarks
- **Finance-specific**: **Kronos** -- purpose-built financial TSFM, 93% improvement in price series forecasting RankIC over general TSFMs; **timesfm_fin** -- fine-tuned TimesFM achieving Sharpe 1.68 on S&P 500
- Key insight: general TSFMs underperform finance-specialized ones significantly, suggesting domain-specific evaluation is critical

### `02-benchmarks/financial-llm-slm.md`

Surveys the LLM/SLM evaluation landscape for finance:

- **FinBen** -- 42 datasets, 24 tasks, 8 domains (IE, textual analysis, QA, generation, forecasting, risk, decision-making, Spanish). Living leaderboard on HuggingFace. GPT-4 and Llama 3.1 lead, but smaller models (7B) surprisingly outperform larger ones on forecasting
- **MultiFinBen** (2025) -- adds multilingual + multimodal + difficulty-aware evaluation
- **Key individual benchmarks within FinBen**: FinQA (numerical reasoning over financial tables), TATQA (tabular + textual QA), ECTSum/EDTSum (earnings call summarization)
- **BloombergGPT reference**: 50B params, 700B tokens training, validated on sentiment, NER, news classification, QA
- **2025 survey finding** (arXiv 2504.07274): 374 NLP papers reviewed, identifies gaps in forecasting scope, multilingual coverage, and financial-metric-based evaluation
- Surprising result: instruction tuning shows limited benefit for complex financial QA tasks

### `03-evaluation-requirements.md`

Cross-cutting requirements framework for dataset selection, applicable regardless of which paradigm the team pursues:

- **Criteria matrix** (adapted from Unidata article + expanded):
  - Time resolution (tick/minute/daily/quarterly)
  - Completeness and missingness patterns
  - Domain fit (market vs. macro vs. corporate vs. consumer)
  - Label availability and quality (supervised targets)
  - Licensing (commercial vs. academic-only vs. proprietary)
  - Update frequency and staleness risk
  - Scale (rows, features, time span)
  - Multimodal compatibility (can text/numeric/image be aligned?)
- **Paradigm-specific requirements**:
  - Tabular FMs: need diverse feature types (numeric + categorical), moderate scale (1K-100K rows optimal for current FMs), labeled classification/regression targets
  - Time-series FMs: need consistent temporal resolution, minimal gaps, multiple related series for cross-learning, ideally both point and distributional targets
  - LLMs/SLMs: need text corpora (filings, news, transcripts), QA pairs, sentiment labels, numerical reasoning examples
- **Evaluation philosophy**: recommend holding out a "meta-evaluation" set that works across paradigms (e.g., stock movement prediction can be framed as tabular classification, time-series forecasting, or text-driven LLM reasoning)

### `04-gaps-and-next-steps.md`

Actionable gap analysis and recommended next steps:

- **Identified gaps**:
  - No unified cross-paradigm financial benchmark exists
  - Tabular FM benchmarks lack finance-specific datasets
  - Most financial text datasets are English-only (MultiFinBen starting to address this)
  - Proprietary data (Bloomberg terminal, Refinitiv) is a major blind spot
  - ESG and alternative data coverage is thin in open benchmarks
- **Recommended next steps** (prioritized):
  1. Register for free-tier APIs (FRED, Alpha Vantage, yfinance) and pull sample data to assess quality firsthand
  2. Download and inspect FinBen task list to understand which of the 42 datasets overlap with team interests
  3. Review FinMultiTime on HuggingFace as a candidate cross-paradigm dataset
  4. Identify 2-3 "paradigm-bridging" evaluation tasks (e.g., stock movement prediction) that can be formulated for all three model types
  5. Survey proprietary data access options through institutional subscriptions
  6. Draft a short-list of candidate evaluation datasets per paradigm for team review

---

## Key Sources Beyond the Unidata Article

- [FinBen benchmark and leaderboard](https://www.thefin.ai/dataset-benchmark/finben)
- [TSFM.ai benchmarks hub](https://tsfm.ai/benchmarks) (FEV Bench, GIFT-Eval, BOOM)
- [TabArena living benchmark](https://tabarena.ai)
- [FinMultiTime on HuggingFace](https://huggingface.co/datasets/Wenyan0110/Multimodal-Dataset-Image_Text_Table_TimeSeries-for-Financial-Time-Series-Forecasting)
- [FRED-MD/FRED-QD for ML](https://research.stlouisfed.org/econ/mccracken/fred-databases)
- [Kronos: financial TSFM](https://arxiv.org/html/2508.02739v1)
- [timesfm_fin: fine-tuned TimesFM](https://github.com/pfnet-research/timesfm_fin)
- [Survey: Language Modeling for Finance (2025)](https://arxiv.org/abs/2504.07274)
- [Open Financial LLM Leaderboard](https://huggingface.co/spaces/finosfoundation/Open-Financial-LLM-Leaderboard)

