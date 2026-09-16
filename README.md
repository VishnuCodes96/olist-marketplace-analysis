# Olist Marketplace Performance & Customer Experience Analysis

> An end-to-end exploratory data analysis of the Olist Brazilian e-commerce marketplace, focused on marketplace performance, delivery reliability, customer satisfaction, geography, product categories, payment behavior, and potential contributors to low customer reviews.

## Project Overview

The **Brazilian E-Commerce Public Dataset by Olist** contains information on nearly 100,000 orders placed on the Olist marketplace between 2016 and 2018, covering customers, orders, sellers, products, payments, freight, and customer reviews.

This project goes beyond basic sales analysis to examine the relationship between **operational performance and customer experience**.

The central business question is:

> **What marketplace and operational factors are associated with customer satisfaction, and which factors appear most strongly connected to poor reviews?**

The analysis was performed as an end-to-end Python data analytics workflow, from data loading and cleaning through metric creation, exploratory analysis, business-question analysis, root-cause analysis, and recommendations.

---

## Business Questions

The project investigates five major areas:

1. **Delivery Performance & Customer Satisfaction**
   - How does delivery timing relative to the estimated delivery date relate to review scores?
   - Does the relationship vary across product categories and regions?

2. **Seller & Geographic Patterns**
   - How does the geographic relationship between sellers and customers relate to delivery performance, freight cost, and customer satisfaction?

3. **Product Category Performance**
   - Which categories generate the most order volume and revenue?
   - How do category prices and customer satisfaction compare with the marketplace benchmark?

4. **Payment Behavior**
   - How do payment methods and installment choices relate to order value?
   - Are payment patterns meaningfully associated with customer satisfaction?

5. **Root-Cause Analysis**
   - Which factors are most strongly associated with low customer review scores?
   - Can delivery, freight, geography, and order value help explain dissatisfaction?

---

## Dataset

The project uses the following Olist datasets:

| Dataset | Purpose |
|---|---|
| `olist_customers_dataset.csv` | Customer IDs, locations, cities, and states |
| `olist_orders_dataset.csv` | Order status and order lifecycle timestamps |
| `olist_order_items_dataset.csv` | Products, sellers, prices, and freight at item level |
| `olist_products_dataset.csv` | Product categories and product attributes |
| `olist_order_payments_dataset.csv` | Payment methods, payment values, and installments |
| `olist_order_reviews_dataset.csv` | Customer review scores |
| `olist_geolocation_dataset.csv` | Brazilian geographic information |
| `olist_sellers_dataset.csv` | Seller IDs and seller locations |
| `product_category_name_translation.csv` | Portuguese-to-English product category translation |

### Data scale

- **99,441 orders**
- **96,478 delivered orders**
- Data period: **2016–2018**
- Average marketplace review score: **4.09 / 5**

---

## Tools & Technologies

- **Python**
- **Pandas** — data manipulation and analysis
- **NumPy** — numerical operations
- **Matplotlib** — visualization
- **Seaborn** — statistical visualization
- **Plotly Express** — interactive visualization
- **Google Colab / Jupyter Notebook**

---

## Analytical Workflow

The project follows an end-to-end analytical workflow:

```text
Raw Olist Datasets
        ↓
Data Understanding & Quality Checks
        ↓
Missing-Value & Duplicate Analysis
        ↓
Date Conversion & Data Preparation
        ↓
Order-Level Analytical Dataset
        ↓
Feature / Metric Creation
        ↓
Business Question Analysis
        ↓
Root-Cause Analysis
        ↓
Business Insights & Recommendations
```

### 1. Data Understanding & Cleaning

The analysis begins by inspecting:

- Dataset dimensions and data types
- Missing values
- Order-status distributions
- Duplicate records
- ID uniqueness
- Missing delivery information
- Product-level missing values
- Multiple records per order

Missing values were handled according to their context rather than blindly dropping records. For example, missing product categories were represented as `Unknown`, while numerical product attributes were imputed using their median values.

Non-delivered orders were retained in the original dataset and filtered only when a specific analysis required delivered orders.

### 2. Creating an Order-Level Analytical Dataset

Several Olist tables operate at different granularities.

For example:

- Orders → order level
- Order items → item level
- Payments → potentially multiple records per order
- Reviews → review level

To prevent duplicated orders during analysis, the project constructs order-level summaries where appropriate.

Important derived metrics include:

- Delivery delay
- Delivery status
- Delay severity
- Review score
- Low-review indicator
- Representative product category
- State-level delivery performance
- Seller-to-customer state routes
- Order-level freight value
- Total order payment value
- Installment groups

### 3. Delivery Analysis

Delivery delay is calculated relative to the estimated delivery date.

The project then examines:

- Early/on-time vs late deliveries
- Delay severity
- Review scores by delivery status
- Delivery-delay correlation with review score
- Delivery performance by category
- Delivery performance by customer state

### 4. Geographic Analysis

The analysis uses **customer state** as the primary regional variable and also constructs seller-to-customer state routes.

This allows comparison of:

