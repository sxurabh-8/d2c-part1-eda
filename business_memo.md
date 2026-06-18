# Business Memo — Pre-Campaign Investigation

**To:** Marketing, Product, Customer Support leadership
**From:** Data Science
**Re:** What we should check **before** spending the retention budget

## Headline
- 47% of customers in our sample are projected to churn in the next 60 days. That number is too high to discount our way out of.
- Two segments alone (**Hibernating** and **At-Risk High Value**) account for **897 customers** and churn at **77–86%** — that is where the budget should land first.

## Five Things to Investigate Before Any Campaign

1. **Why are paid-acquisition cohorts churning faster than Organic?**
   Customers acquired through **Marketplace** and **Google Search** show meaningfully higher churn than Organic/Referral. If we increase paid spend before fixing this, we just buy more churners. Pull a CAC-to-90-day-retained-revenue ratio by channel before any spend decision.

2. **The "loyalty program" is the strongest retention lever we already own.**
   Loyalty-tier members churn at roughly half the rate of non-members. Yet **58% of customers are not enrolled**. Before discounting, ask: why aren't we auto-enrolling at signup? A free intervention here likely beats a paid one.

3. **Support pain is concentrated and predictive.**
   Customers with **2+ tickets in 90 days** and **negative sentiment** show clearly elevated churn. Examples: CUST01067, CUST00756, CUST01056 — all "High-Value Unhappy" with negative sentiment rate ≥ 0.5. Audit `refund_delay` and `late_delivery` tickets (the two highest-volume issue types) before launching a campaign — fixing the operational cause is cheaper than buying back the customer.

4. **30-day web/app inactivity is a near-deterministic churn signal.**
   Customers with `sessions_30d == 0` churn at very high rates. A free re-engagement push (email/notification) should be tested *before* a discount. We need to confirm whether these users opted out of comms (~marketing_consent = No) — if so, a discount won't reach them anyway.

5. **Discount fatigue check.**
   "Discount-Sensitive" customers (high `avg_discount`, multiple orders) still churn at ~42%. Discounting harder may not move that number — confirm price-elasticity with a small A/B before scaling any blanket promo.

## Recommended Sequence
1. **Week 1 — Diagnose:** quantify CAC payback by channel; pull root-cause on `refund_delay` and `late_delivery` tickets.
2. **Week 2 — Free interventions:** auto-enroll non-loyalty customers; trigger re-engagement notifications to `sessions_30d=0` cohort.
3. **Week 3+ — Paid interventions:** focus the retention budget on **At-Risk High Value** (highest LTV × highest churn probability). Avoid blanket discounts to Champions — they are not at risk and discounting them only erodes margin.

## What Success Looks Like
- 60-day churn rate for treated cohorts ≥ 8 pp lower than control.
- No measurable change in Champions' AOV (no cannibalization).
- Reduction in `refund_delay` ticket volume.
