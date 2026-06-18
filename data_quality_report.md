# Data Quality Report

Snapshot date: **2025-09-30**. Files inspected: customers, orders, support_tickets, churn_labels, intervention_history, rfm_modeling_snapshot.

## 1. Missing Values
| File | Column | Nulls | Treatment |
|---|---|---|---|
| customers | `loyalty_tier` | ~1,386 (58%) | Encode as `"None"` — null means "not enrolled", not missing. |
| customers | `skin_type` | ~401 (17%) | Encode as `"Unknown"`. Possible useful signal — flag as `skin_type_provided` boolean. |
| orders | `rating` | sparse (e.g., ORD000014) | Leave NaN; use `avg_rating` only where present. |
| support_tickets | `resolution_hours` | some `1` values look like floors / placeholder | Treat 1.0 as suspect; cap at 99th percentile elsewhere. |

## 2. Duplicates / Key Issues
- No duplicate `customer_id` in `customers.csv` (2,400 unique).
- `orders.csv` `order_id` is unique (10,009 rows).
- `support_tickets.customer_id`, `intervention_history.customer_id` all link cleanly to `customers`.
- `intervention_history` has exactly 1 row per customer (most-recent campaign only) — by design.

## 3. Date Consistency / Leakage Risk ⚠️
- `orders.csv` contains rows **after the 2025-09-30 snapshot** (e.g., ORD008129 dated 2025-10-24). The data dictionary states these are label-generation rows and **must NOT be used as features**.
- All features built from `orders` must filter `order_date <= 2025-09-30`.
- `web_events_snapshot` is already snapshot-aligned (30-day window ending 2025-09-30).
- `rfm_modeling_snapshot.csv`appears to be snapshot-aligned and suitable for modeling, subject to verification of feature-generation logic.; safe to use directly for modeling.

## 4. Outliers
- `orders.gross_amount`: long right tail; 99th percentile ≈ ₹2,800. Cap or log-transform for monetary aggregations.
- `support_tickets.resolution_hours`: many rows at exactly `1.0` (likely a placeholder for sub-hour resolution); separately a long tail >72h on `refund_delay` tickets.
- `rfm_snap.recency_days` reaches >280 for fully dormant accounts — keep but bucket.

## 5. Invalid / Unusual Values
- `customers.skin_type` literally contains `"NA"` strings in addition to true NaN — normalize.
- `intervention_history.last_campaign_received = 'none'` for ~25% of customers — keep as its own category, do not treat as missing.
- `orders.discount_pct` capped at [0, 1] — clean.
- `tickets.sentiment_score` in [-1, 1] — clean.

## 6. Join Health
| Join | Match Rate | Notes |
|---|---|---|
| customers ↔ churn_labels | 100% | 2,400 ↔ 2,400. |
| customers ↔ orders | 99% have ≥1 order; ~15% have 0 pre-snapshot orders. | These zero-order customers tend to be high-churn. |
| customers ↔ support_tickets | ~50% have at least one ticket | Missing = no contact, encode as 0. |
| customers ↔ intervention_history | 100% | One row per customer. |

## 7. Recommended Treatments
1. Always filter `orders.order_date <= 2025-09-30` before feature engineering.
2. Encode null `loyalty_tier` as `"None"` (semantic, not missing).
3. Encode null `skin_type` as `"Unknown"` + add `skin_type_provided` boolean.
4. Cap monetary at 99th percentile or log-transform.
5. Treat `resolution_hours==1.0` as a separate "fast-resolve" indicator rather than a numeric outlier.
6. Use the provided `split` column (train/validation/test) consistently across all parts.
