# Tabular Foundation Model Benchmarks

This report surveys the evaluation ecosystem for tabular foundation models (tabular FMs) — pre-trained models that generalise across arbitrary tabular schemas without task-specific feature engineering. The tabular FM paradigm is the youngest of the three under consideration, and its benchmark infrastructure reflects that immaturity, particularly in finance.

---

## General-Purpose Tabular Benchmarks

### TabArena

| Attribute | Detail |
|---|---|
| **Type** | Living benchmark with public leaderboard |
| **URL** | [tabarena.ai](https://tabarena.ai) |
| **Maintained by** | Academic/industry consortium |
| **Datasets** | Continuously growing, manually curated collection of tabular classification and regression tasks |
| **Evaluation protocol** | Tests models in three settings: (1) **default** hyperparameters, (2) **tuned** hyperparameters, (3) **ensembled** configurations. This multi-setting protocol is important because tabular FM papers often report only default-setting results, which can misrepresent practical performance |
| **Leaderboard** | Public, continuously updated. Allows community submissions |
| **Key design choice** | Manual curation — datasets are selected for quality and diversity rather than scraped from OpenML or Kaggle indiscriminately. This reduces the "benchmark gaming" problem where models overfit to well-known datasets |

**Why it matters.** TabArena is currently the most credible general-purpose tabular FM benchmark. Its living-benchmark design addresses two chronic problems in tabular ML evaluation: (1) benchmark staleness (fixed benchmarks get overfitted over time) and (2) hyperparameter sensitivity (tabular FMs are highly sensitive to tuning, so default-only evaluation is misleading). For the team, TabArena provides the methodological template for how tabular FM evaluation should be conducted — even if the datasets themselves are not finance-specific.

**Current SOTA on TabArena.** As of early 2025:
- **TabICLv2** leads on both default and tuned settings
- **RealTabPFN-2.5** is competitive, especially in the default (zero-configuration) setting
- **TabPFN** excels on small datasets (<10K rows) but degrades at scale
- **Gradient boosting** (XGBoost, LightGBM, CatBoost) remains highly competitive on medium-to-large datasets, particularly in the tuned setting

**Implication for finance.** The finding that gradient boosting remains competitive — and sometimes superior — to tabular FMs at scale is directly relevant to finance, where many production datasets (loan tapes, transaction logs, claims) exceed 100K rows. Tabular FMs may offer the most value on smaller, heterogeneous datasets (e.g., quarterly corporate default prediction with hundreds, not millions, of observations).

---

### TALENT

| Attribute | Detail |
|---|---|
| **Type** | Large-scale benchmark suite |
| **Scale** | 300+ datasets |
| **Source** | Curated from OpenML and other repositories |
| **Evaluation protocol** | Standardised train/test splits, fixed preprocessing |
| **Focus** | Breadth — tests generalisation across a wide range of tabular tasks |

**Why it matters.** TALENT complements TabArena's depth with breadth. While TabArena focuses on carefully curated high-quality datasets, TALENT tests whether tabular FMs generalise across 200+ diverse tasks — a stronger test of the "foundation" claim (i.e., that the model works out-of-the-box on arbitrary tables).

**Current SOTA on TALENT.** TabICLv2 and RealTabPFN-2.5 also lead here, confirming the TabArena results. The consistency across both benchmarks suggests these rankings are robust, not artifacts of dataset selection.

---

## Finance-Specific Gap Analysis

### The Core Problem: No Financial Tabular Benchmark Exists

This is the most important finding for the team. Despite the maturity of tabular ML in finance (credit scoring, fraud detection, insurance pricing, risk modelling), **there is no established benchmark suite dedicated to financial tabular tasks**. The reasons:

1. **Data privacy.** The most natural financial tabular datasets — credit applications, transaction logs, insurance claims, loan performance — contain sensitive personal information. Institutions cannot share raw data, and anonymisation often degrades data quality to the point of uselessness.

2. **Proprietary competitive advantage.** Banks and insurers treat their feature-engineering pipelines and labeled datasets as core IP. Sharing them would erode competitive advantage.

3. **Regulatory constraints.** GDPR, CCPA, and sector-specific regulations (GLBA in banking, HIPAA-adjacent in insurance) create legal barriers to data sharing even when institutions are willing.

4. **Scattered public datasets.** A few financial tabular datasets exist in the wild — UCI German Credit, Kaggle credit card fraud, LendingClub loans — but they are:
   - Small (German Credit: 1,000 rows)
   - Outdated (many from the 2000s–2010s)
   - Not maintained as part of a benchmark suite
   - Not designed for tabular FM evaluation (no standardised splits, no multi-setting protocol)

### Financial Tabular Tasks the Team Could Curate (Important)

If the team decides to invest in the tabular FM paradigm, the following tasks could form a finance-specific benchmark. For each, a candidate data source is identified:

| Task | Description | Candidate source | Scale estimate |
|---|---|---|---|
| **Credit default prediction** | Predict whether a borrower defaults within 12 months | LendingClub public loan data (retired but archived), or FRED macro + derived features | 10K–1M rows |
| **Fraud detection** | Classify transactions as fraudulent or legitimate | Kaggle Credit Card Fraud (284K transactions, 492 fraud), or synthetic generation | 100K–1M rows |
| **Insurance claim prediction** | Predict claim severity or occurrence | Various Kaggle insurance datasets; synthetic augmentation | 10K–100K rows |
| **Stock movement classification** | Predict next-day return direction from tabular features (technicals, fundamentals) | yfinance + derived technical indicators (see [market-and-price-data.md](../01-data-sources/market-and-price-data.md)) | 100K–1M rows |
| **Macro regime classification** | Predict recession/expansion from macro features | FRED-MD monthly panel (see [macroeconomic-institutional.md](../01-data-sources/macroeconomic-institutional.md)) | ~800 rows (monthly since 1959), but very wide feature set |
| **Corporate rating prediction** | Predict credit rating from financial statement features | Compustat or hand-collected from SEC filings | 1K–10K rows |

### Assessment: Where Tabular FMs Could Add Value (Important)

Based on current SOTA (TabICLv2, TabPFN) performance characteristics:

| Dataset characteristic | Tabular FM advantage | Gradient boosting advantage |
|---|---|---|
| Small sample (<10K rows) | **Strong** — TabPFN and in-context learning methods excel here | Weak — overfitting risk, tuning overhead |
| Large sample (>100K rows) | Modest — gains over tuned XGBoost are marginal | **Strong** — scalable, well-understood |
| Heterogeneous features (mixed numeric + categorical) | **Strong** — pre-trained representations handle diverse types without manual encoding | Moderate — requires feature engineering |
| Missing data | **Strong** — many tabular FMs handle missingness natively | Moderate — XGBoost handles natively but others need imputation |
| Few labelled examples (few-shot) | **Strong** — in-context learning enables prediction from <100 examples | Weak — insufficient data for tree-based training |
| Well-understood domain (credit, fraud) | Modest — feature engineering knowledge is well-established | **Strong** — decades of domain-specific pipeline optimisation |

**Bottom line for finance.** Tabular FMs are most compelling for (a) small-sample financial tasks (e.g., corporate default prediction, where positive labels are rare and dataset sizes are limited) and (b) rapid prototyping across diverse financial tables without task-specific feature engineering. For large-scale production tasks (high-frequency fraud detection, loan underwriting at scale), gradient boosting with domain-engineered features will likely remain competitive or superior.

---

## Evaluation Methodology Recommendations

Based on the TabArena and TALENT designs, any finance-specific tabular evaluation should adopt:

1. **Multi-setting evaluation.** Report results with default, tuned, and ensembled hyperparameters. Default-only results overstate tabular FM advantages; tuned-only results overstate gradient boosting advantages.

2. **Standardised preprocessing.** Fix train/test splits, imputation strategies, and categorical encoding to ensure apples-to-apples comparison.

3. **Multiple metrics.** Report AUC-ROC, F1, and calibration (Brier score, ECE) for classification; RMSE and MAE for regression. Financial tasks are often imbalanced (rare defaults, rare fraud), so accuracy alone is misleading.

4. **Include gradient boosting baselines.** XGBoost, LightGBM, and CatBoost should always be included as baselines. Any tabular FM that cannot beat tuned gradient boosting on a given task should be honestly reported.

5. **Temporal splits for financial data.** Unlike standard ML, financial tasks require time-based train/test splits (train on past, predict future) to avoid lookahead bias. Random splits are inappropriate for most financial tasks.

---

## Key References

- [TabArena: living benchmark](https://tabarena.ai)
- [TALENT benchmark suite](https://github.com/relevant-repo) — 200+ classification datasets
- TabPFN: Hollmann et al. (2023) — prior-fitted network for small tabular tasks
- TabICLv2: in-context learning for tabular prediction
- RealTabPFN-2.5: enhanced prior-fitted network
