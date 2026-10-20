# Yelwa and Sons Holdings Ltd. – Business Analytics Project

A data analysis and dashboard project built on **synthetic (simulated) data** for a fictional 66-employee, multi-subsidiary Nigerian group. Period: Oct 2025 – Sep 2026. Amounts in Nigerian Naira (₦).

## Contents
| Folder | What it holds |
|---|---|
| `data/` | Six CSV files plus `data_dictionary.csv` (column definitions) |
| `dashboard/index.html` | Interactive management dashboard (Chart.js, self-contained apart from the CDN script) |
| `scripts/` | `dash.py` (cleaning and aggregation) and `tpl.html` (dashboard template) |

## Data model
- `employees` (66) → `workflow_tasks` via `employee_id = assigned_to`
- `customers` (130) → `transactions` via `customer_id = counterparty_id` (revenue rows only)
- `employees` → `transactions` via `employee_id = recorded_by`
- `business_overview_monthly`: pre-aggregated, one row per month per subsidiary

## Cleaning rules
1. Trim text fields and parse dates.
2. Check duplicate IDs, month/date consistency, non-positive amounts and orphan keys (all passed).
3. Only `status = Completed` transactions count toward revenue and profit (101 Failed/Reversed/Pending rows excluded).
4. Reconciled recomputed revenue against `business_overview_monthly` (difference = 0).

## Key findings
- Agro earns the most and is seasonal: strong at harvest (Oct–Dec), loss-making at planting (Apr–May).
- Fintech grows from a monthly loss to a profit.
- Logistics, Properties and Tech launch later in the period.
- HQ has no sales revenue; the two grants only partly offset its costs.

## Using the dashboard
Open `dashboard/index.html` in a browser. Filter by subsidiary chip, by month range, or click a bar in "Profit by subsidiary" to cross-filter every chart.

## Power BI
Load the CSVs from `data/`, build the relationships above, and recreate the visuals. Suggested measures:
```
Revenue = CALCULATE(SUM(transactions[amount_ngn]), transactions[transaction_type]="Revenue", transactions[status]="Completed")
Expenses = CALCULATE(SUM(transactions[amount_ngn]), transactions[transaction_type]="Expense", transactions[status]="Completed")
Net Profit = [Revenue] - [Expenses]
Profit Margin % = DIVIDE([Net Profit], [Revenue])
Task Completion % = DIVIDE(COUNTROWS(FILTER(workflow_tasks, workflow_tasks[status]="Completed")), COUNTROWS(workflow_tasks))
```
Create a Date table and relate it to `transactions[date]` for time-intelligence measures.

## Disclaimer
All people, companies and figures are fictional and for learning purposes only.
