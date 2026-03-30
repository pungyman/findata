# Market & Price Data

This report surveys the major open and semi-open sources of market and price data relevant to financial ML research. It expands on the "Market & Stock Data" section of the [Unidata blog post](https://unidata.pro/blog/best-financial-ml-datasets/) with additional detail on format, licensing, and paradigm relevance.

---

## Source Summaries

### Yahoo Finance / yfinance

| Attribute | Detail |
|---|---|
| **Coverage** | Global equities (S&P 500+), ETFs, mutual funds, indices, forex, some commodities |
| **Time resolution** | Daily OHLCV (adjusted close); some intraday (1m–1h) with rolling 30-day history |
| **Format** | REST-like endpoints; Python `yfinance` library returns Pandas DataFrames directly |
| **Access** | Free, no API key required |
| **Licensing** | Data sourced from Yahoo; acceptable for research and personal use. Commercial redistribution is grey-area — Yahoo's ToS restrict scraping, though `yfinance` is widely used in production research |
| **History depth** | Varies by ticker; major US equities back to ~1970 for daily data |
| **Labels / targets** | None provided. Raw OHLCV only; users must derive labels (returns, regime tags, buy/sell signals) |
| **Update frequency** | Near-real-time during market hours (15–20 min delay) |

**Strengths.** Zero-friction access; broad ticker universe; sufficient for prototyping and backtesting on daily horizons. The `yfinance` Python wrapper is the de facto standard for quick data pulls in ML notebooks.

**Limitations.** Intraday data has a very short lookback window (≤30 days). No fundamentals beyond summary statistics. Adjusted-close methodology can introduce subtle survivorship-bias artifacts during splits and delistings. No formal SLA — Yahoo can (and has) changed endpoints without notice.

**Derived technical-indicator datasets.** Although yfinance delivers raw OHLCV, the data is sufficient to programmatically derive a wide library of technical indicators — RSI, MACD, Bollinger Bands, ATR, OBV, Stochastic Oscillator, ADX, and dozens more (via libraries such as `ta`, `ta-lib`, or `pandas-ta`). This turns a narrow 6-column price record into a dense feature vector per stock per day. Combined with a derived target — next-day return (regression) or directional move (classification) — the result is a ready-made tabular dataset where each row is a (stock, date) observation and each column is a technical feature. This construction is directly suited to tabular FM evaluation: schema is consistent across tickers, the feature count is moderate (50–200 indicators), and the sample size scales with (number of stocks × trading days). The practical implication is that yfinance, despite providing no labels or features out of the box, can anchor a low-cost tabular FM benchmark pipeline with modest engineering effort.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | Raw OHLCV → derived technical indicators creates a dense (stock, date) feature vector suitable for tabular classification/regression. See note above on derived datasets |
| Time-Series FMs | **High** | Core use case — daily price series are the primary input for TS forecasting models |
| Financial LLMs/SLMs | Low | No textual content; only useful if numeric features are serialised into prompts |

---

### Alpha Vantage

| Attribute | Detail |
|---|---|
| **Coverage** | US and international equities, forex (150+ pairs), crypto, commodities, economic indicators, company fundamentals (income statement, balance sheet, cash flow) |
| **Time resolution** | Intraday (1min, 5min, 15min, 30min, 60min), daily, weekly, monthly |
| **Format** | REST API returning JSON or CSV |
| **Access** | Free tier: 25 requests/day. Premium plans from $49.99/mo (75 req/min) to $249.99/mo (1200 req/min) |
| **Licensing** | Free tier for personal and educational use. Premium tier permits commercial use |
| **History depth** | Intraday: up to 2 years of full history (premium). Daily: 20+ years for major tickers |
| **Labels / targets** | None. Some fundamental data (EPS, PE) can serve as derived features but not as ready-made ML labels |
| **Update frequency** | Real-time with API call (rate-limited) |

**Strengths.** Richer than Yahoo Finance in two dimensions: (1) true intraday data with multi-year lookback on premium plans, and (2) fundamental data endpoints (income statements, balance sheets, earnings) that Yahoo doesn't expose cleanly through `yfinance`. The economic indicator endpoints overlap partially with FRED.

**Limitations.** Free tier is severely rate-limited (25 calls/day makes bulk downloads infeasible). Data quality on less liquid international tickers can be inconsistent. Fundamental data coverage is US-centric.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Fundamental data (EPS, PE, margins) creates richer tabular feature sets than pure OHLCV |
| Time-Series FMs | **High** | Intraday granularity enables sub-daily forecasting research that Yahoo Finance cannot support |
| Financial LLMs/SLMs | Low | No text data. Numeric fundamentals could be serialised but this is not a natural use case |

---

### Quandl (Nasdaq Data Link)

| Attribute | Detail |
|---|---|
| **Coverage** | Aggregator of 300+ data publishers. Key free collections: **WIKI** (US equities, discontinued but archived), **FRED** (mirror), **FSE** (Frankfurt), **LSE** (London). Premium: options, futures, ETF flows, short interest, institutional holdings |
| **Time resolution** | Varies by dataset — daily is most common; some macro datasets are monthly/quarterly |
| **Format** | REST API (JSON, CSV, XML); Python/R/Excel libraries; bulk download for WIKI |
| **Access** | Free datasets: free API key, 300 calls/10s, 50K calls/day. Premium datasets: paid subscriptions (pricing varies by publisher, typically $50–$500/mo per dataset) |
| **Licensing** | Free datasets generally permit research use. Premium dataset licenses vary by publisher — some are academic-only, others permit commercial use. Always check individual dataset terms |
| **History depth** | WIKI: 1970–2018 (frozen). FRED mirror: matches FRED. Premium datasets vary (some back to 1990s) |
| **Labels / targets** | None in price datasets. Some premium datasets (e.g., Zacks earnings surprises) include event labels usable as targets |
| **Update frequency** | WIKI: frozen (no updates). Active premium datasets: daily or intraday |

**Strengths.** The aggregator model is Quandl's key differentiator — it provides a single API to access hundreds of data providers. The WIKI dataset, though frozen, remains one of the cleanest freely available US equity price datasets for historical backtesting. Premium datasets (options chains, short interest, institutional ownership) fill gaps that Yahoo and Alpha Vantage cannot.

**Limitations.** Quandl's transition to Nasdaq Data Link created confusion around branding and access. The free WIKI dataset stopped updating in 2018, so it cannot be used for recent-period evaluation. Premium datasets are expensive and licensing complexity is high.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | Premium datasets (short interest, institutional holdings, earnings surprises) provide rich cross-sectional features ideal for tabular classification/regression |
| Time-Series FMs | **High** | Daily/intraday price series; WIKI is a popular training corpus for TS model papers |
| Financial LLMs/SLMs | Low | No textual data. Purely numeric datasets |

---

### Global Financial Data (GFD)

| Attribute | Detail |
|---|---|
| **Coverage** | Historical financial and economic data spanning 200+ countries. Asset classes: equities, bonds, bills, exchange rates, commodities, real estate. Also includes historical CPI, interest rates, and GDP estimates |
| **Time resolution** | Daily for prices (where available historically); monthly and annual for macro series |
| **Format** | Proprietary download platform (Finaeon); CSV export; Excel-friendly |
| **Access** | Paid subscription. Academic pricing available. Individual plans start ~$300/yr; institutional pricing negotiated |
| **Licensing** | Academic and commercial licenses available. Redistribution restricted |
| **History depth** | Flagship feature: data back to the **1700s–1800s** for major markets (UK equities back to 1694, US back to 1791). No other source matches this depth |
| **Labels / targets** | None. Raw series only. However, the extreme historical depth enables unique regime-labelling studies (wars, depressions, monetary regime changes) |
| **Update frequency** | Monthly updates for most series |

**Strengths.** Unmatched historical depth. GFD is the only source that provides consistent, cleaned financial time series spanning multiple centuries. This is valuable for research on tail risks, regime changes, long-run return distributions, and structural breaks — phenomena that short-history datasets (post-1970) cannot capture. Academic publications on long-run equity premia and bond returns rely heavily on GFD.

**Limitations.** Cost is a barrier for individual researchers. Data before ~1900 is inherently sparse and methodologically reconstructed (spliced from multiple original sources), so quality degrades the further back you go. The proprietary platform is less developer-friendly than API-based sources.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Cross-country, cross-century panel data could test tabular FM generalisation across very different regimes, but feature set is narrow (prices + macro) |
| Time-Series FMs | **High** | Centuries-long series are ideal for testing robustness to distributional shift and long-memory patterns |
| Financial LLMs/SLMs | Low | No text data |

---

### Bloomberg Terminal (Bloomberg L.P.)

| Attribute | Detail |
|---|---|
| **Coverage** | Arguably the most comprehensive single source of financial data in existence. Equities, fixed income, derivatives, commodities, forex, indices, funds — across all major global markets. Also includes fundamentals (income statements, balance sheets, estimates), ownership, corporate actions, ESG scores, credit ratings, economic indicators, and news |
| **Time resolution** | Tick-level, 1-second, 1-minute, daily, and all standard aggregations. Real-time streaming for subscribers |
| **Format** | Bloomberg Terminal GUI; Bloomberg Data License (bulk feeds, FTP/SFTP); Bloomberg API (`blpapi` for Python/C++/Java); Excel add-in (`BDH`, `BDP` functions); Bloomberg Query Language (BQL) for programmatic queries |
| **Access** | Bloomberg Terminal subscription: ~$24,000–$27,000/yr per seat. Bloomberg Data License (institutional bulk data): negotiated pricing, typically six figures annually. No free tier. Academic access available via Bloomberg terminals installed at university libraries (Bloomberg for Education program) |
| **Licensing** | Strictly proprietary. Data cannot be redistributed, shared externally, or published in raw form. ML models trained on Bloomberg data may face restrictions on commercialisation depending on the license agreement. Academic use via terminal is permitted but output sharing is constrained |
| **History depth** | Varies by asset class and market. US equities: 1980s–present (some back to 1960s). Fixed income: comprehensive from 1990s. Tick data: typically 10–15 years depending on exchange |
| **Labels / targets** | Bloomberg provides derived analytics that other sources lack: consensus EPS estimates and revisions, credit ratings changes, analyst recommendation changes, corporate event calendars. These can serve as natural supervised targets without manual label engineering |
| **Update frequency** | Real-time (streaming). Fundamentals update with filing cycles. Estimates update continuously as analysts revise |

**Strengths.** Bloomberg is the gold standard for financial data breadth and quality. Several advantages are difficult or impossible to replicate with free sources:

- **Cross-asset consistency**: a single identifier system (FIGI/ticker) and unified schema across equities, bonds, derivatives, and macro — eliminating the data harmonisation burden of stitching together Yahoo + FRED + Quandl.
- **Derived analytics as labels**: consensus estimates, analyst revisions, credit rating changes, and corporate event flags are natural supervised targets. This directly addresses the "no labelled targets" gap that plagues every free source in this report.
- **Data quality and survivorship**: Bloomberg maintains delisted securities, corporate action adjustments, and point-in-time snapshots of fundamentals — critical for bias-free backtesting that free sources handle poorly.
- **News and text integration**: Bloomberg News, company filings, and press releases are co-indexed with price data, enabling time-aligned multimodal datasets without manual alignment.
- **Reference data**: sector classifications (BICS), supply chain linkages, ESG scores, and ownership structures provide rich auxiliary features for tabular models.

**Limitations.** Cost is the dominant constraint:

- **Price**: at $24K+/yr per terminal seat, Bloomberg is prohibitively expensive for most academic research teams and early-stage projects. Institutional bulk data licenses can run into hundreds of thousands annually.
- **Redistribution restrictions**: raw Bloomberg data cannot be published in papers, shared in open datasets, or included in reproducible research artifacts. Any benchmark built on Bloomberg data is inherently non-reproducible by teams without their own Bloomberg access.
- **Lock-in**: Bloomberg's proprietary data formats and API encourage deep integration, making it costly to migrate to alternative providers later.
- **API learning curve**: the `blpapi` is powerful but lower-level than modern REST APIs. BQL is more ergonomic but limited to the Terminal environment.
- **Academic access is read-only and seat-limited**: university Bloomberg labs typically offer only a handful of terminals, and bulk programmatic downloads are not permitted under education licenses.

**When Bloomberg makes sense for this project.** If the team has institutional access (via an employer or university Bloomberg lab), Bloomberg data should be used as a quality reference and for constructing labelled evaluation sets that free sources cannot provide — particularly consensus estimate revision targets, credit rating migration labels, and point-in-time fundamental features. However, any benchmark or dataset intended for open publication or sharing should be built on open data (yfinance, FRED, etc.) so that results are reproducible.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | The richest source of cross-sectional features (fundamentals, estimates, ESG, ownership) and natural label candidates (rating changes, estimate revisions). Ideal for tabular classification/regression if access is available |
| Time-Series FMs | **High** | Tick-to-daily resolution, multi-decade history, cross-asset coverage. Superior to free alternatives in quality and depth |
| Financial LLMs/SLMs | **High** | Bloomberg News and filings corpus (363B tokens used for BloombergGPT training) is one of the largest proprietary financial text collections. Analyst reports and press releases add further textual richness |

---

## Comparative Summary

| Source | Cost | Best resolution | History | Fundamentals | Paradigm sweet spot |
|---|---|---|---|---|---|
| Yahoo / yfinance | Free | Daily (limited intraday) | ~1970–present | Minimal | Tabular FMs (derived technicals) + TS FMs |
| Alpha Vantage | Freemium | 1-minute | ~2000–present | Yes (US) | TS FMs (intraday research) |
| Quandl | Freemium | Daily (premium: intraday) | 1970–2018 (WIKI) | Premium only | Tabular + TS FMs |
| GFD | Paid | Daily (historical monthly) | 1694–present | Limited macro | TS FMs (long-run studies) |
| Bloomberg | ~$24K+/yr | Tick-level | ~1960s–present | Comprehensive | All three paradigms (if access available) |

## Key Gap

None of these sources provide **labelled targets** (buy/sell signals, regime classifications, default indicators). Any supervised evaluation — whether for tabular FMs, TS FMs, or LLMs — requires the team to derive labels from raw data or source them separately. This has implications for reproducibility (different labelling schemes yield different leaderboard outcomes) and should be addressed in the evaluation requirements framework (see [03-evaluation-requirements.md](../03-evaluation-requirements.md)).