- Same-state vs different-state fulfillment
- Late-delivery rates
- Freight costs
- Average review scores
- Low-review rates
- High-risk routes

### 5. Product Category Analysis

Product categories are evaluated across:

- Item/order volume
- Revenue
- Average product price
- Average review score
- Performance relative to the marketplace review benchmark

For order-level customer-satisfaction analysis, a **representative category** is assigned to each order using the category of its highest-priced item. This avoids duplicating a single order's review when an order contains multiple items.

### 6. Payment Analysis

Payment records are aggregated to the order level because some orders contain multiple payment records.

The analysis examines:

- Payment methods
- Total order payment value
- Multiple payment types
- Installment counts
- Installment groups
- Order value by payment behavior
- Review score by payment behavior

Installments are grouped into:

- `1 installment`
- `2–6 installments`
- `7+ installments`

This reduces noise from installment counts with small sample sizes.

### 7. Root-Cause Analysis

A **low review** is defined as a review score of **≤ 2**.

The analysis compares low-review rates across:

- Late vs early/on-time deliveries
- Product categories
- Freight-cost groups
- Same-state vs different-state orders
- Order-value groups
- Payment behavior

The objective is to identify **observed contributors**, not to claim a single causal root cause.

---

# Key Findings

## 1. Delivery Performance Has the Strongest Observed Relationship with Customer Satisfaction

Among reviewed delivered orders:

- Early/on-time orders averaged approximately **4.29 / 5**
- Late orders averaged approximately **2.27 / 5**
- Delivery delay vs review score correlation: **r = -0.267**

Customer satisfaction also declined as delivery lateness increased:

| Delivery condition | Average review |
|---|---:|
| Early / On Time | 4.29 |
| 1–3 days late | 3.29 |
| 4+ days late | ~1.6–2.1 |

The analysis also found:

- **9.23%** of early/on-time orders received low reviews
- **62.36%** of late orders received low reviews

This relationship remained visible across multiple product categories rather than being isolated to a single category.

### Important nuance

Late delivery does **not** explain every poor review.

Approximately **67.5% of low-review orders were still delivered early or on time**.

Therefore, delivery lateness is strongly associated with dissatisfaction, but other aspects of the customer experience must also contribute.

---

## 2. Geography Is Associated with Delivery Risk and Customer Experience

Different-state orders showed:

| Metric | Same-state | Different-state |
|---|---:|---:|
| Late-delivery rate | 4.46% | 7.89% |
| Average freight | 15.43 | 26.88 |
| Average review | 4.26 | 4.10 |
| Low-review rate | 10.42% | 14.09% |

State-level analysis also showed a strong negative relationship between late-delivery rate and average review score:

- All analyzed states: **r = -0.881**
- States with at least 500 reviewed delivered orders: **r = -0.912**

Several routes, particularly routes originating from São Paulo and serving northeastern destinations, showed elevated late-delivery rates.

### Interpretation

Geographic separation is associated with higher freight costs, higher late-delivery rates, and lower satisfaction.

However, the analysis does **not** establish that geographic distance itself causes dissatisfaction. Geography may partly affect customer experience through logistics and delivery performance.

---

## 3. Product Category Performance Is Not Explained by Revenue Alone

Product categories varied substantially across volume, revenue, price, and customer satisfaction.

Examples:

- **Bed, Bath & Table**
  - Highest item volume: **11,115**
  - Revenue: approximately **1.04M**
  - Average review: **3.98**
  - Below the marketplace benchmark of 4.09

- **Health & Beauty**
  - High volume: **9,670**
  - Revenue: approximately **1.26M**
  - Average review: **4.18**

- **Watches & Gifts**
  - Item volume: **5,991**
  - Revenue: approximately **1.21M**
  - Average price: **201.14**
  - Demonstrates how higher average selling price can generate high revenue without the highest volume

- **Office Furniture**
  - 1,260 reviewed orders
  - Average review: **3.62**
  - Below the marketplace benchmark

- **Books — General Interest**
  - Average review: approximately **4.47**
  - 505 reviewed orders

- **Luggage & Accessories**
  - Average review: approximately **4.33**
  - 1,023 reviewed orders

### Key takeaway

High sales volume or revenue does not necessarily correspond to high customer satisfaction.

Some commercially important categories combine strong demand with below-average reviews and therefore warrant further investigation.

---

## 4. Payment Behavior Is More Closely Associated with Order Value Than Satisfaction

Payment analysis showed a strong relationship between installment usage and order value:

| Installment group | Average order value |
|---|---:|
| 1 installment | 121.04 |
| 2–6 installments | 155.45 |
| 7+ installments | 337.16 |

Average order values by major payment group included:

| Payment group | Average order value |
|---|---:|
| Credit card | 166.95 |
| Boleto | 145.03 |
| Debit card | 142.72 |
| Voucher | 114.39 |
| Credit card + voucher | 150.88 |

However, installment count had almost no linear relationship with review score:

**Installments vs review score: r = -0.031**

Average review scores across major payment groups also showed relatively modest differences.

### Key takeaway

Payment behavior appears considerably more useful for understanding **purchase value and transaction behavior** than for explaining customer satisfaction.

