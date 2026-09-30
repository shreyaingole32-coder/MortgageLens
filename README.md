# MortgageLens: Mortgage Lending Portfolio & Pricing Intelligence

MortgageLens is an end-to-end data analytics and decision-support project built using public Home Mortgage Disclosure Act (HMDA) mortgage loan application data. 

The primary business question answered by this project is:
> **"Where should a portfolio or risk analyst investigate further, and what empirical evidence supports that investigation?"**

## Project Architecture & Directory Structure

```text
MortgageLens/
├── README.md
├── requirements.txt
├── config.yaml
├── .gitignore
│
├── data/
│   ├── raw/
│   └── processed/
│
├── database/
│   ├── schema.sql
│   ├── staging.sql
│   └── analytical_queries.sql
│
├── notebooks/
│   ├── 01_data_validation.ipynb
│   ├── 02_eda.ipynb
│   ├── 03_statistical_analysis.ipynb
│   └── 04_risk_monitoring.ipynb
│
├── src/
│   ├── data_cleaning.py
│   ├── feature_engineering.py
│   ├── statistics.py
│   └── monitoring.py
│
├── dashboard/
├── visualizations/
├── tests/
└── docs/
    ├── data_dictionary.md
    ├── methodology.md
    ├── limitations.md
    └── problem_definition.md
```

## Status

- **Milestone 1**: Problem Definition & Framework Setup — [COMPLETE]
- **Milestone 2**: Dataset Acquisition — [PENDING APPROVAL]
