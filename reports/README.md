# Financial ML Dataset Survey

## Research Context

This survey supports early-stage exploration of three potentially disjoint model paradigms for financial applications:

| Paradigm | Core idea | Why it matters |
|---|---|---|
| **Tabular Foundation Models** | Pre-trained models that generalise across arbitrary tabular schemas (TabPFN, TabICL, RealTabFormer) | Much of finance lives in structured tables — credit scoring, loan tapes, claims, factor features |
| **Time-Series Foundation Models** | Pre-trained forecasters that zero-shot transfer to unseen series (Chronos, Moirai, TimesFM) | Price, macro, and risk series are the backbone of quantitative finance |
| **Financial LLMs / SLMs** | Large or small language models fine-tuned on financial text (BloombergGPT, FinGPT, FinMA) | Filings, earnings calls, and news carry alpha that numeric models ignore |

The goal is **not** to pick a winner today. It is to map the dataset and benchmark landscape so the team can make an informed decision about where to invest next. Crypto and blockchain data are excluded from scope.

This repository of reports expands on the [Unidata blog post on financial ML datasets](https://unidata.pro/blog/best-financial-ml-datasets/) and branches into benchmark ecosystems, model-specific evaluation, and gap analysis.

---

## Table of Contents

### Data Sources (`01-data-sources/`)

Reports on available datasets, organised by modality.

| # | Report | One-line summary |
|---|--------|-----------------|
| 1 | [Market & Price Data](01-data-sources/market-and-price-data.md) | OHLCV equities, forex, and derivatives via Yahoo Finance, Alpha Vantage, Quandl, and GFD |
| 2 | [Macroeconomic & Institutional](01-data-sources/macroeconomic-institutional.md) | FRED (including ML-ready FRED-MD/QD), World Bank, IMF, OECD, and EU Open Data |
| 3 | [Financial Text & Filings](01-data-sources/financial-text-and-filings.md) | SEC EDGAR corpus, SEC-QA, FinNLI, FinancialPhraseBank, earnings transcripts |
| 4 | [Multimodal & Research-Grade](01-data-sources/multimodal-and-research-grade.md) | FNSPID, FinMultiTime, Google Trends, FinBen collection, EUROFIDAI |

### Benchmarks (`02-benchmarks/`)

Reports on evaluation benchmarks, organised by model paradigm.

| # | Report | One-line summary |
|---|--------|-----------------|
| 5 | [Tabular Foundation Models](02-benchmarks/tabular-foundation-models.md) | TabArena, TALENT, finance-specific gaps in tabular evaluation |
| 6 | [Time-Series Foundation Models](02-benchmarks/time-series-foundation-models.md) | FEV Bench, GIFT-Eval, TSFM.ai, Kronos, timesfm_fin |
| 7 | [Financial LLMs / SLMs](02-benchmarks/financial-llm-slm.md) | FinBen (42 datasets / 24 tasks), MultiFinBen, BloombergGPT reference points |

### Cross-Cutting

| # | Report | One-line summary |
|---|--------|-----------------|
| 8 | [Evaluation Requirements](03-evaluation-requirements.md) | Criteria matrix and paradigm-specific data requirements |
| 9 | [Gaps & Next Steps](04-gaps-and-next-steps.md) | Identified blind spots and prioritised action items |

### Reading Order

- **Requirements-first** (recommended for decision-makers): start with `03-evaluation-requirements.md` to understand what we need, then skim data source reports against those criteria.
- **Data-first** (recommended for researchers): read through `01-data-sources/*` to build intuition, then check benchmarks and requirements.

---

## Dataset × Paradigm Relevance Matrix

Quick reference showing which datasets and benchmarks are relevant to each model paradigm. Relevance is rated **High (H)**, **Medium (M)**, or **Low (L)**.

### Data Sources

| Dataset / Source | Tabular FMs | Time-Series FMs | Financial LLMs/SLMs |
|---|:---:|:---:|:---:|
| Yahoo Finance / yfinance | **H** | **H** | L |
| Alpha Vantage | M | **H** | L |
| Quandl / Nasdaq Data Link | **H** | **H** | L |
| Global Financial Data (GFD) | M | **H** | L |
| Bloomberg Terminal | **H** | **H** | **H** |
| FRED | M | **H** | L |
| FRED-MD / FRED-QD | **H** | **H** | L |
| World Bank Open Data | **H** | M | L |
| IMF IFS | M | M | L |
| OECD Public Finance | **H** | M | L |
| EU Open Data Portal | M | M | L |
| SEC EDGAR filings | L | L | **H** |
| SEC-QA | L | L | **H** |
| FinNLI | L | L | **H** |
| FinancialPhraseBank | L | L | **H** |
| Earnings call transcripts | L | L | **H** |
| FNSPID (price + news) | M | **H** | **H** |
| FinMultiTime (4-modal) | **H** | **H** | **H** |
| Google Trends | M | M | M |
| FinBen collection (42 datasets) | M | M | **H** |
| EUROFIDAI | **H** | M | L |

### Benchmarks

| Benchmark | Tabular FMs | Time-Series FMs | Financial LLMs/SLMs |
|---|:---:|:---:|:---:|
| TabArena | **H** | L | L |
| TALENT | **H** | L | L |
| FEV Bench | L | **H** | L |
| GIFT-Eval | L | **H** | L |
| TSFM.ai leaderboards | L | **H** | L |
| Kronos evaluation | L | **H** | L |
| FinBen benchmark | M | M | **H** |
| MultiFinBen | M | M | **H** |
| Open Financial LLM Leaderboard | L | L | **H** |

---

## Key External References

- [FinBen benchmark and leaderboard](https://www.thefin.ai/dataset-benchmark/finben)
- [TSFM.ai benchmarks hub](https://tsfm.ai/benchmarks) (FEV Bench, GIFT-Eval, BOOM)
- [TabArena living benchmark](https://tabarena.ai)
- [FinMultiTime on HuggingFace](https://huggingface.co/datasets/Wenyan0110/Multimodal-Dataset-Image_Text_Table_TimeSeries-for-Financial-Time-Series-Forecasting)
- [FRED-MD / FRED-QD for ML](https://research.stlouisfed.org/econ/mccracken/fred-databases)
- [Kronos: financial TSFM](https://arxiv.org/html/2508.02739v1)
- [timesfm_fin: fine-tuned TimesFM](https://github.com/pfnet-research/timesfm_fin)
- [Survey: Language Modeling for Finance (2025)](https://arxiv.org/abs/2504.07274)
- [Open Financial LLM Leaderboard](https://huggingface.co/spaces/finosfoundation/Open-Financial-LLM-Leaderboard)