---

# Root-Cause Analysis

The root-cause analysis treats the results as **associations rather than proof of causation**.

### Primary observed contributor: Delivery performance

Evidence:

- Late orders: **62.36% low-review rate**
- Early/on-time orders: **9.23% low-review rate**
- Delivery delay vs review score: **r = -0.267**

The relationship also appeared across multiple product categories.

### Secondary observed contributors

#### Freight cost

- Low-freight group: **9.06% low-review rate**
- High-freight group: **18.16% low-review rate**
- Average review decreased from **4.32 to 3.94**

Freight is also related to geography and delivery characteristics, so its independent effect is not established.

#### Geographic separation

- Same-state low-review rate: **10.42%**
- Different-state low-review rate: **14.09%**

Different-state orders also had higher late-delivery rates, meaning geography and delivery performance are interconnected.

#### Order value

- Low-value orders: **10.34% low-review rate**
- High-value orders: **15.86% low-review rate**

Higher order value was therefore associated with a higher low-review rate in this analysis.

### Overall interpretation

Customer dissatisfaction appears to be **multi-factorial**.

Delivery lateness showed the strongest observed association with poor reviews, while freight cost, geographic separation, order value, and other product/service factors may also contribute.

---

# Business Recommendations

Based on the analysis:

1. **Prioritize reducing late deliveries**
   - Focus on routes and product categories with consistently elevated late-delivery rates.

2. **Investigate high-risk shipping routes**
   - Examine whether seller location, carrier performance, logistics planning, or geographic separation contributes to delays.

3. **Review high-freight orders**
   - Identify routes where shipping costs are unusually high and investigate opportunities for logistics optimization.

4. **Monitor commercially important categories with weaker satisfaction**
   - Pay particular attention to high-volume/high-revenue categories with below-average reviews.

5. **Investigate low reviews among on-time deliveries**
   - Since most low-review orders were not late, additional factors such as product quality, seller service, order accuracy, and customer expectations should be investigated.

6. **Use customer reviews as an ongoing feedback signal**
   - Identify recurring dissatisfaction patterns and use them to prioritize operational improvements.

---

# Limitations

The analysis has several important limitations:

- The analysis identifies **associations, not causation**.
- Delivery delay, freight cost, geography, and order value may interact with one another.
- Geographic analysis is performed at the **state level**, rather than using actual physical or road distance.
- Some product categories and groups have relatively small sample sizes and therefore require cautious interpretation.
- For order-level category analysis, the category of the **highest-priced item** is used as the representative category when an order contains multiple categories.
- Category commercial metrics such as item count, revenue, and average price are calculated at the item level, while customer satisfaction is analyzed using the representative order-level category.
- Some orders do not have review records, so satisfaction analyses use only orders with available review scores.

---

# Repository Structure

A simple repository structure for this project is:

```text
Olist-Marketplace-Performance-Customer-Experience-Analysis/
│
├── Olist_Marketplace_Performance_Customer_Experience_Analysis.ipynb
├── README.md
│
├── olist_customers_dataset.csv
├── olist_orders_dataset.csv
├── olist_order_items_dataset.csv
├── olist_products_dataset.csv
├── olist_order_payments_dataset.csv
├── olist_order_reviews_dataset.csv
├── olist_geolocation_dataset.csv
├── olist_sellers_dataset.csv
└── product_category_name_translation.csv
```

If the datasets are not stored in the repository, place the CSV files in the notebook's working directory before running the notebook.

---

# How to Run

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd Olist-Marketplace-Performance-Customer-Experience-Analysis
```

### 2. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn plotly jupyter
```

### 3. Place the Olist CSV datasets in the working directory

The notebook expects the CSV filenames listed in the **Dataset** section.

### 4. Launch Jupyter Notebook

and run the notebook from top to bottom.

---

# Skills Demonstrated

This project demonstrates practical data-analytics skills including:

- Data loading and inspection
- Data-quality assessment
- Missing-value handling
- Duplicate and uniqueness checks
- Data cleaning
- Datetime transformation
- Multi-table data integration
- Order-level aggregation
- Feature engineering
- Exploratory Data Analysis (EDA)
- Descriptive statistics
- Correlation analysis
- Group-based analysis
- Business KPI analysis
- Customer satisfaction analysis
- Geographic analysis
- Category performance analysis
- Payment behavior analysis
- Root-cause analysis
- Data visualization
- Business recommendations
- Analytical reasoning and interpretation

---

# Final Takeaway

The Olist marketplace shows strong overall delivery performance and generally positive customer reviews, but the analysis reveals a clear relationship between **delivery reliability and customer satisfaction**.

Late deliveries had a substantially higher low-review rate than early/on-time deliveries, and the relationship was visible across multiple product categories and geographic segments.

At the same time, delivery lateness does not explain all dissatisfaction. Freight costs, geographic separation, order value, and potentially product or seller-related factors also show associations with low reviews.

The broader lesson from the analysis is:

> **Marketplace growth and transaction volume alone do not provide a complete picture of marketplace health. Operational reliability and customer experience need to be analyzed together.**
