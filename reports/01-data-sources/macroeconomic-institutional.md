# Macroeconomic & Institutional Data

This report surveys the major open sources of macroeconomic and institutional data relevant to financial ML research. It expands on the "Macroeconomic & Banking" section of the [Unidata blog post](https://unidata.pro/blog/best-financial-ml-datasets/) with particular attention to ML-ready curated subsets (FRED-MD/QD) and cross-paradigm applicability.

---

## Source Summaries

### FRED (Federal Reserve Economic Data)

| Attribute | Detail |
|---|---|
| **Provider** | Federal Reserve Bank of St. Louis |
| **Coverage** | 800,000+ economic time series: interest rates, employment, GDP, CPI, money supply, housing, trade, industrial production, and hundreds of other categories. Primarily US-focused with some international series |
| **Time resolution** | Varies by series — daily (e.g., Treasury yields, Fed Funds rate), weekly, monthly, quarterly, annual |
| **Format** | REST API (JSON, XML); Excel downloads; Python (`fredapi`, `pandas-datareader`), R (`fredr`) |
| **Access** | Free. API key required (instant registration). No rate limit published, but 120 req/min is the practical ceiling |
| **Licensing** | Public domain for FRED-originated data. Third-party series (e.g., S&P, BLS) may carry their own terms. Generally safe for research and commercial use |
| **History depth** | Varies — some series back to 1940s (GDP), others to 1960s (CPI components). Newer series may start in the 2000s |
| **Update frequency** | Depends on release schedule of underlying agency. Monthly series update monthly; daily series update daily |

**Strengths.** FRED is the single most important free macro data source for US-oriented financial ML. The breadth of coverage (800K+ series), clean API, and public-domain licensing make it a default first stop. Integration with Python/R is mature and well-documented.

**Limitations.** Predominantly US data. International coverage exists but is patchy compared to dedicated international sources (IMF, World Bank). Individual series require domain knowledge to select — the raw catalog is overwhelming without curation.

#### FRED-MD and FRED-QD — ML-Ready Curated Subsets

FRED-MD (monthly) and FRED-QD (quarterly) deserve special attention because they were specifically designed for econometric and ML research by [McCracken & Ng](https://research.stlouisfed.org/econ/mccracken/fred-databases).

| Attribute | FRED-MD | FRED-QD |
|---|---|---|
| **Series count** | 128+ monthly series | 248+ quarterly series |
| **History** | 1959–present | 1960–present |
| **Categories** | Output & income, labor market, housing, consumption, money & credit, interest rates & exchange rates, prices, stock market | Same categories plus additional quarterly indicators (GDP components, surveys, financial conditions) |
| **Format** | CSV, updated monthly on McCracken's FRED page. Comes with a companion transformation codes file specifying recommended stationarity transforms (log, diff, etc.) |
| **Preprocessing** | Outlier-adjusted. Missing-value treatment documented. Transformation codes provided for standard factor-model usage |
| **Research usage** | Widely used as the benchmark dataset for dynamic factor models (Stock & Watson), diffusion indices, and macroeconomic nowcasting. Increasingly adopted in ML papers for macro forecasting benchmarks |

**Why FRED-MD/QD matter for this project.** These are among the very few macroeconomic datasets that are *already structured for ML*: fixed schema, documented transforms, consistent updates, long history. They are directly usable as:
- **Tabular FM input**: each month (or quarter) is a row, each series is a feature — a natural tabular classification/regression setup for recession prediction, inflation forecasting, or macro regime detection.
- **Time-series FM input**: 128+ co-moving monthly series provide a rich multivariate TS forecasting benchmark.
- They bridge the tabular and time-series paradigms, making them candidates for the "paradigm-bridging" evaluation tasks recommended in the gap analysis.

**Paradigm relevance (FRED overall + FRED-MD/QD):**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** (FRED-MD/QD) / Medium (raw FRED) | FRED-MD/QD's fixed monthly panel is a natural tabular dataset. Raw FRED requires curation |
| Time-Series FMs | **High** | Core use case for macro TS forecasting. FRED-MD's 128 series since 1959 are a strong multivariate benchmark |
| Financial LLMs/SLMs | Low | No text content. Numeric series could theoretically be serialised into prompts but this is not a standard use pattern |

---

### World Bank Open Data

| Attribute | Detail |
|---|---|
| **Provider** | The World Bank Group |
| **Coverage** | 16,000+ indicators across 200+ countries. GDP, GNI, trade, education, health, poverty, infrastructure, governance, environment. Strongest in development economics and cross-country comparison |
| **Time resolution** | Predominantly annual. Some quarterly and monthly indicators exist but are the exception |
| **Format** | REST API (JSON, XML); bulk CSV/Excel downloads; Python (`wbgapi`, `pandas-datareader`), R (`WDI`) |
| **Access** | Free. API key not required for most endpoints |
| **Licensing** | CC BY 4.0 — free for commercial and non-commercial use with attribution |
| **History depth** | Most indicators from 1960–present. Coverage improves over time (many developing countries have sparse data pre-1990) |
| **Update frequency** | Annual for most indicators (released in spring). World Development Indicators updated semi-annually |

**Strengths.** Unmatched cross-country breadth — 200+ economies with consistent indicator definitions. The CC BY 4.0 license is the cleanest in this survey for commercial use. The data is well-suited for panel studies (country × year) that are natural for tabular models.

**Limitations.** Annual resolution is too coarse for most financial forecasting tasks (no intraday, no daily, very limited monthly). Missing data is endemic for developing countries in earlier decades. Financial-market-specific indicators are limited — this is primarily a development economics dataset.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | Country-year panels with 16K+ features are a textbook tabular setup. Cross-country prediction (GDP growth, sovereign risk, credit rating) is a strong evaluation task |
| Time-Series FMs | Medium | Annual resolution limits utility for TS FMs designed around daily/monthly data. Useful for long-horizon macro forecasting experiments |
| Financial LLMs/SLMs | Low | No text data |

---

### IMF International Financial Statistics (IFS)

| Attribute | Detail |
|---|---|
| **Provider** | International Monetary Fund |
| **Coverage** | Balance of payments, exchange rates, government finance, international reserves, monetary aggregates, interest rates, prices, production for 190+ countries |
| **Time resolution** | Monthly, quarterly, and annual depending on series |
| **Format** | IMF Data portal (JSON REST API); bulk download (SDMX, CSV); Python access via `imfp` or direct API calls |
| **Access** | Free. Some series require registration. API rate limits are relatively generous (10 req/s) |
| **Licensing** | CC BY license for most data. Some series sourced from national authorities may have restrictions. IMF terms of use permit research and non-commercial use broadly; commercial use should verify per-dataset |
| **History depth** | Varies by country and indicator. Major economies from 1950s–present. Developing countries often from 1980s |
| **Update frequency** | Monthly for most financial indicators. Quarterly for government finance. Annual for some structural indicators |

**Strengths.** The IFS is the authoritative source for cross-country financial statistics — foreign reserves, exchange rates, and monetary aggregates are more granular here than in World Bank data. Monthly resolution for many series makes it more useful for TS forecasting than the annual-only World Bank.

**Limitations.** The data portal and API are less developer-friendly than FRED or World Bank. Documentation is dense and oriented toward economists, not ML practitioners. Bulk download workflows are clunky compared to modern REST APIs.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Useful for country-level panel models, but feature extraction requires domain expertise. Less "ML-ready" than FRED-MD |
| Time-Series FMs | Medium | Monthly exchange rate and reserve series are viable TS forecasting targets. Less standardised than FRED-MD |
| Financial LLMs/SLMs | Low | No text data |

---

### OECD Public Finance Data

| Attribute | Detail |
|---|---|
| **Provider** | Organisation for Economic Co-operation and Development |
| **Coverage** | Tax revenue, government spending, public debt, fiscal balance, social expenditure, pensions, sovereign risk indicators for 38 OECD member countries plus some partner economies |
| **Time resolution** | Primarily annual and quarterly |
| **Format** | OECD.Stat portal; REST API (SDMX/JSON); bulk CSV/Excel; Python via `pandasdmx` |
| **Access** | Free for most datasets. Some publications are behind paywalls but underlying data is open |
| **Licensing** | Open for research and commercial use with attribution. Specific terms on [OECD Terms and Conditions](https://www.oecd.org/termsandconditions/) |
| **History depth** | Most fiscal series from 1970s–present for founding members. Newer members have shorter history |
| **Update frequency** | Quarterly for fiscal data. Annual for structural indicators |

**Strengths.** OECD data fills a niche that FRED (US-only) and World Bank (development-focused) miss: detailed fiscal and public finance data for advanced economies. This is valuable for sovereign risk modelling, fiscal sustainability analysis, and cross-country policy comparison. Data definitions are harmonised across countries, reducing preprocessing burden.

**Limitations.** Coverage limited to OECD members and partners (~50 countries vs. 200+ at World Bank). Annual/quarterly resolution limits utility for higher-frequency models. The SDMX-based API has a steeper learning curve than REST/JSON APIs.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | **High** | Harmonised cross-country fiscal panels are strong candidates for tabular classification (e.g., fiscal crisis prediction, credit rating models) |
| Time-Series FMs | Medium | Quarterly fiscal series are usable but resolution is coarse for most TS FM architectures designed around daily/monthly data |
| Financial LLMs/SLMs | Low | No text data |

---

### EU Open Data Portal

| Attribute | Detail |
|---|---|
| **Provider** | European Commission / Eurostat |
| **Coverage** | Broad European economic statistics: GDP, unemployment, trade, inflation, government finance, industry, agriculture, energy, transport. Includes subnational (NUTS-2, NUTS-3 regional) data not available from other sources |
| **Time resolution** | Monthly, quarterly, and annual depending on indicator |
| **Format** | REST API (JSON-stat); bulk download (CSV, TSV, SDMX); Python via `eurostat` package or `pandasdmx` |
| **Access** | Free. No API key required |
| **Licensing** | Commission Decision 2011/833/EU — free for commercial and non-commercial reuse with attribution |
| **History depth** | EU member states typically from 1990s or EU accession date. Eurozone aggregates from 1999 |
| **Update frequency** | Monthly for flash estimates. Quarterly for GDP and fiscal data. Annual for structural indicators |

**Strengths.** Unique subnational granularity — NUTS-2/NUTS-3 regional data enables spatial analysis not possible with country-level sources. Strong coverage of EU-specific indicators (fiscal rules compliance, convergence criteria, banking supervision data). Free and commercially reusable.

**Limitations.** EU-only geographic scope (no US, Asia, LatAm). Data portal UX is notoriously difficult to navigate. Some indicator definitions changed after methodology revisions (ESA 2010 transition), creating structural breaks.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Medium | Regional panel data (NUTS regions × time) is a unique tabular setup, but requires significant preprocessing |
| Time-Series FMs | Medium | Monthly/quarterly European series complement US-centric FRED data; useful for multi-region TS experiments |
| Financial LLMs/SLMs | Low | No text data |

---

## Comparative Summary

| Source | Cost | Geographic scope | Best resolution | ML-ready subset | Paradigm sweet spot |
|---|---|---|---|---|---|
| FRED | Free | US (some international) | Daily | **FRED-MD/QD** | Tabular + TS FMs |
| World Bank | Free | Global (200+ countries) | Annual | — | Tabular FMs (panels) |
| IMF IFS | Free | Global (190+ countries) | Monthly | — | TS FMs (forex, reserves) |
| OECD | Free | OECD members (~50) | Quarterly | — | Tabular FMs (fiscal) |
| EU Open Data | Free | EU members (~27) | Monthly | — | Regional tabular/TS |

## Key Takeaways

1. **FRED-MD/QD should be a priority dataset** for the team. It is free, ML-ready, well-documented, and bridges the tabular and time-series paradigms. It is one of the few datasets in this survey that can serve as a direct evaluation benchmark without extensive preprocessing.

2. **All sources in this report are free**, in contrast to the market data sources (where GFD and Quandl premium are paid). Budget is not a constraint for macro data acquisition.

3. **No source provides labelled targets.** As with market data, the team must derive supervised labels (recession indicators, inflation regime changes, crisis events) from raw series. Standard label sources include NBER recession dates (for US), CEPR dates (for Europe), and IMF crisis databases.

4. **No source provides text data.** These datasets are purely numeric. For financial LLM/SLM evaluation, the team should look to the [financial text & filings report](financial-text-and-filings.md) instead. However, macro features from these sources can serve as auxiliary inputs to multimodal models that combine text and numeric data.

5. **Geographic complementarity matters.** FRED covers the US deeply, World Bank and IMF cover the globe broadly, OECD covers advanced economies with fiscal depth, and EU Open Data adds European subnational granularity. A well-designed evaluation suite should sample across geographies to test generalisation.
