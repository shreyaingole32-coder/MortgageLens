# MortgageLens: Problem Definition & Analytical Framework

## 1. Executive Problem Statement

In the mortgage lending industry, financial institutions, portfolio managers, risk analysts, and pricing teams face a constant challenge: identifying emerging portfolio risks, pricing discrepancies, and geographic shifts in mortgage application outcomes across dynamic economic environments. 

Public Home Mortgage Disclosure Act (HMDA) data provides a comprehensive, multi-institution view of the US residential mortgage market. However, raw HMDA data is massive, complex, and unaggregated. Without a structured analytical framework, decision-makers cannot easily detect segment-level deviations, pricing anomalies, or lender concentration shifts.

**MortgageLens** addresses this challenge by serving as an end-to-end **Data Analytics & Decision-Support System**. The primary objective of MortgageLens is to answer the central business question:

> **"Where should a portfolio or risk analyst investigate further, and what empirical evidence supports that investigation?"**

MortgageLens is **NOT** an automated credit underwriting or loan decisioning tool, nor does it attempt predictive default modeling. Instead, it systematically transforms observational HMDA data into actionable insights through descriptive statistics, diagnostic SQL queries, rigorous hypothesis testing, and an explainable anomaly monitoring framework.

---

## 2. Stakeholders & Analytical Needs

MortgageLens supports six distinct stakeholder groups across mortgage operations, risk management, and strategic planning:

| Stakeholder Group | Core Analytical Need | Primary Business Questions | Key Output / Artifact |
| :--- | :--- | :--- | :--- |
| **Risk Analyst** | Detecting unusual denial rate spikes or underwriting shifts across geographic markets. | *"Which core statistical areas (MSAs) or counties show unexpected denial rate surges relative to historical baselines?"* | Segment anomaly flags & diagnostic drill-downs |
| **Portfolio Analyst** | Analyzing portfolio composition, loan purpose distribution, and product mix shifts. | *"How is portfolio volume shifting between conventional, FHA, and VA loans, or single-family vs. multi-family properties?"* | Volume breakdowns & concentration indices |
| **Pricing Analyst** | Evaluating interest rate spreads, rate distributions, and pricing variances across loan segments. | *"Are certain borrower or geographic segments experiencing higher pricing spreads relative to market baselines?"* | Pricing distribution plots & regression models |
| **Business Manager** | Monitoring market share, origination growth, and lender competition. | *"Which lenders dominate specific regional markets, and how is market concentration changing YoY?"* | Concentration share & HHI metrics |
| **Compliance / Risk Team** | Auditing portfolio outcome disparities and preparing objective empirical baseline reporting. | *"Are observed outcome differences across segments statistically significant after controlling for loan characteristics?"* | Hypothesis testing & statistical reports |
| **Senior Management** | Macro-level executive summaries, portfolio health checks, and strategic market expansion guidance. | *"What are the top 5 regional markets requiring deeper operational review this quarter?"* | Executive Dashboard & Summary KPIs |

---

## 3. Analytical Pipeline Architecture

MortgageLens structures analysis into a six-stage progressive pipeline:

```
[ Data Layer ] ──> [ Descriptive ] ──> [ Diagnostic ] ──> [ Statistical ] ──> [ Monitoring ] ──> [ Decision Support ]
```

1. **Data Layer**: Clean, standardized HMDA application records, validated types, and structured PostgreSQL storage.
2. **Descriptive Analytics**: Core summary metrics (origination volumes, median loan amounts, denial rates, baseline pricing).
3. **Diagnostic Analytics**: SQL CTEs, window functions (`LAG`, `RANK`), and segmentation matrices to uncover *where* deviations occur.
4. **Statistical Analytics**: 95% confidence intervals, Chi-Square/t-tests for outcome comparison, and interpretable linear regression for pricing spread determinants.
5. **Monitoring Framework**: Robust z-score and historical baseline deviation tracking to auto-generate plain-English attention flags.
6. **Decision Support**: Interactive Streamlit dashboard enabling analysts to filter by state, lender, loan purpose, and property type to guide targeted manual investigations.

