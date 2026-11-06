# 🛒 TheLook E-Commerce — Customer Retention Analysis with RFM++ & K-Means Clustering

> Turning transactional data into actionable retention strategies through behavioral segmentation and cohort analysis.

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python&logoColor=white)
![BigQuery](https://img.shields.io/badge/BigQuery-Google%20Cloud-4285F4?logo=google-cloud&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-F7931E?logo=scikit-learn&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data-150458?logo=pandas&logoColor=white)

---

## 📌 Introduction

This project analyzes **customer retention patterns** for TheLook, a fictitious e-commerce platform (Google BigQuery public dataset), using machine learning to segment 80,000+ customers into actionable behavioral groups.

We go beyond traditional RFM analysis by engineering an **extended feature set (RFM++)** that captures browsing behavior, session depth, product diversity, and cart engagement — then apply **K-Means clustering** to reveal five distinct customer personas with dramatically different retention profiles.

**Dataset:** 100K users · 125K orders · 182K order items · 29K products · 2.43M events  
**Date Range:** Jan 2019 – Sep 2025  
**Source:** [Google BigQuery `thelook_ecommerce`](https://console.cloud.google.com/bigquery?p=bigquery-public-data&d=thelook_ecommerce)
**Data Visualization:** [Link](https://drive.google.com/file/d/1IfCcnGrwVjjvuasVg3X4bixQPRJC58mW/view?usp=sharing)

---

## 🔥 The Pain Point

E-commerce platforms face a universal challenge: **62% of customers never return after their first purchase**, and retention drops to ~3% after the first month, stabilizing below 2% long-term.

The traditional approach — treating all customers the same — wastes resources on users who will never convert again while under-investing in high-value segments. Key questions:

- Which customers are worth retaining, and which should be deprioritized?
- What behavioral signals predict repeat purchases vs. churn?
- How should retention strategies differ across customer segments?

---

## 💡 Core Idea

Instead of relying on classic 3-variable RFM (Recency, Frequency, Monetary), we propose **RFM++** — an extended segmentation framework that adds 5 behavioral features derived from web engagement data:

| Feature | Source | Description |
|---------|--------|-------------|
| **R** — Recency | orders | Days since last purchase |
| **F** — Frequency | orders | Total completed orders |
| **M** — Monetary | order_items | Total revenue generated |
| **V** — Visits | events | Total website sessions |
| **D** — Durations | events | Total time spent on platform |
| **A** — Actions | events | Total interactions (clicks, searches, page scrolls) |
| **P** — Diversity View | events | Number of distinct product categories browsed |
| **C** — Add-to-Cart Count | events | Products added to cart |

This enriched feature space allows K-Means to distinguish between customers who browse but don't buy, customers who buy but don't engage, and everything in between.

---

## ⚙️ Implementation

### 1. Data Preparation
- Queried 5 BigQuery tables, joined at user level
- Handled missing values (e.g., `shipped_at` ~33.4% null — expected for in-transit orders)
- Aggregated 2.43M events into per-user behavioral metrics
- Final dataset: **80,073 customers × 8 features**

### 2. Feature Engineering & EDA
- Distribution analysis with histograms + boxplots for all 8 features
- Identified right-skewed distributions across Monetary, Actions, and Durations
- Detected outliers via IQR method (retained — they represent "whale" customers)

### 3. Optimal Cluster Selection
- Evaluated k = 2–10 using three metrics:
  - **Elbow Method** (inertia) → knee at k=5
  - **Silhouette Score** → peak at k=2, plateau from k=5
  - **Calinski-Harabasz Index** → diminishing returns after k=5
- Selected **k=5** as the optimal trade-off between statistical fit and business interpretability

### 4. K-Means Clustering → 5 Customer Personas

| Cluster | Persona | Size | Key Traits |
|---------|---------|------|------------|
| **0** | 🏆 Champions (VIP) | 10.08% | Highest F (4.34), strong M, best retention (29.37% Q1), diverse product interest |
| **1** | 👻 Massive Newbies | 40.16% | All metrics ≈ 0, zero repeat purchase, zero web engagement — one-and-done |
| **2** | 🔍 High-Engaged Browsers | 10.59% | Highest Add-to-Cart (1.22) & Actions, but low F (0.88) — browsing without converting |
| **3** | 💤 Hibernating / Lost | 18.70% | Highest Recency (2.77), all other metrics near 0 — churned customers |
| **4** | 🔄 Loyal Survivors | 20.30% | Steady F (~1.8), moderate engagement, 2nd-best retention (18.02% Q1) |

### 5. Cluster-Level Retention Cohort Analysis
- Built **retention heatmaps per cluster** (purchase cohorts + visit cohorts)
- Cluster 0 (VIP): 29.37% return in Q1, still active after 5 years (~2% at 18 quarters)
- Cluster 1 (Newbies): 0.00% return rate — complete one-time buyers
- Cluster 2 (Browsers): 9.18% Q1 return, declines to <1% by Q13
- Cluster 4 (Loyal): 18.02% Q1, good first-year retention but drops faster than VIPs

### 6. Visit vs. Purchase Gap Analysis
- Cluster 2 stands out: high revisit rate but low purchase conversion
- → Opportunity: convert engaged browsers into buyers with targeted incentives

---

## 📊 Key Results

### Retention Findings
- **62% of all customers** are single-purchase (Cluster 1 + Cluster 3 = 58.86%)
- **Champions (10%)** drive disproportionate lifetime value — CLV of repeat customers ($181.74) is **2.8× higher** than one-time buyers ($64.21)
- **CLV growth is driven by frequency, not AOV** — repeat buyers spend similar amounts per order but buy more often
- **Add-to-cart behavior is the strongest retention predictor** — session actions matter more than session duration

### Strategic Recommendations

| Segment | Strategy | Tactics |
|---------|----------|---------|
| Champions | Maximize CLV | VVIP perks, cross-category bundles, early access, beta testing |
| Loyal Survivors | Sustain retention | Automated lifecycle emails, subscription models, replenishment reminders |
| High-Engaged Browsers | Convert to buyers | First-purchase vouchers, scarcity triggers, abandoned cart flows |
| Massive Newbies | Reduce acquisition waste | Audit ad targeting, exclude from retention campaigns to optimize ROI |
| Hibernating/Lost | Low-cost maintenance | Automated win-back emails only, analyze churn causes for remaining segments |

---

## 📁 Repository Structure

```
├── data/                    # Sample data files
├── notebooks/
│   ├── 01_EDA.ipynb         # Exploratory data analysis
│   ├── 02_RFM_Features.ipynb    # Feature engineering (RFM++)
│   ├── 03_Clustering.ipynb      # K-Means clustering & evaluation
│   └── 04_Retention_Cohort.ipynb    # Cohort retention by cluster
├── sql/
│   └── queries.sql          # BigQuery data extraction queries
├── visuals/                 # Charts and heatmaps
├── presentation.pdf         # Full project presentation (39 slides)
└── README.md
```

---

## 🛠️ Tech Stack

- **Data Warehouse:** Google BigQuery (public dataset)
- **Languages:** Python (Pandas, NumPy, scikit-learn, Matplotlib, Seaborn)
- **Visualization:** Power BI (dashboard), Matplotlib/Seaborn (EDA)
- **ML:** K-Means Clustering, Elbow/Silhouette/CH evaluation

---

