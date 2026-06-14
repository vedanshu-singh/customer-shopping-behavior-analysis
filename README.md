# 🛒 Customer Shopping Behavior Analysis

> An end-to-end data analytics project exploring purchasing trends, customer segmentation, product performance, and revenue drivers across 3,900 retail transactions.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat-square&logo=mysql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-2ecc71?style=flat-square)

---

## 📌 Overview

Retail organizations collect vast volumes of transaction data, but deriving strategic value from it requires structured analysis. This project combines **Python**, **MySQL**, and **Power BI** to analyze customer shopping behavior and generate actionable business insights.

The analysis covers:
- Revenue breakdown by category, gender, and age group
- Customer segmentation (New / Returning / Loyal)
- Subscription vs. non-subscription spending patterns
- Discount dependency by product
- Shipping type vs. average spend
- Top-rated products by review score

---

## 📊 Key Metrics

| Metric | Value |
|--------|-------|
| Total Customers | 3,900 |
| Total Revenue | $233,000 |
| Avg. Purchase Amount | $59.76 |
| Avg. Review Rating | 3.75 / 5.0 |
| Product Categories | 4 |
| Customer Locations | 50 US States |

---

## 🔍 Key Findings

- 🏆 **Clothing** is the top revenue category — generating **~$104K (45%)** of total revenue
- 👨 **Male customers** contribute **~68%** of total revenue
- 🎯 **Young Adults** are the highest-value demographic at **$62.14K** in total revenue
- 🔁 **Loyal customers** (10+ purchases) represent the largest segment — **~2,300 customers**
- 📉 Only **27% of customers** are subscribers, despite comparable spending to non-subscribers
- 🚀 Customers using **premium shipping** (2-Day, Express) tend to spend more per transaction

---

## 🗂️ Project Structure

```
customer-shopping-behavior-analysis/
│
├── data/
│   └── customer_shopping_data.csv        # Raw dataset (3,900 records)
│
├── notebooks/
│   └── EDA_analysis.ipynb                # Exploratory Data Analysis
│
├── sql/
│   └── business_queries.sql              # 10 SQL business questions
│
├── dashboard/
│   └── Customer_Shopping_Dashboard.pbix  # Power BI dashboard file
│
├── report/
│   └── Customer_Shopping_Behavior_Report.docx  # Stakeholder report
│
└── README.md
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **Python** (Pandas, NumPy) | Data cleaning, EDA, feature engineering |
| **MySQL + SQLAlchemy** | Database integration, business querying |
| **Power BI** | Interactive dashboard & visualization |
| **Jupyter Notebook** | Exploratory analysis environment |

---

## ⚙️ Data Preparation

The raw dataset required several preprocessing steps before analysis:

1. **Missing Value Treatment** — 37 missing `review_rating` values filled using **category-level median** imputation
2. **Column Standardization** — All column names converted to `snake_case` for consistency across Python and SQL
3. **Age Group Engineering** — Customers segmented into: `Young Adult`, `Adult`, `Middle-aged`, `Senior`
4. **Frequency Encoding** — Purchase frequency categories (Weekly, Monthly, etc.) mapped to numeric day values
5. **Redundancy Removal** — `promo_code_used` dropped (identical to `discount_applied` across all records)

---

## 🗃️ SQL Business Analysis

Ten business questions were answered using SQL. Below are selected examples:

<details>
<summary><strong>Q1 — Revenue by Gender</strong></summary>

```sql
SELECT gender, SUM(purchase_amount) AS revenue
FROM customer
GROUP BY gender;
```
</details>

<details>
<summary><strong>Q3 — Top 5 Products by Avg Review Rating</strong></summary>

```sql
SELECT item_purchased,
       ROUND(AVG(review_rating::numeric), 2) AS "Average Product Rating"
FROM customer
GROUP BY item_purchased
ORDER BY AVG(review_rating) DESC
LIMIT 5;
```
</details>

<details>
<summary><strong>Q7 — Customer Segmentation (New / Returning / Loyal)</strong></summary>

```sql
WITH customer_type AS (
  SELECT customer_id, previous_purchases,
    CASE
      WHEN previous_purchases = 1 THEN 'New'
      WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
      ELSE 'Loyal'
    END AS customer_segment
  FROM customer
)
SELECT customer_segment, COUNT(*) AS "Number of Customers"
FROM customer_type
GROUP BY customer_segment;
```
</details>

<details>
<summary><strong>Q8 — Top 3 Products per Category (Window Function)</strong></summary>

```sql
WITH item_counts AS (
  SELECT category, item_purchased,
         COUNT(customer_id) AS total_orders,
         ROW_NUMBER() OVER (PARTITION BY category ORDER BY COUNT(customer_id) DESC) AS item_rank
  FROM customer
  GROUP BY category, item_purchased
)
SELECT item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
```
</details>

---

## 📈 Power BI Dashboard

The interactive dashboard includes:

- **KPI Cards** — Total Revenue, Total Customers, Avg Purchase Amount, Avg Review Rating
- **Revenue by Category** — Bar chart comparing all 4 categories
- **Revenue by Gender** — Donut chart with percentage split
- **Revenue by Age Group** — Horizontal bar chart across 4 demographic segments
- **Customer Segmentation** — New / Returning / Loyal breakdown
- **Subscription Status** — Donut showing 27% subscriber rate
- **Shipping Type vs Avg Spend** — Comparative bar chart

**Interactive Filters:** Gender · Category · Season · Subscription Status

---

## 💡 Business Recommendations

| # | Recommendation |
|---|---------------|
| 1 | **Increase Subscription Adoption** — Target frequent buyers (5+ purchases) with exclusive perks |
| 2 | **Strengthen Loyalty Programs** — Tiered rewards for Loyal segment customers |
| 3 | **Focus on Young Adults & Female Customers** — Highest-growth demographic opportunities |
| 4 | **Optimize Discount Strategy** — Replace blanket discounts with value-added promotions |
| 5 | **Amplify Top-Rated Products** — Prioritize Gloves, Sandals, Boots in marketing campaigns |

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install pandas numpy sqlalchemy mysql-connector-python jupyter
```

### Run the EDA Notebook

```bash
jupyter notebook notebooks/EDA_analysis.ipynb
```

### Set Up the Database

```sql
CREATE DATABASE customer_behavior;
USE customer_behavior;
-- Then run the data load script and business_queries.sql
```

---

## 📁 Dataset

The dataset contains **3,900 records** and **18 features** covering:

- Customer demographics (Age, Gender, Location)
- Purchase details (Item, Category, Amount, Season, Size, Color)
- Behavioral attributes (Review Rating, Shipping Type, Discount Applied, Subscription Status, Payment Method, Purchase Frequency)

> **Note:** Dataset sourced from a public retail transactions dataset. Suitable for educational and portfolio use.

---

## 🧠 Skills Demonstrated

`Data Cleaning` · `Exploratory Data Analysis` · `Feature Engineering` · `SQL Querying` · `Window Functions` · `CTEs` · `Customer Segmentation` · `Business Intelligence` · `Dashboard Development` · `Data Visualization` · `Stakeholder Reporting`

---

## 📄 License

This project is for portfolio and educational purposes. Feel free to fork, reference, or build upon it.

---

<p align="center">Made with 📊 Python · MySQL · Power BI</p>
