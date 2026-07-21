# Coffee Shop Sales Analysis

End-to-end sales analytics project: data cleaning, feature engineering, star-schema data modeling, exploratory data analysis, hypothesis testing, and a Power BI dashboard — built on transaction-level coffee shop sales data across three NYC locations.

## Project Overview

This project answers real business questions about a coffee shop chain's sales performance using statistically validated methods rather than relying on visual inspection alone. It covers the full pipeline from raw transaction data to a business-ready insights report and interactive dashboard.

**Dataset:** Coffee Shop Sales transaction data (Jan 1 – Jun 19, 2023)
**Locations:** Astoria, Hell's Kitchen, Lower Manhattan
**Grain:** One row = one product sold in one transaction



## Data Source

(https://mavenanalytics.io/data-playground/coffee-shop-sales)


## How to Run

1. Clone the repository
2. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn scipy scikit-posthocs holidays openpyxl jupyter
   ```
3. Add the dataset to `data/Coffee Shop Sales.xlsx`
4. Run the notebook top to bottom:
   ```bash
   jupyter notebook sales_analysis.ipynb
   ```
5. Cleaned data and star-schema CSVs are written to `output/` — these can be imported directly into Power BI.

## Analysis Pipeline

1. **Data Cleaning** — whitespace stripping, dtype correction, date/time parsing
2. **Feature Engineering** — `total_amount`, `is_holiday` (via the `holidays` library, not a hardcoded list),
   `hour_bin`, `price_tier` (quartile-based, since price is right-skewed), `size` (regex-extracted from
   product names, with genuinely sizeless items correctly labeled rather than left as "Unknown")
3. **Star Schema Design** — one fact table (`fact_sales`) and four dimension tables
   (`dim_date`, `dim_time`, `dim_store`, `dim_product`), exported as CSVs for Power BI
4. **EDA** — univariate and bivariate analysis of revenue drivers by category, time, and store
5. **Hypothesis Testing** — six tests using non-parametric methods (Mann-Whitney U, Kruskal-Wallis with
   Dunn's post-hoc, Chi-square), chosen because `total_amount` is non-normally distributed
6. **Dashboard** — an interactive Power BI report built on the star schema (not included in this repo;
   see the insights report for a summary of what it surfaces)

## Key Findings

| Question | Finding | Statistically significant? | Practically meaningful? |
|---|---|---|---|
| Does spending differ on weekends? | No | No | — |
| Do all three stores perform the same? | Lower Manhattan has larger basket sizes (not pricing) | Yes | **Yes** |
| Do holidays affect spending? | ~$0.15 lower on average, same median | Yes | No |
| Does spending differ by time of day? | No — but transaction *volume* varies hugely | No | — |
| Is size tied to product category? | Yes — only drink categories have size variants | Yes | Structural, not behavioral |
| Does product mix differ by store? | Modest skew toward Bakery/Flavours at Lower Manhattan | Yes | Modest |

**Two actionable takeaways:**
- **Lower Manhattan** generates comparable revenue to the other stores with *fewer* transactions, driven by
  customers buying more items per visit — not different pricing or product mix. Worth investigating what
  drives this and testing whether it can be replicated at the other locations.
- **Morning** is the peak revenue window purely due to transaction *volume* (85,865 transactions vs. 4,594
  during Dawn/Early Morning) — not higher spending per visit. Growth in slower periods should focus on
  driving foot traffic, not on increasing basket size.

Full details, evidence, and recommended actions for each finding are in
[`docs/Coffee_Shop_Sales_Insights_Report.docx`](docs/Coffee_Shop_Sales_Insights_Report.docx).

## Methods Used

- **Mann-Whitney U test** — comparing two independent groups on a non-normal numeric variable
- **Kruskal-Wallis test** — comparing three or more independent groups on a non-normal numeric variable
- **Dunn's post-hoc test (Bonferroni-corrected)** — pairwise follow-up after a significant Kruskal-Wallis result
- **Chi-square test of independence** — testing association between two categorical variables

Non-parametric tests were chosen throughout because `total_amount` is heavily right-skewed with real
high-value outliers, violating the normality assumption behind t-tests and ANOVA.

## Tech Stack

- **Python** — pandas, numpy, matplotlib, seaborn, scipy, scikit-posthocs, holidays
- **Power BI** — star-schema data model, DAX measures, interactive dashboard
- **Jupyter Notebook** — analysis and documentation

## License

This project is for educational/portfolio purposes. Dataset structure based on the publicly available Coffee Shop Sales dataset.