---

## 4. Business Questions

MortgageLens is designed to answer 15 explicit business questions:

### Portfolio & Origination Trends
1. What is the overall origination volume, application volume, and approval rate across the selected timeframe?
2. How do origination rates and loan volumes vary across different loan purposes (Home Purchase, Refinancing, Home Improvement)?
3. What is the distribution of loan amounts across different property types (e.g., Single-Family, Manufactured, Multi-Family)?

### Denial & Outcome Analytics
4. What is the overall denial rate, and how does denial rate vary by geographic region (State/County/MSA)?
5. Which loan purposes exhibit the highest denial rates, and how have these rates shifted YoY?
6. Are denial rates statistically significantly different across loan types or property types?

### Pricing Intelligence
7. What is the distribution of interest rates and rate spreads across originated loans?
8. How does pricing (rate spread/interest rate) differ between conventional loans and government-backed loans (FHA/VA/USDA)?
9. What loan characteristics (e.g., loan amount, occupancy type, loan purpose) show the strongest statistical association with rate spreads?

### Lender Concentration & Geography
10. Which top lenders account for the majority of origination volume in target geographic markets?
11. How concentrated is the mortgage lending market in specific states or MSAs (measured via Herfindahl-Hirschman Index / HHI)?
12. What geographic sub-markets exhibit extreme shifts in lender concentration or origination share YoY?

### Deviation & Risk Monitoring
13. Which geographic or product segments show statistically significant deviations in denial rates compared to their multi-year historical baselines?
14. Where are interest rate spreads triggering attention flags due to unusual segment pricing variances?
15. What specific empirical evidence (sample size, baseline metric, z-score, confidence interval) supports prioritizing a segment for analyst investigation?

---

## 5. Conceptual KPI Framework

All metrics will be calculated strictly from verified fields once the HMDA dataset schema is ingested.

| Candidate KPI | Conceptual Calculation | Analytical Grain | Expected Field Category | Business Meaning & Caveats |
| :--- | :--- | :--- | :--- | :--- |
| **Origination Volume** | `COUNT(applications WHERE action_taken == Originated)` | Overall, State, Lender, Product | Action Taken, Date/Year | Total successful loan volume. Does not reflect dollar magnitude. |
| **Origination Rate** | `Originations / Total Applications` | Overall, State, Segment | Action Taken | Conversion rate of applications to closed loans. Affected by withdrawal rates. |
| **Denial Rate** | `Denied Applications / (Originated + Denied Applications)` | State, County, Lender, Product | Action Taken | Key portfolio risk metric. Excludes withdrawn/incomplete applications. |
| **Median Loan Amount** | `MEDIAN(loan_amount)` | Geographic, Product, Lender | Loan Amount | Central tendency measure robust to high-dollar loan outliers. |
| **Average Loan Amount** | `MEAN(loan_amount)` | Geographic, Product, Lender | Loan Amount | Parametric central tendency; sensitive to luxury property outliers. |
| **Mean / Median Rate Spread** | `AVG(rate_spread)` or `MEDIAN(rate_spread)` | Product, Lender, Geography | Rate Spread / Interest Rate | Metric indicating premium charged over benchmark rate. Only applies to originated loans with reported spread. |
| **Geographic Market Share** | `Lender Originations in MSA / Total MSA Originations` | Lender × MSA | Lender ID, MSA/County | Measures competitive footprint of a lender in a local market. |
| **Herfindahl-Hirschman Index (HHI)** | `SUM((Lender Market Share * 100)^2)` | County / MSA / State | Lender ID, Geography | Standard financial metric for market concentration (<1500 competitive, >2500 highly concentrated). |
| **YoY Volume / Rate Change** | `(Metric_t - Metric_{t-1}) / Metric_{t-1}` | Multi-Year Segment | Year, Action Taken, Geography | Tracks growth or contraction across consecutive annual reporting periods. |
| **Historical Deviation (z-score)** | `(Segment_Metric - Baseline_Mean) / Baseline_StdDev` | Segment × Year | Multi-year Aggregates | Statistical indicator of unusual deviation from historical performance. |

