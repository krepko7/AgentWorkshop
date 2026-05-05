# Fabric Data Agent Instructions

## Overview

This data agent provides insights into a SaaS subscription analytics platform. The data model follows a star schema with one fact table tracking subscription revenue and usage metrics, and five dimension tables providing context about customers, products, plans, regions, and dates.

---

## Table Descriptions

### fact_subscriptions (Fact Table)
The central fact table containing subscription transaction records. Each row represents a subscription instance for a customer, capturing revenue, usage metrics, and status information.

| Column | Description |
|--------|-------------|
| subscription_id | Unique identifier for each subscription record |
| customer_id | Foreign key to dim_customer |
| product_id | Foreign key to dim_product |
| plan_id | Foreign key to dim_subscription_plan |
| region_id | Foreign key to dim_region |
| date_id | Foreign key to dim_date (subscription start month) |
| revenue_amount | Total revenue for the subscription period (USD equivalent) |
| seats_purchased | Number of user seats purchased |
| seats_active | Number of seats actively being used |
| api_calls_used | Number of API calls consumed in the period |
| storage_used_gb | Storage consumed in gigabytes |
| subscription_status | Current status: Active or Cancelled |
| mrr | Monthly Recurring Revenue in USD |
| arr | Annual Recurring Revenue in USD |
| is_expansion | Whether this subscription is an expansion from an existing customer (Yes/No) |
| is_churned | Whether this subscription has churned (Yes/No) |
| nps_score | Net Promoter Score from customer (1-10) |
| support_tickets_opened | Number of support tickets opened during the period |

### dim_customer (Dimension Table)
Customer master data including company details and segmentation.

| Column | Description |
|--------|-------------|
| customer_id | Unique customer identifier |
| customer_name | Primary contact name |
| company_name | Company or organization name |
| industry | Industry vertical (Technology, Healthcare, Finance, etc.) |
| company_size | Company size category: Small, Medium, or Large |
| country | Country of headquarters |
| state | State or province |
| city | City of headquarters |
| signup_date | Date the customer first signed up |
| customer_segment | Business segment: SMB, Mid-Market, or Enterprise |

### dim_product (Dimension Table)
Product catalog with pricing and feature details.

| Column | Description |
|--------|-------------|
| product_id | Unique product identifier |
| product_name | Full product name |
| product_category | Product family: Analytics, Integration, Security, or AI & Automation |
| product_tier | Pricing tier: Basic, Professional, or Enterprise |
| monthly_price | Monthly subscription price in USD |
| annual_price | Annual subscription price in USD |
| max_users | Maximum allowed users (or "Unlimited") |
| storage_gb | Included storage in GB |
| api_calls_monthly | Monthly API call allowance |
| support_level | Support tier: Email, Email & Chat, or 24/7 Priority |

### dim_subscription_plan (Dimension Table)
Subscription plan terms and conditions.

| Column | Description |
|--------|-------------|
| plan_id | Unique plan identifier |
| plan_name | Descriptive plan name |
| billing_frequency | How often billing occurs: Monthly or Annual |
| contract_length_months | Contract duration in months |
| discount_percentage | Discount applied vs monthly pricing |
| auto_renewal | Whether the plan auto-renews: Yes or No |
| cancellation_policy | Terms for cancellation |
| trial_days | Number of free trial days included |

### dim_region (Dimension Table)
Geographic and sales territory information.

| Column | Description |
|--------|-------------|
| region_id | Unique region identifier |
| region_name | Top-level region: North America, Europe, Asia Pacific, Latin America, Middle East & Africa |
| sub_region | Sub-region within the top-level region |
| country | Country name |
| currency | Local currency code |
| timezone | Primary timezone |
| sales_territory | Internal sales territory code |
| regional_manager | Name of the assigned regional sales manager |

### dim_date (Dimension Table)
Calendar dimension for time-based analysis.

| Column | Description |
|--------|-------------|
| date_id | Date key in YYYYMMDD format |
| full_date | Full date in YYYY-MM-DD format |
| year | Calendar year |
| quarter | Calendar quarter (Q1-Q4) |
| month | Month number (1-12) |
| month_name | Full month name |
| day_of_month | Day of the month |
| day_of_week | Day of the week (1=Monday, 7=Sunday) |
| day_name | Full day name |
| is_weekend | Whether the date falls on a weekend: Yes or No |
| fiscal_year | Microsoft fiscal year (July-June) |
| fiscal_quarter | Fiscal quarter (FQ1-FQ4) |

---

## Relationships

- `fact_subscriptions.customer_id` → `dim_customer.customer_id`
- `fact_subscriptions.product_id` → `dim_product.product_id`
- `fact_subscriptions.plan_id` → `dim_subscription_plan.plan_id`
- `fact_subscriptions.region_id` → `dim_region.region_id`
- `fact_subscriptions.date_id` → `dim_date.date_id`

---

## Sample Questions to Test the Data Agent

### Revenue & Financial Analysis
1. "What is the total revenue across all subscriptions?"
2. "Which product category generates the most revenue?"
3. "Show me the monthly recurring revenue (MRR) trend over time."
4. "What is the average ARR by customer segment?"
5. "Which region contributes the most revenue?"

### Customer Analytics
6. "How many customers do we have in each industry?"
7. "Which enterprise customers have the highest revenue?"
8. "What is the average NPS score by customer segment?"
9. "List all customers who have churned and their associated revenue loss."
10. "Which customers have expansion subscriptions?"

### Product Performance
11. "Which product tier is most popular by number of subscriptions?"
12. "What is the average seat utilization rate across all products?" (seats_active / seats_purchased)
13. "Which products have the highest API call consumption?"
14. "Compare revenue between Analytics and Security product categories."
15. "Which Enterprise-tier products generate the most revenue?"

### Churn & Retention
16. "What is our overall churn rate?"
17. "Is there a correlation between NPS scores and churn?"
18. "Which subscription plans have the highest churn?"
19. "What is the average number of support tickets for churned vs. active customers?"
20. "Which product categories experience the most churn?"

### Geographic Analysis
21. "Break down revenue by region."
22. "Which sales territory has the most customers?"
23. "What is the average deal size in Europe vs. North America?"
24. "Which regional manager oversees the highest revenue territory?"

### Usage & Engagement
25. "What is the average storage utilization across all subscriptions?"
26. "Which customers are exceeding their API call allowance?"
27. "What is the seat utilization rate by company size?"
28. "Are there any customers with very low seat utilization that might be at risk of churning?"
