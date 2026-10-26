[github_repository_documentation.md](https://github.com/user-attachments/files/33975751/github_repository_documentation.md)
# 🛒 Retail Sales & Profitability Optimization (Superstore Analytics)

[![Power BI](https://img.shields.io/badge/Power_BI-F2C94C?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![SQL](https://img.shields.io/badge/SQL-PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)]()

An end-to-end analytics project performing data cleaning, exploratory data analysis, SQL querying, and interactive Power BI dashboarding to identify profit leaks and optimize promotional discounting strategies for a US national retailer.

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Business Problem](#-business-problem)
- [Dataset Architecture](#-dataset-architecture)
- [Tech Stack & Tools](#-tech-stack--tools)
- [Data Pipeline & Cleaning](#-data-pipeline--cleaning)
- [Key Findings & Business Insights](#-key-findings--business-insights)
- [Code & Query Snippets](#-code--query-snippets)
- [Power BI Dashboard Visuals](#-power-bi-dashboard-visuals)
- [Strategic Recommendations](#-strategic-recommendations)
- [Repository Structure](#-repository-structure)
- [How to Run & Reproduce](#-how-to-run--reproduce)

---

## 🎯 Project Overview

The objective of this project is to analyze over **10,000 transaction records** from Superstore to diagnose why strong top-line sales growth failed to deliver proportional bottom-line profitability. Through data cleaning, cohort profiling, SQL aggregation, and Power BI visualization, this project isolates negative-margin product lines, evaluates discounting thresholds, and delivers actionable business recommendations.

### Key Metrics Summary
* **Total Sales Revenue:** $2,297,201
* **Total Net Profit:** $286,397
* **Overall Net Margin:** ~12.5%
* **Total Orders Processed:** 5,009
* **Identified Annual Profit Leakage:** ~$21,300+ (from heavy discounting)

---

## 🚨 Business Problem

Despite achieving **$2.30M+ in top-line revenue**, Superstore's net profit margin remains suppressed at **~12.5%**. Executive leadership noted significant margin compression across specific product categories and geographic regions.

### Core Challenges:
1. **Uncontrolled Promotional Discounting:** Discounts as high as 70%–80% were applied without margin safety controls.
2. **Category Margin Disparities:** The *Technology* category achieves a healthy ~17.4% margin, whereas *Furniture* collapses to **2.4%** due to severe losses in sub-categories like **Tables (-$17,725 loss)** and **Bookcases (-$3,473 loss)**.
3. **Regional Performance Imbalances:** The **Central Region** produces the lowest profit margins (7.9%) despite strong overall order volumes.

---

## 📊 Dataset Architecture

The underlying dataset contains order-level transactions spanning nationwide retail operations across 4 US regions.

| Field Name | Data Type | Description |
| :--- | :--- | :--- |
| `Order ID` | String / Text | Unique identifier for each customer transaction |
| `Order Date` | Datetime (`YYYY-MM-DD`) | Date the order was placed by the customer |
| `Ship Date` | Datetime (`YYYY-MM-DD`) | Date the order was dispatched |
| `Ship Mode` | String / Categorical | Standard Class, Second Class, First Class, Same Day |
| `Segment` | String / Categorical | Customer demographic: Consumer, Corporate, Home Office |
| `Region` | String / Categorical | Geographic market: East, West, Central, South |
| `Category` | String / Categorical | Product high-level grouping: Furniture, Office Supplies, Technology |
| `Sub-Category` | String / Categorical | Detailed product classification (e.g., Tables, Chairs, Phones) |
| `Sales` | Decimal / Numeric | Gross monetary value of transaction ($ USD) |
| `Quantity` | Integer | Units purchased in the transaction |
| `Discount` | Decimal / Percentage | Promotional discount percentage applied (0.0 to 0.8) |
| `Profit` | Decimal / Numeric | Net financial outcome after accounting for cost & discount ($ USD) |

---

## 🛠 Tech Stack & Tools

* **Data Processing & Wrangling:** Python 3.x (`pandas`, `numpy`)
* **Database & Analytical Queries:** SQL (`SQLite` / `PostgreSQL`)
* **Data Visualization & Dashboards:** Power BI Desktop (DAX, Data Modeling, Dynamic Slicers)
* **Presentation & Documentation:** Microsoft PowerPoint, Markdown

---

## 🧹 Data Pipeline & Cleaning

The raw dataset (`superstore.csv`) underwent structured data preprocessing in Python prior to relational modeling:

1. **Schema Validation & Type Conversion:**
   * Parsed `Order Date` and `Ship Date` into standardized ISO `YYYY-MM-DD` datetime formats.
   * Computed shipping latency: `Shipping Days = Ship Date - Order Date`.

2. **Missing Data Handling:**
   * Imputed missing `Postal Code` entries using state and city lookup mappings.

3. **Deduplication & Integrity:**
   * Validated unique constraints across composite keys (`Order ID` + `Product ID`).
   * Removed exact record duplicates and standardized whitespace/casing across categorical columns.

4. **Feature Engineering:**
   * `Profit Margin (%)` = `(Profit / Sales) * 100`
   * `Discount Bin` = Categorized into `No Discount (0%)`, `Low (0-15%)`, `Moderate (15-35%)`, `High (>35%)`.

---

## 💡 Key Findings & Business Insights

1. **The 20% Discount Tipping Point:**
   * Sales with zero or low discounts (<15%) yield an average net margin exceeding **18%**.
   * When promotional discounts exceed **20%**, the average transaction profit turns negative. Discounts $\ge$ 50% result in an average net loss of **-$85 per transaction**.

2. **Furniture Category Drag:**
   * High gross revenue ($742K) hides severe sub-category losses.
   * **Tables** generate -$17,725 in net loss on $206K sales due to an average discount rate of **26%**.
   * **Bookcases** contribute -$3,473 in net loss with an average discount of **21%**.

3. **Regional Sensitivity:**
   * **Central Region** is impacted worst by promotional discounting, driving margins down to **7.9%** compared to the **West Region's 14.8%**.

---

## 💻 Code & Query Snippets

### SQL: Identifying Unprofitable Sub-Categories
```sql
SELECT 
    category,
    sub_category,
    COUNT(order_id) AS total_orders,
    ROUND(AVG(discount), 2) AS avg_discount_rate,
    ROUND(SUM(sales), 2) AS total_sales,
    ROUND(SUM(profit), 2) AS total_profit
FROM superstore_cleaned
GROUP BY category, sub_category
HAVING SUM(profit) < 0
ORDER BY total_profit ASC;
```

### Python: Statistical Correlation Profiling
```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# Load preprocessed dataset
df = pd.read_csv('data/superstore_cleaned.csv')

# Calculate correlation matrix across key metrics
corr_matrix = df[['sales', 'quantity', 'discount', 'profit']].corr()

print("Correlation Matrix:")
print(corr_matrix)

# Sub-category loss profiling
loss_analysis = (
    df.groupby('sub_category')
    .agg(
        total_sales=('sales', 'sum'),
        total_profit=('profit', 'sum'),
        mean_discount=('discount', 'mean')
    )
    .query('total_profit < 0')
    .sort_values(by='total_profit', ascending=True)
)

print("\nLoss-Making Sub-Categories:")
print(loss_analysis)
```

---

## 🖼 Power BI Dashboard Visuals

The interactive Power BI report includes:
* **KPI Header Cards:** Total Sales, Total Profit, Profit Margin %, Total Orders.
* **Category & Sub-Category Performance Bar Charts:** Dynamic breakdown highlighting negative profit bars in red.
* **Discount vs. Profit Margin Scatter Plot:** Visual proof of margin erosion past the 20% discount threshold.
* **Geographic Map Slicer:** Regional and state-level drill-down functionality.

> *(Note: Place your exported dashboard screenshot in your repository's `/images` folder as `dashboard_screenshot.png` to render automatically below)*

![Power BI Dashboard Screenshot](images/dashboard_screenshot.png)

---

## 🚀 Strategic Recommendations

1. **Enforce Hard Discount Caps:**
   * Implement a strict governance rule limiting promotional discounts to a **maximum of 15%** on vulnerable lines like *Tables* and *Bookcases*. This single operational change is projected to recover **$20,000+ annually**.

2. **Catalog & Supplier Restructuring:**
   * Renegotiate vendor freight/manufacturing costs for the *Furniture* segment.
   * Bundle low-margin *Tables* exclusively with high-margin *Technology accessories* (e.g., monitor mounts, high-end chairs) to ensure cart-level profitability.

3. **Regional Pricing Governance:**
   * Restructure localized discount permissions in the **Central Region**, aligning sales team commission incentives to *Net Profit Generated* rather than gross revenue.

---

## 📁 Repository Structure

```text
├── data/
│   ├── superstore_raw.csv           # Original uncleaned dataset
│   └── superstore_cleaned.csv       # Preprocessed and engineered dataset
├── notebooks/
│   └── data_cleaning_analysis.ipynb # Python Jupyter Notebook (Pandas EDA & Profiling)
├── sql/
│   └── analytical_queries.sql       # SQL scripts for aggregations and profit profiling
├── dashboard/
│   └── Superstore_Analytics.pbix    # Power BI Dashboard source file
├── presentation/
│   └── Superstore_Analytics_Presentation.pptx # 6-Slide Executive Deck
├── images/
│   └── dashboard_screenshot.png     # Visual screenshot for documentation
├── README.md                        # Project documentation (this file)
└── LICENSE                          # Open-source license (MIT)
```

---

## ⚙️ How to Run & Reproduce

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/your-username/retail-sales-profitability-optimization.git
   cd retail-sales-profitability-optimization
   ```

2. **Set Up Python Environment:**
   ```bash
   pip install pandas numpy seaborn matplotlib
   ```

3. **Run Data Preprocessing:**
   * Open and execute `notebooks/data_cleaning_analysis.ipynb` to regenerate `superstore_cleaned.csv`.

4. **Execute SQL Queries:**
   * Import `data/superstore_cleaned.csv` into your preferred SQL environment (SQLite / PostgreSQL / MySQL) and run `sql/analytical_queries.sql`.

5. **Open Dashboard:**
   * Open `dashboard/Superstore_Analytics.pbix` in Power BI Desktop to interact with the visualizations.

---

## 🤝 Author & Acknowledgments
* **Author:** Your Name
* **LinkedIn:** [Your LinkedIn Profile](https://linkedin.com)
* **Portfolio:** [Your Portfolio Link](https://yourportfolio.com)
