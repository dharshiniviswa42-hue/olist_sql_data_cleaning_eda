# 🛒 Olist Brazilian E-Commerce — SQL Data Analysis

An end-to-end SQL analysis of the **Olist Brazilian E-Commerce** dataset, covering data cleaning, exploratory data analysis (EDA), and business recommendations derived purely from verified SQL query outputs.

---

## 📌 Project Overview

Olist is a Brazilian e-commerce marketplace that connects small businesses to customers across Brazil. This project analyzes order, payment, delivery, and review data across multiple linked tables to uncover revenue trends, payment behavior, and delivery performance — and translates those findings into actionable business recommendations.

## 🎯 Objective

To analyze Olist's order and payment data to:
- Measure overall business scale (orders & revenue)
- Track year-over-year and month-over-month sales trends
- Identify the most preferred payment methods and their revenue contribution
- Identify top revenue-generating product categories
- Evaluate order fulfillment health and delivery performance
- Recommend data-driven strategies to improve revenue and customer retention

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| **MySQL** | Data cleaning & SQL querying |
| **Workbench / Jupyter (SQLAlchemy)** | Database connection & query execution |
| **9 relational tables** | orders, order_items, order_payments, order_reviews, products, customers, sellers, geolocation, category_translation |

## 📂 Dataset

The dataset contains 9 interlinked CSV tables sourced from Olist's public Brazilian e-commerce dataset, covering ~100K orders placed between 2016–2018, including order status, payment, freight performance, and customer reviews.

## 📁 Project Structure

```
olist-ecommerce-sql-analysis/
│
├── olist_project.sql   -- data cleaning + all 7 EDA business questions
└── README.md           -- project overview, insights & recommendations
```

---

## 🧹 Data Cleaning

Full queries in [`olist_project.sql`](./olist_project.sql) (Part 1). Summary of steps:

1. **Data type correction** — converted timestamp columns from text to proper `DATETIME` across `orders` and `order_items`.
2. **Null handling** — replaced missing `product_category_name` with `'unknown'`, and filled missing product attribute fields with `0`.
3. **Column rename** — fixed typos in source column names (`product_name_lenght` → `product_name_length`, etc.).

> ⚠️ **Why NULL date columns were intentionally left untouched:** One of the main goals of this project is to calculate **Delivery Latency / Lead Time** — the time from order placement to actual delivery, using `DATEDIFF()`. If fake/placeholder dates were inserted into these columns instead of NULL, the time-delta calculations would be completely skewed, corrupting the average shipping performance reports. Preserving the NULL state in delivery date columns is essential to represent real-world events accurately.
>
> As a result, **654 "delivered" records** were excluded from the delivery-performance analysis (Q7) due to missing delivery timestamps — done to preserve data integrity for logistics tracking.

---

## 📊 Business Questions & Insights

Full queries in [`olist_project.sql`](./olist_project.sql) (Part 2).

### Q1. What is the total revenue generated and total orders placed?

| total_orders | total_revenue (R$) |
|---|---|
| 99,440 | 16,008,872.12 |

**Insight:** The business processed **99,440 unique orders**, generating a total gross revenue of approximately **R$16 million** — giving a clear high-level view of business scale before diving into deeper trends.

---

### Q2. How has revenue and order volume changed year-over-year?

| order_year | total_orders | total_revenue (R$) |
|---|---|---|
| 2016 | 328 | 59,362.34 |
| 2017 | 45,101 | 7,249,746.73 |
| 2018 | 54,011 | 8,699,763.05 |

**Insight:** The business shows a **strong upward growth trend from 2016 to 2018**.
- 2016 was the **launch year**, with very minimal transactions (just 328 orders).
- In 2017, the business experienced a **massive jump**, reaching 45,101 orders and ~R$7.2M in revenue.
- Growth continued into 2018, with orders rising to 54,011 and revenue hitting ~R$8.7M.

This proves that the company's customer base and market presence expanded rapidly year-over-year.

---

### Q3. How does the sales trend change month-over-month, and what seasonal patterns emerge?

**Key patterns found:**

1. **Business launch in 2016** — operations started only in the last few months of the year, so order volumes were extremely low (Sep: 3 orders, Oct: 324 orders, Dec: 1 order).
2. **Peak month — November 2017** — orders hit the highest peak of **7,544 orders** (R$1,194,882.80), driven by **Black Friday discounts and seasonality**.
3. **Post-festival drop in December 2017** — orders naturally declined to 5,673 once Black Friday offers ended. This isn't a business loss; it's a **normal post-festival drop**, and December's 5,673 orders were still well above pre-festival months like September/October.
4. **Steady growth through 2018** — monthly orders stayed strong (6,000–7,200/month) from January through August 2018.

> 📝 **Data Limitation Note:** Order volume crashes to near-zero from September 2018 onward (16 orders in Sep, 4 in Oct). This is a **known limitation of the public Olist dataset** — data collection stops around Sep/Oct 2018 — and does **not** reflect a real decline in business performance.

---

### Q4. Which payment methods do customers prefer, and how much revenue does each contribute?

| payment_type | total_orders | total_revenue (R$) |
|---|---|---|
| credit_card | 76,505 | 12,542,084.19 |
| boleto | 19,784 | 2,869,361.27 |
| voucher | 3,866 | 379,436.87 |
| debit_card | 1,528 | 217,989.79 |
| not_defined | 3 | 0.00 |