---

## 6. Project Scope

### In-Scope
* **Data Engineering**: Standardized ingestion of public HMDA data, schema normalization, PostgreSQL database storage, and indexing.
* **SQL Analytics**: Multi-stage CTEs, window functions (`LAG`, `RANK`, `DENSE_RANK`), aggregate rollups, and HHI calculations.
* **Exploratory Data Analysis (EDA)**: Distribution analysis, correlation matrices, boxplots, heatmaps, and spatial aggregations using Pandas, Matplotlib, and Seaborn.
* **Statistical Rigor**: 95% confidence intervals, hypothesis testing (Chi-Square test of independence, two-sample t-tests), and interpretable Ordinary Least Squares (OLS) / Logistic regression for association analysis.
* **Explainable Monitoring Framework**: Rule-based and robust statistical deviation detection flagging unusual segment anomalies for analyst review.
* **Decision Support Interface**: Multi-page interactive Streamlit dashboard featuring filterable views, pricing charts, and anomaly inspection cards.

### Out-of-Scope
* Predictive credit underwriting or default risk modeling (e.g., predicting individual borrower default probability).
* Machine learning algorithms (e.g., XGBoost, Random Forests, Neural Networks).
* Large Language Models (LLMs), Generative AI, or automated decisioning agents.
* Real-time streaming infrastructure, Kafka, Docker, Kubernetes, or microservices architecture.
* Causal inference claims (e.g., asserting that lender policy *caused* denial rate disparities without unobserved underwriting variables).

---

## 7. Success Criteria & Quality Bar

1. **Empirical Correctness & Reproducibility**: 100% of reported numbers match output from executable Python/SQL scripts. All scripts run reproducibly from `config.yaml`.
2. **SQL & Analytical Depth**: Complex analytical SQL utilizing window functions and subqueries with full documentation of business intent for every query.
3. **Statistical Validity**: Statistical tests must explicitly report $H_0$, $H_1$, test statistics, p-values, degrees of freedom, effect sizes (e.g., Cohen's d, Cramér's V), and assumption verifications.
4. **Interview Defensibility**: Every metric, threshold, and SQL query can be clearly explained by a B.Tech CSE student without reliance on hand-waving or black-box jargon.
5. **Clear Ethical & Causal Boundaries**: Strict adherence to observational data language (associations, deviations, patterns; never causal claims or unverified accusations).

---

## 8. Public Data Limitations & Constraints

HMDA data is a powerful public disclosure dataset, but it possesses key inherent limitations that must be acknowledged in every analytical report:

1. **Lack of Full Credit File / Underwriting Depth**: Public HMDA records do not contain exact borrower FICO scores, detailed debt-to-income (DTI) ratios, exact loan-to-value (LTV) ratios, asset reserves, or employment verification histories.
2. **Observational Data Constraint**: HMDA data reflects macro-level lending outcomes, not controlled experiments. Observed disparities in denial rates or pricing spreads between segments cannot be interpreted as proof of unlawful discrimination or lender intent.
3. **Annual Granularity**: HMDA is reported annually by financial institutions. Monthly or seasonal trend analysis cannot be performed without synthetic assumption (which is explicitly prohibited in this project).
4. **Data Redaction & Public Masking**: Certain sensitive fields (such as exact age ranges, credit score buckets, or specific rate spreads below reporting thresholds) are masked or binned in public HMDA releases to preserve borrower privacy.
5. **Post-Origination Performance**: HMDA tracks loan *applications* and *originations* at point-of-sale. It contains zero information regarding post-origination loan performance, delinquencies, defaults, or prepayments.
