# Financial LLM / SLM Benchmarks

This report surveys the evaluation landscape for large and small language models applied to finance. Among the three paradigms under consideration, financial LLM/SLM evaluation is the most developed, with comprehensive benchmark suites, living leaderboards, and a substantial body of survey literature.

---

## Primary Benchmark Suites

### FinBen (Financial Benchmark)

| Attribute | Detail |
|---|---|
| **Provider** | The FinAI research group |
| **URL** | [thefin.ai/dataset-benchmark/finben](https://www.thefin.ai/dataset-benchmark/finben) |
| **Scale** | **42 datasets** across **24 tasks** in **8 domains** |
| **Leaderboard** | Living leaderboard on HuggingFace, continuously updated with new model submissions |

**Domain and task breakdown:**

| Domain | Example tasks | Example datasets |
|---|---|---|
| **Information extraction** | Named entity recognition, relation extraction | FiNER, FinRED |
| **Textual analysis** | Sentiment classification, stance detection | FPB, FiQA-SA, FOMC stance |
| **Question answering** | Numerical reasoning over tables, financial document QA | FinQA, TATQA, ConvFinQA |
| **Text generation** | Earnings call summarisation, report generation | ECTSum, EDTSum |
| **Forecasting** | Stock movement prediction, volatility estimation | StockNet, CIKM18 |
| **Risk management** | Credit scoring, fraud detection | German Credit, Kaggle fraud |
| **Decision-making** | Trading signal generation, portfolio allocation | FLARE tasks |
| **Spanish-language finance** | Cross-lingual financial NLP | MultiFin (ES) |

**Current leaderboard highlights:**
- **GPT-4** and **Llama 3.1 (405B)** lead overall across the 24 tasks
- **Surprising finding: smaller models (7B) outperform larger ones on forecasting tasks.** This is counterintuitive and suggests that financial forecasting from text may benefit more from domain-specific training than from raw model scale. FinMA-7B and FinGPT-7B outperform GPT-4 on stock movement prediction tasks within FinBen
- Instruction tuning shows **limited benefit for complex financial QA** — fine-tuned models on FinQA and TATQA do not consistently outperform prompted base models, suggesting that numerical reasoning capability is not easily instilled through instruction tuning alone

**Why FinBen matters.** FinBen is the single most important benchmark for evaluating financial LLMs. Its 42-dataset, 24-task, 8-domain coverage is unmatched. For the team, FinBen provides:
1. A ready-made evaluation protocol across the full spectrum of financial NLP tasks
2. A living leaderboard to track the competitive landscape
3. A dataset inventory — the 42 datasets can be individually inspected and potentially reused for custom evaluation
4. Evidence-based guidance on where LLMs add value (textual analysis, QA) vs. where they struggle (forecasting, numerical reasoning)

---

### MultiFinBen (Multilingual Financial Benchmark)

| Attribute | Detail |
|---|---|
| **Provider** | Academic (2025 publication) |
| **Extends** | FinBen with three new dimensions |
| **New dimensions** | (1) **Multilingual** evaluation beyond English, (2) **Multimodal** tasks incorporating tables and charts alongside text, (3) **Difficulty-aware** scoring that weights hard examples more heavily |

**Why MultiFinBen matters.** MultiFinBen addresses three specific blind spots in FinBen:

1. **Multilingual coverage.** FinBen is predominantly English. MultiFinBen adds evaluation in Chinese, Japanese, German, French, Spanish, and other languages where financial text is abundant but evaluation infrastructure is lacking. This matters if the team operates in or analyses non-English markets.

2. **Multimodal integration.** Financial documents are inherently multimodal — annual reports contain text, tables, and charts in interleaved layouts. MultiFinBen tests whether LLMs can jointly reason over text and structured visual/tabular elements, moving beyond the text-only evaluation of FinBen.

3. **Difficulty-aware evaluation.** Aggregate scores on FinBen can be dominated by easy tasks (sentiment classification) while masking poor performance on hard tasks (multi-step numerical reasoning). Difficulty-aware weighting provides a more honest assessment of model capabilities.

**Current status.** As a 2025 publication, MultiFinBen adoption is still early. The team should monitor it as the likely successor or complement to FinBen for comprehensive evaluation.

---

## Key Individual Benchmarks Within the Ecosystem

### FinQA (Financial Question Answering)

| Attribute | Detail |
|---|---|
| **Task** | Numerical reasoning over financial tables and text. Given a financial table (e.g., from a 10-K filing) and a natural language question, produce the correct numerical answer — often requiring multi-step arithmetic (additions, divisions, percentage calculations) |
| **Scale** | ~8,000 QA pairs with annotated reasoning programs |
| **Source** | Earnings reports from S&P 500 companies |
| **Why it matters** | FinQA is the hardest widely-used financial QA benchmark. It tests whether LLMs can perform the kind of multi-step numerical reasoning that financial analysts do daily (e.g., "What was the year-over-year change in operating margin?"). Current SOTA accuracy is still well below human expert performance, making it a discriminating benchmark |

### TATQA (Tabular and Textual QA)

| Attribute | Detail |
|---|---|
| **Task** | Hybrid QA requiring reasoning across both tables and free text within financial documents |
| **Scale** | ~16,000 QA pairs |
| **Distinction from FinQA** | TATQA explicitly requires cross-referencing between tabular and textual information (e.g., "According to Note 5, what was the impairment charge recorded in the table above?"), testing a different reasoning pattern than FinQA's primarily table-focused questions |

### ECTSum / EDTSum (Earnings Call/Document Summarisation)

| Attribute | Detail |
|---|---|
| **Tasks** | ECTSum: summarise earnings call transcripts. EDTSum: summarise earnings-related documents |
| **Why they matter** | Summarisation is one of the highest-ROI applications of LLMs in finance — condensing 60-minute earnings calls into 3-paragraph summaries saves analyst time. These benchmarks test whether summaries are faithful (no hallucinated figures), complete (key metrics included), and concise |

---

## BloombergGPT as a Reference Point

BloombergGPT (2023) is not a benchmark but serves as an important reference for understanding financial LLM capabilities and limitations:

| Attribute | Detail |
|---|---|
| **Parameters** | 50 billion |
| **Training data** | ~700B tokens total (363B financial + 345B general) |
| **Evaluation tasks** | Sentiment analysis (FPB), NER (FiNER), news classification, QA, headline generation |
| **Key results** | Outperformed GPT-3 on financial tasks while remaining competitive on general NLP. Demonstrated that domain-specific pre-training on hundreds of billions of financial tokens improves financial task performance |

**Relevance for the team.** BloombergGPT demonstrates that domain pre-training works, but at massive scale (50B params, 700B tokens). The open question is whether smaller, more accessible approaches (fine-tuning a 7B open-source model on curated financial data) can achieve comparable results on specific tasks. FinBen leaderboard evidence suggests yes — at least for forecasting — which is an important data point for the team's architecture decision.

---

## 2025 Survey Findings (arXiv 2504.07274)

A comprehensive 2025 survey of 374 NLP papers applied to finance identified several systemic gaps:

| Gap | Detail |
|---|---|
| **Forecasting scope** | Most papers evaluate only stock movement prediction. Forecasting of credit events, macro indicators, and bond spreads from text is underexplored |
| **Multilingual coverage** | The vast majority of papers use English-only data. Chinese is a distant second. Other financial-text-rich languages (Japanese, German, French) are barely represented |
| **Financial-metric-based evaluation** | Papers predominantly report NLP metrics (accuracy, F1, ROUGE) rather than financial metrics (Sharpe, alpha, tracking error). This disconnect means that "better" NLP performance may not translate to financial utility |
| **Reproducibility** | Many papers use proprietary data (Bloomberg, Refinitiv) or undocumented preprocessing, making results irreproducible |
| **Instruction tuning plateau** | Across the surveyed papers, instruction tuning shows diminishing returns on complex financial tasks. Models gain more from domain-specific pre-training or specialised fine-tuning than from general instruction tuning |

**Implication for the team.** These gaps represent opportunities. A well-designed financial LLM evaluation that addresses even one of these (e.g., financial-metric evaluation, or multilingual coverage) would be a meaningful contribution and would differentiate the team's work.

---

## Open Financial LLM Leaderboard

| Attribute | Detail |
|---|---|
| **URL** | [huggingface.co/spaces/finosfoundation/Open-Financial-LLM-Leaderboard](https://huggingface.co/spaces/finosfoundation/Open-Financial-LLM-Leaderboard) |
| **Maintained by** | FINOS Foundation (Fintech Open Source Foundation) |
| **Scope** | Ranks open-source LLMs on financial tasks from FinBen and related benchmarks |
| **Why it matters** | Restricts to open-source models only, providing a more relevant competitive landscape for teams that cannot use proprietary APIs (GPT-4, Claude) in production. Tracks the pace at which open models are closing the gap with closed ones on financial tasks |

---

## Evaluation Methodology Recommendations

For financial LLM/SLM evaluation, the team should adopt:

1. **Use FinBen as the baseline evaluation suite.** It provides ready-made tasks, datasets, and a competitive reference. Extend with MultiFinBen tasks as they mature.

2. **Report financial metrics, not just NLP metrics.** For forecasting tasks, include Sharpe ratio, RankIC, and directional accuracy. For QA tasks, include financial accuracy (are the numbers correct?) alongside standard F1/EM.

3. **Test small models explicitly.** The FinBen finding that 7B models outperform larger ones on forecasting is actionable — if cost and latency matter (and they always do in production finance), smaller models may be the right choice.

4. **Evaluate instruction tuning critically.** Given evidence of limited benefit for complex financial tasks, compare instruction-tuned models against base models with few-shot prompting to determine whether fine-tuning effort is justified.

5. **Include hallucination evaluation.** Financial LLMs that generate plausible but incorrect numbers (e.g., wrong revenue figures, fabricated ratios) pose serious risks. Any evaluation should include factuality checks against known ground truth.

6. **Test on multiple financial sub-domains.** FinBen's 8-domain structure makes this straightforward. Avoid aggregating scores across domains — a model that excels at sentiment but fails at numerical reasoning has a very different utility profile than one that does the reverse.

---

## Key References

- [FinBen benchmark and leaderboard](https://www.thefin.ai/dataset-benchmark/finben)
- [Open Financial LLM Leaderboard](https://huggingface.co/spaces/finosfoundation/Open-Financial-LLM-Leaderboard)
- [Survey: Language Modeling for Finance (2025)](https://arxiv.org/abs/2504.07274) — 374-paper review
- BloombergGPT: Wu et al. (2023) — 50B parameter financial LLM
- FinQA: Chen et al. (2021) — numerical reasoning over financial tables
- TATQA: Zhu et al. (2021) — tabular + textual QA
