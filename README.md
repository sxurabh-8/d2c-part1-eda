# Part 1 — Data Audit, EDA & Business Understanding

## Overview
End-to-end audit of the D2C customer churn dataset (2,400 customers, 10,009 orders, 1,921 support tickets).
Snapshot date: **2025-09-30**. Target window: next 60 days.

## Files
- `eda_audit.ipynb` — full data loading, joins, EDA, hypotheses
- `data_quality_report.md` — issues found and treatment recommendations
- `business_memo.md` — executive memo on what to investigate first
- `charts/` — 6+ generated charts and supporting tables
- `requirements.txt`

## How to Run
```bash
pip install -r requirements.txt
jupyter notebook eda_audit.ipynb
```
Place the provided dataset CSV files inside the data/ directory before running the notebook.

## Key Findings
- Overall 60-day churn rate: ~47% (1127 churned vs 1273 non-churned customers), indicating a near-balanced classification problem.
- Hibernating + At-Risk High-Value customers churn at **77–86%** — the obvious priority list.
- Customers with **0 sessions in the last 30 days** dominate the churn group.
- Loyalty-program members churn at roughly half the rate of non-members.