**Objective:** Analyze the most preferred payment methods used by Olist customers and understand their revenue contribution — helping the business understand customer spending behavior and optimize banking partnerships.

**Insight — three clear trends:**
- 💳 **Credit card is king** — the dominant payment method, driving over **76,505 orders** and contributing a massive **R$12.54 million** in revenue (78% of total revenue).
- 🧾 **Boleto is second** — a significant number of customers (19,784 orders) prefer Boleto (local cash voucher), bringing in R$2.87 million.
- 🪙 **Vouchers & debit cards are low adoption** — vouchers (3,866 orders) and debit cards (1,528 orders) have very low adoption on the platform.
- 🔍 A negligible **3 orders** have an undefined payment type with R$0 revenue — a minor data quality edge case, not material to the analysis.

---

### Q5. Which product categories drive the most revenue?

| product_category_name | total_orders | total_revenue (R$) |
|---|---|---|
| beleza_saude (Beauty & Health) | 8,836 | 1,441,248.07 |
| relogios_presentes (Watches & Gifts) | 5,624 | 1,305,541.61 |
| cama_mesa_banho (Bed & Bath) | 9,417 | 1,241,681.72 |
| esporte_lazer (Sports & Leisure) | 7,720 | 1,156,656.48 |
| informatica_acessorios (Computer Accessories) | 6,689 | 1,059,272.40 |

**Insight:** **beleza_saude** generates the highest revenue despite fewer orders than cama_mesa_banho — indicating a **higher revenue-per-order** (less price-sensitive, higher organic demand). **cama_mesa_banho**, in contrast, has the highest order volume but lower revenue-per-order — consistent with it being a high-competition, lower-margin essentials category. This directly powers the category-based financing strategy recommended below.

---

### Q6. What is the overall order fulfillment breakdown (e-commerce success rate)?

| order_status | total_orders |
|---|---|
| delivered | 96,478 |
| shipped | 1,107 |
| canceled | 625 |
| unavailable | 609 |
| invoiced | 314 |
| processing | 301 |
| created | 5 |
| approved | 2 |

**Insight:** Out of 99,440 total orders, **96,478 (97%) were successfully delivered** — only a small fraction were canceled, unavailable, or stuck in earlier stages of the pipeline. This reflects a **healthy, well-functioning order fulfillment pipeline**.

---

### Q7. How does delivery performance affect customer satisfaction?

> *Specifically: what percentage of orders are delivered before the estimated date, and how do their review scores compare to delayed orders?*

| delivery_status | total_orders | avg_review_score |
|---|---|---|
| on_time | 88,163 | 4.29 |
| delayed | 7,661 | 2.57 |

**Core Insight:** Out of all successfully delivered orders with valid tracking timestamps, a staggering **92% (88,163 orders)** were delivered on time, while only **8% (7,661 orders)** experienced delays — highlighting a highly efficient baseline logistics operation.

**The "Why" Factor:** Shipping delays severely damage customer sentiment. On-time deliveries enjoy a high average rating of **4.29/5.0**, indicating strong customer retention. In contrast, delayed deliveries cause the rating to **crash drastically to 2.57/5.0** — proving that improving logistics directly impacts review scores and customer retention.

---

## 💡 Business Recommendations

### 1. Product-Category Based Financing Strategy
As a data analyst, I recommend a **product-category based financing strategy** to balance both sales volume and profit margins:

- **Low-interest EMI for high-demand products** — Categories like *beleza_saude* (Beauty & Health) have very high organic demand and revenue-per-order. Offering a low-interest EMI keeps customers buying (since they already want the product) while letting Olist protect its profit margins.
- **0% interest EMI for high-competition essentials** — Categories like *cama_mesa_banho* (Bed & Bath) are heavy essentials with high order volume but high competition and lower margins. A 0% interest (no-cost) EMI acts as a powerful psychological trigger, reducing the perceived financial burden and pushing customers toward instant bulk purchases.

### 2. Fix the Delivery Bottleneck
To improve the overall customer rating, the company must focus heavily on the **8% delayed-order bottleneck**:
- Implement **automated alerts** for shipments nearing their delivery deadline.
- **Optimize carrier routes** in high-delay regions.

Directly improving these logistics will improve customer loyalty and push average review scores higher across the platform.

---

## 🔑 Key Takeaways

- Olist generated **~R$16M in gross revenue** across **99,440 orders** (2016–2018), with strong YoY growth.
- **97% order fulfillment rate** — a healthy, well-functioning order pipeline.
- **Credit card** is the dominant payment method (78% of revenue), followed by **Boleto**.
- Sales are **seasonal** — major spikes around festival periods like Black Friday (Nov 2017).
- **beleza_saude** and **cama_mesa_banho** are the top two revenue categories, with different demand/margin profiles — powering a tailored EMI financing strategy.
- **92% on-time delivery rate**, but the remaining 8% of delayed orders cause customer ratings to crash from 4.29 → 2.57 — making logistics the single biggest lever for improving customer satisfaction.

---

## 📫 Connect with Me

- **LinkedIn:** [linkedin.com/in/viswa-dharshini](https://www.linkedin.com/in/viswa-dharshini)
- **GitHub:** [github.com/dharshiniviswa42-hue](https://github.com/dharshiniviswa42-hue)
