# Financial Text & Filings Data

This report surveys the major sources of financial text data relevant to ML research, with an emphasis on NLP, question-answering, and sentiment analysis tasks. It goes beyond the [Unidata blog post](https://unidata.pro/blog/best-financial-ml-datasets/) into the rapidly expanding financial NLP data ecosystem.

---

## Source Summaries

### SEC EDGAR Filings Corpus

| Attribute | Detail |
|---|---|
| **Provider** | U.S. Securities and Exchange Commission |
| **Coverage** | All public-company filings with the SEC: 10-K (annual reports), 10-Q (quarterly), 8-K (material events), S-1 (IPO prospectuses), proxy statements (DEF 14A), insider transactions (Form 4), and more. Covers ~8,000 public companies actively filing, plus historical filings from delisted companies |
| **Time resolution** | Event-driven (filings as they occur). 10-K annually, 10-Q quarterly, 8-K on material events |
| **Format** | Raw HTML/SGML on EDGAR full-text search; structured XBRL for financials (since 2009 for large filers). Bulk download via EDGAR Full-Text Search or EDGAR archives (FTP-like). Python: `edgartools`, `sec-edgar-downloader`, `edgar` |
| **Access** | Free. No API key. Rate limit: 10 requests/second (identify via User-Agent header) |
| **Licensing** | Public domain. U.S. government works — no copyright restrictions. Commercial use fully permitted |
| **History depth** | EDGAR electronic filings from 1993–present. Some filings back to 1996 in full text. XBRL structured data from 2009 |
| **Labels / targets** | No labels provided. Filing text is raw; researchers derive sentiment, readability scores, or pair with stock returns around filing dates for event studies |
| **Update frequency** | Continuous — filings appear within minutes of submission |

**Strengths.** EDGAR is the single most important free financial text corpus. The public-domain license is unmatched — no restrictions on redistribution, training, or commercial use. The corpus is massive (millions of filings across three decades), diverse (from boilerplate risk factors to material-event disclosures), and naturally time-stamped, enabling event-study alignment with price data. For LLM pre-training and fine-tuning, EDGAR filings represent a high-quality, domain-specific text source with no licensing friction.

**Limitations.** Raw filings are noisy: HTML formatting is inconsistent across filers and eras, boilerplate text inflates document length (10-Ks can exceed 100 pages), and extracting clean text from SGML/HTML requires non-trivial parsing. XBRL adoption is uneven — smaller filers often have lower-quality structured data. No semantic labels, entity annotations, or QA pairs are provided natively.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Low | XBRL financial statements can be extracted as structured tables, but this is a derived use case requiring significant preprocessing |
| Time-Series FMs | Low | Text data, not time series. Filing dates can be aligned with price series for multimodal setups |
| Financial LLMs/SLMs | **High** | Core pre-training and fine-tuning corpus. BloombergGPT used SEC filings as a major component of its 363B-token training set |

---

### SEC-QA

| Attribute | Detail |
|---|---|
| **Provider** | Academic (ACL 2025 publication) |
| **Coverage** | Systematic evaluation corpus for question-answering over SEC filings. Questions require extracting and reasoning over information from 10-K and 10-Q filings |
| **Format** | Dataset with question-answer pairs linked to specific filing passages |
| **Access** | Published alongside the ACL 2025 paper. Expected to be available on standard academic repositories |
| **Licensing** | Academic use. Specific terms tied to publication |
| **Scale** | Designed for systematic evaluation — covers multiple companies, filing types, and reasoning complexity levels |

**Strengths.** SEC-QA fills a critical gap: it provides a structured, systematic evaluation corpus specifically for financial document QA. Unlike ad hoc QA datasets derived from earnings calls or news, SEC-QA is designed around the formal filing corpus, testing an LLM's ability to locate, extract, and reason over specific financial information buried in long-form regulatory documents. This closely matches real-world use cases (analysts querying filings, compliance review, due diligence).

**Limitations.** As a 2025 publication, adoption is still early. The dataset size and diversity may be limited compared to broader NLP QA benchmarks. Evaluation requires access to the corresponding EDGAR filings, adding a data-pipeline dependency.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Low | Not tabular data |
| Time-Series FMs | Low | Not time-series data |
| Financial LLMs/SLMs | **High** | Purpose-built LLM evaluation benchmark for financial document QA |

---

### FinNLI (Financial Natural Language Inference)

| Attribute | Detail |
|---|---|
| **Provider** | Academic research |
| **Coverage** | 21,000 premise-hypothesis pairs drawn from SEC filings, annual reports, and earnings call transcripts. Each pair is expert-annotated with an entailment label (entailment, contradiction, neutral) |
| **Format** | Standard NLI dataset format — premise text, hypothesis text, gold label. Available for download from the associated publication |
| **Access** | Free for research |
| **Licensing** | Academic use |
| **Scale** | ~21,000 annotated pairs — moderate scale, but high quality due to expert annotation (not crowdsourced) |
| **Annotation quality** | Expert-annotated by financial domain specialists, not crowdworkers. This is a significant quality advantage over crowdsourced NLI datasets |

**Strengths.** FinNLI is one of the few NLI datasets grounded in real financial documents rather than synthetic or general-domain text. The expert annotation is a major differentiator — financial text contains domain-specific reasoning patterns (hedging language, conditional disclosures, forward-looking statements) that crowdworkers may misinterpret. The 21K-pair scale is sufficient for fine-tuning evaluation and few-shot prompting experiments.

**Limitations.** The 21K scale is modest for pre-training purposes. English-only. The premise-hypothesis format tests a specific reasoning capability (textual entailment) rather than broader financial understanding.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Low | Not tabular data |
| Time-Series FMs | Low | Not time-series data |
| Financial LLMs/SLMs | **High** | Directly evaluates financial reasoning capabilities — a core LLM/SLM task. Useful for fine-tuning and benchmark evaluation |

---

### FinancialPhraseBank (FPB)

| Attribute | Detail |
|---|---|
| **Provider** | Malo et al. (2014), hosted on HuggingFace and various academic repositories |
| **Coverage** | ~4,845 English sentences from financial news, each annotated with sentiment (positive, negative, neutral) from the perspective of a retail investor |
| **Format** | CSV / text with sentiment labels. Multiple agreement-level subsets: sentences where 50%+, 66%+, 75%+, or 100% of annotators agree |
| **Access** | Free. Available on [HuggingFace](https://huggingface.co/datasets/financial_phrasebank) and academic mirrors |
| **Licensing** | CC BY-NC-SA 3.0 — non-commercial use with attribution and share-alike |
| **Scale** | 4,845 sentences. Small by modern LLM standards but well-curated |
| **Annotation** | 16 annotators with financial background. Agreement-level subsets allow researchers to control for annotation noise |

**Strengths.** FPB is the most widely used sentiment benchmark in financial NLP. Its simplicity (sentence-level, three-class) makes it a standard evaluation task across FinBERT, FinGPT, BloombergGPT, and virtually every financial LLM paper. The agreement-level subsets are a thoughtful design — they let researchers distinguish between genuinely ambiguous sentences and clear-cut cases, enabling more nuanced evaluation.

**Limitations.** Small dataset by modern standards (4,845 sentences). The three-class sentiment scheme is coarse — real financial sentiment exists on a spectrum and is context-dependent (a "revenue declined 5%" sentence is negative for equity but may be neutral for credit). The CC BY-NC-SA license prohibits commercial use. The dataset is from 2014, so it reflects the language patterns of that era.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Low | Sentiment labels could feed tabular models as a feature, but this is a derived use |
| Time-Series FMs | Low | Not time-series data. Sentiment scores could be aligned with price series as an auxiliary signal |
| Financial LLMs/SLMs | **High** | The standard sentiment classification benchmark for financial LLMs. Used in FinBen, BloombergGPT evaluation, and most financial NLP papers |

---

### Earnings Call Transcripts

| Attribute | Detail |
|---|---|
| **Sources** | **Seeking Alpha** (partial free access, premium subscription ~$240/yr for full archive), **S&P Capital IQ** (institutional, ~$10K+/yr), **Refinitiv/LSEG** (institutional), **The Motley Fool** (selected transcripts, free), **SEC filings** (some companies file transcripts as 8-K exhibits) |
| **Coverage** | Quarterly earnings call transcripts for US and international public companies. Typically includes prepared remarks and Q&A sections |
| **Time resolution** | Quarterly (tied to earnings reporting cycle). Companies typically report within 30–60 days of quarter-end |
| **Format** | Varies by source — HTML, PDF, or structured text. No standard schema across providers. Prepared remarks and Q&A are usually separated |
| **Access** | Fragmented. No single free, comprehensive source. Seeking Alpha has the broadest free coverage but limits access. Academic access typically through WRDS or Capital IQ |
| **Licensing** | Generally proprietary. Seeking Alpha transcripts are copyrighted. Capital IQ / Refinitiv data is strictly licensed. Academic fair use may apply for research on small excerpts |
| **History depth** | Seeking Alpha: ~2004–present for major US companies. Capital IQ: more complete, ~2000–present. Historical coverage declines sharply before 2005 |
| **Labels / targets** | No labels. Researchers typically derive: post-call stock price reaction (event study), management tone/sentiment, analyst question sentiment, forward guidance extraction |

**Strengths.** Earnings calls are one of the richest sources of forward-looking financial text. The Q&A section — where analysts probe management — is particularly valuable because it captures spontaneous responses, hedging, evasion, and information asymmetry in ways that prepared filings cannot. Academic research has demonstrated that earnings call features (tone, complexity, Q&A dynamics) predict short-term returns, earnings surprises, and credit events. For LLM evaluation, summarisation of earnings calls (ECTSum, EDTSum benchmarks) is an active research area.

**Limitations.** The fragmented access landscape is the primary barrier. No single free source provides comprehensive, clean, structured transcripts with full historical coverage. Parsing quality varies across providers (speaker attribution, section splitting). Copyright restrictions prevent most researchers from sharing processed datasets, creating reproducibility challenges.

**Paradigm relevance:**

| Paradigm | Relevance | Notes |
|---|---|---|
| Tabular FMs | Low | Extracted features (tone scores, word counts, hedging metrics) can become tabular inputs, but this requires an NLP preprocessing pipeline |
| Time-Series FMs | Low | Not inherently time-series data, though quarterly tone trajectories could be modelled as a short series |
| Financial LLMs/SLMs | **High** | Core evaluation domain: summarisation (ECTSum/EDTSum), sentiment analysis, forward guidance extraction, and Q&A understanding |

---

### BloombergGPT Training Data (Reference Point)

This entry does not describe an accessible dataset but provides an important reference point for understanding the scale of financial text data used in practice.

| Attribute | Detail |
|---|---|
| **Model** | BloombergGPT (50B parameters, 2023) |
| **Financial training corpus** | **363 billion tokens** of financial text: Bloomberg News articles, SEC filings, press releases, Bloomberg-authored content, financial web pages |
| **General training corpus** | Combined with ~345B tokens of general text (The Pile, C4, Wikipedia) for a total of ~700B training tokens |
| **Composition of financial corpus** | ~56% web content, ~30% news, ~9% filings, ~5% press/other |

**Why this matters.** BloombergGPT's 363B-token financial corpus establishes a scale benchmark for financial LLM training. It demonstrates that:
- Effective financial LLMs require hundreds of billions of domain tokens, not millions.
- SEC filings alone are insufficient (~9% of their corpus) — news, web content, and press releases provide essential diversity.
- The proprietary nature of Bloomberg's corpus (Bloomberg News is not publicly available) means that open-source efforts must find alternative sources to reach comparable scale and domain coverage.
- For the team's purposes, this frames the challenge: if pursuing financial LLM/SLM development, assembling a training corpus of comparable scale from open sources (EDGAR + open news + financial web) is feasible but requires deliberate curation effort.

---

## Comparative Summary

| Source | Cost | Scale | Labels | Primary task | License clarity |
|---|---|---|---|---|---|
| SEC EDGAR | Free | Millions of filings | None | Pre-training, QA, extraction | **Public domain** |
| SEC-QA | Free | Evaluation-scale | QA pairs | Document QA evaluation | Academic |
| FinNLI | Free | 21K pairs | Entailment (expert) | NLI evaluation | Academic |
| FPB | Free | 4,845 sentences | Sentiment (3-class) | Sentiment classification | CC BY-NC-SA |
| Earnings transcripts | Free–$10K+ | Thousands of calls/yr | None | Summarisation, sentiment, guidance | Mostly proprietary |
| BloombergGPT corpus | Not available | 363B tokens | N/A | Reference for scale | Proprietary |

## Key Takeaways

1. **SEC EDGAR is the foundational open text corpus** for financial LLM work. Its public-domain status is unique among financial data sources and makes it the only large-scale text corpus that can be freely redistributed in open research.

2. **Evaluation datasets are small but well-targeted.** FPB (sentiment), FinNLI (entailment), and SEC-QA (document QA) each test a specific capability. Together they cover three of the most important financial NLP tasks, though scale is modest.

3. **Earnings call access is fragmented and expensive.** Despite being one of the most research-productive text sources in finance, there is no clean, free, comprehensive dataset of earnings call transcripts. This is a persistent blind spot for open financial NLP research.

4. **BloombergGPT's 363B tokens sets the scale bar.** Any team pursuing financial LLM development should understand that effective domain adaptation requires large-scale, diverse financial text — not just filings.

5. **Text data primarily serves the LLM/SLM paradigm**, but derived features (sentiment scores, readability metrics, topic distributions) can serve as inputs to tabular and time-series models in multimodal setups. Cross-paradigm value is realised through feature engineering, not direct ingestion.
