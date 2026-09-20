
#  ProfitGuard — Profitability Analytics & Anomaly Detection

> **Identifying where revenue is generated but profit is lost.**

ProfitGuard is an end-to-end **Data Analytics and Data Science project** that analyzes transaction-level sales data to identify loss-making transactions, detect unusual profitability patterns, investigate potential profit leaks, and present the findings through an interactive Power BI dashboard.

The project combines **Python, Pandas, NumPy, Scikit-learn, anomaly detection, business analysis, and Power BI** to move from raw transactional data to actionable profitability insights.

---

##  Business Problem

A company can generate substantial sales while still losing money on specific transactions.

A traditional sales dashboard can show:

* Total sales
* Total profit
* Profit margin
* Product performance

But it may not immediately reveal:

* Which transactions are destroying profit?
* Which transactions are unusual compared with the rest?
* Where are potential losses concentrated?
* Which discount levels are associated with negative profitability?
* Which product × discount combinations deserve investigation?
* Which regions and states contain a large share of potential losses?

**ProfitGuard was built to investigate these questions.**

---

# 🔄 Project Workflow

```text
Raw Transaction Data
        ↓
Data Cleaning & Validation
        ↓
Exploratory Data Analysis
        ↓
Profitability Metrics
        ↓
Discount & Loss Analysis
        ↓
Anomaly Detection
        ↓
Potential Profit-Leak Candidates
        ↓
Leak Explanation
        ↓
Root Cause Analysis
        ↓
Business Priority Analysis
        ↓
Power BI Dashboard
```

---

# 🛠️ Technology Stack

### Programming & Data Analysis

* Python
* Pandas
* NumPy
* Jupyter Notebook

### Machine Learning

* Scikit-learn
* Isolation Forest
* Unsupervised Anomaly Detection

### Business Intelligence

* Microsoft Power BI
* DAX
* Power Query

### Analytical Techniques

* Exploratory Data Analysis
* Feature Engineering
* Profit Margin Analysis
* Discount Band Analysis
* Anomaly Detection
* Root Cause Analysis
* Business Priority Scoring
* Data Visualization

---

# 📁 Dataset

The project uses the **Global Superstore** transaction dataset.

Available fields include:

```text
Ship Mode
Segment
Country
City
State
Postal Code
Region
Category
Sub-Category
Sales
Quantity
Discount
Profit
```

### Dataset Size

**9,994 transactions**

The analysis uses the fields available in the dataset and does not assume unavailable attributes such as customer IDs, order IDs, product IDs, shipping costs, or order dates.

---

# 1️⃣ Data Preparation

The dataset was inspected for:

* Missing values
* Invalid sales values
* Data types
* Duplicate records
* Profitability consistency
* Numerical outliers

A transaction-level profit margin was engineered:

```python
df["Profit Margin"] = (df["Profit"] / df["Sales"]) * 100
```

### Dataset-level results

| Metric                   |    Value |
| ------------------------ | -------: |
| Transactions             |    9,994 |
| Total Sales              |   ₹2.30M |
| Total Profit             | ₹286.24K |
| Overall Profit Margin    |   12.46% |
| Loss-Making Transactions |    1,869 |
| Observed Loss            | ₹156.11K |

---

# 2️⃣ Profitability Analysis

Transactions were analyzed based on:

* Sales
* Profit
* Profit Margin
* Discount
* Quantity
* Category
* Sub-Category
* Region
* State

This established the baseline profitability picture before applying machine learning.

---

# 3️⃣ Discount Analysis

Discounts were grouped into five bands:

```text
0–10%
10–20%
20–30%
30–40%
40%+
```

The analysis showed that higher discount bands were associated with progressively weaker aggregate profitability.

For example:

| Discount Band |  Revenue |   Profit |  Margin |
| ------------- | -------: | -------: | ------: |
| 0–10%         |   ₹1.14M | ₹329.87K |  28.89% |
| 10–20%        | ₹792.06K |  ₹91.73K |  11.58% |
| 20–30%        | ₹102.95K | -₹10.36K | -10.06% |
| 30–40%        | ₹130.91K | -₹25.45K | -19.44% |
| 40%+          | ₹128.63K | -₹99.55K | -77.40% |

> **Important:** This analysis identifies an association between discount levels and profitability. It does not establish that discounts caused the losses.

---

# 4️⃣ Business Risk Indicators

Several transaction-level indicators were created to support investigation:

### Negative Profit

Identifies transactions where:

```text
Profit < 0
```

### High Discount

Identifies transactions with:

```text
Discount > 20%
```

### Extreme Discount

Identifies transactions with:

```text
Discount > 40%
```

### Severe Margin Loss

Identifies transactions where:

```text
Profit Margin < -20%
```

These indicators were used as supporting evidence alongside machine-learning anomaly detection.

---

# 5️⃣ 🤖 Anomaly Detection with Isolation Forest

Isolation Forest was used to identify transactions that were statistically unusual within the dataset.

### Features used

```text
Sales
Quantity
Discount
Profit
Profit Margin
```

Implementation:

```python
from sklearn.ensemble import IsolationForest

ml_features = df[
    ["Sales", "Quantity", "Discount", "Profit", "Profit Margin"]
].copy()

model = IsolationForest(
    contamination=0.05,
    random_state=42
)

model.fit(ml_features)

df["Anomaly"] = model.predict(ml_features)
```

The model identified:

**499 anomalous transactions**

These anomalies were not automatically treated as profit leaks.

---

# 6️⃣ 🎯 Potential Profit-Leak Candidates

To make the anomaly detection useful from a business perspective, anomaly detection was combined with profitability:

```text
Anomaly
+
Negative Profit
=
Potential Profit-Leak Candidate
```

### Result

**328 transactions** were identified as potential profit-leak candidates.

| Metric                    |   Result |
| ------------------------- | -------: |
| Total Transactions        |    9,994 |
| Potential Leak Candidates |      328 |
| Candidate Share           |    3.28% |
| Candidate Loss            | ₹106.94K |
| Observed Loss Captured    |   68.50% |

The 328 candidates represent a relatively small investigation set while containing **68.50% of the observed loss**.

---

# 7️⃣ 🔍 Explainable Leak Analysis

The project generates human-readable reasons for why a transaction was selected for investigation.

Example:

```text
Negative Profit
+
Extreme Discount
+
Severe Margin Loss
+
Unusual Transaction Pattern
```

The analysis also creates structured indicators such as:

```text
Negative_Profit
High_Discount
Severe_Margin
ML_Anomaly
Evidence_Count
Leak_Candidate
```

This makes the ML output easier to investigate using business rules and transaction-level evidence.

---

# 8️⃣ 📦 Product-Level Analysis

Potential leak candidates were analyzed by sub-category.

The largest concentrations of candidate loss were associated with:

| Sub-Category | Candidate Loss |
| ------------ | -------------: |
| Binders      |        ₹32.42K |
| Machines     |        ₹28.65K |
| Tables       |        ₹19.45K |
| Appliances   |         ₹8.63K |
| Bookcases    |         ₹8.24K |

**Binders, Machines and Tables together represented approximately 75.3% of candidate loss.**

---

# 9️⃣ 💸 Candidate Discount Analysis

Among the 328 potential leak candidates:

| Discount Band | Candidates | Candidate Loss |
| ------------- | ---------: | -------------: |
| 10–20%        |          9 |         ₹3.66K |
| 20–30%        |          4 |         ₹1.30K |
| 30–40%        |         39 |        ₹17.15K |
| 40%+          |        276 |        ₹84.83K |

### Key observation

**276 of 328 candidates (84.15%) had discounts of 40% or more.**

These transactions represented approximately **79.32% of candidate loss**.

Again, this is an **association**, not a causal conclusion.

---

# 🔟 Root Cause Analysis

Potential losses were analyzed across:

```text
Sub-Category × Discount Band
```

This allowed concentrated patterns to be identified.

Examples include:

* Binders × 40%+
* Machines × 40%+
* Tables × 30–40%
* Appliances × 40%+
* Bookcases × 40%+

A configurable business priority score was created using:

```text
50% → Loss Impact
30% → Frequency
20% → Loss Severity
```

The score is intended to help organize investigation efforts.

It is **not an ML prediction or an objective measure of business importance**.

---

# 1️⃣1️⃣ 🌎 Geographic Analysis

Potential leak candidates were also analyzed geographically.

### Candidate Loss by Region

| Region  | Candidate Loss |  Share |
| ------- | -------------: | -----: |
| Central |        ₹40.55K | 37.92% |
| East    |        ₹30.87K | 28.87% |
| South   |        ₹21.18K | 19.81% |
| West    |        ₹14.34K | 13.41% |

The Central region represented approximately **37.92% of candidate loss**.

At the state level, Texas represented approximately **24.79% of candidate loss**.

These results indicate geographic concentration within the identified candidates but do not establish geographic causation.

---

# 📊 Power BI Dashboard
## 📊 Dashboard Preview

### Executive Overview
![Executive Overview](<img width="1103" height="752" alt="Executive Overview" src="https://github.com/user-attachments/assets/ad50d807-d0d8-4023-806a-6ed0eed5b3ff" />
)

### Leak Explorer
![Leak Explorer](<img width="1437" height="803" alt="Leak Explorer" src="https://github.com/user-attachments/assets/64d6a0bd-fc30-47bf-b956-bf2ca16728da" />
)

### Root Cause Analysis
![Root Cause Analysis](<img width="1045" height="801" alt="Root Cause Analysis" src="https://github.com/user-attachments/assets/f8050cd4-8cd5-48be-9e8d-fde05f7a8f5c" />
)

### Priority & Geography
![Priority & Geography](<img width="1292" height="816" alt="Priority Geography" src="https://github.com/user-attachments/assets/bd3c31e6-52db-454d-a815-5434db2e580a" />
)
The analysis was converted into a four-page interactive Power BI dashboard.

## Page 1 — Executive Overview

Provides the overall profitability picture.

### KPIs

* Total Sales
* Total Profit
* Overall Profit Margin
* Loss-Making Transactions
* ML Leak Candidates
* Loss Captured %

---

## Page 2 — Leak Explorer

Provides transaction-level investigation of the potential leak candidates.

### Filters

* Region
* State
* Category
* Sub-Category
* Discount Band
* Segment

### Investigation fields

* Sales
* Quantity
* Discount
* Profit
* Profit Margin
* Evidence Count
* Leak Type
* Leak Reason

---

## Page 3 — Root Cause Analysis

Analyzes the patterns behind potential profit leakage.

### Includes

* Candidate Loss by Sub-Category
* Candidate Loss by Discount Band
* Sub-Category × Discount Band Matrix
* Root Cause Investigation Priorities

---

## Page 4 — Priority & Geography

Analyzes where potential leak candidates are concentrated.

### Includes

* Candidate Loss by Region
* Top States by Candidate Loss
* Region × Category Matrix
* Priority States for Investigation

---

# 📌 Key Findings

### Profitability

**9,994 transactions** generated approximately **₹2.30M in sales** and **₹286.24K in profit**, producing an overall margin of **12.46%**.

### Losses

**1,869 transactions** generated negative profit, with approximately **₹156.11K in observed loss**.

### ML-Assisted Investigation

**328 transactions** were identified as potential profit-leak candidates.

These candidates captured approximately **68.50% of observed loss**.

### Discount Concentration

Transactions with **40%+ discounts represented 84.15% of potential leak candidates and 79.32% of candidate loss**.

### Product Concentration

**Binders, Machines and Tables represented approximately 75.3% of candidate loss.**

### Geographic Concentration

The **Central region represented approximately 37.92% of candidate loss**, while Texas represented approximately 24.79%.

---

# ⚠️ Analytical Limitations

ProfitGuard is an **analytics and investigation project**, not a causal inference or production decision-making system.

The analysis does not prove that:

* High discounts caused losses
* Specific products caused losses
* Specific regions caused losses
* Anomalous transactions are errors
* Every flagged transaction requires corrective action

Isolation Forest identifies statistically unusual observations based on the selected features.

The business priority score is a configurable framework for organizing investigation efforts.

Additional operational data would be required to establish causal drivers.

---

# 🚀 Future Improvements

Possible extensions include:

* SHAP-based anomaly explanations
* Automated data ingestion
* SQL database integration
* Scheduled anomaly detection
* Automated email/Teams alerts
* Product-level profitability monitoring
* Discount optimization
* Predictive profit-risk modeling
* LLM-generated investigation summaries
* Automated business reports
* What-if discount simulations

---

# 📂 Project Structure

```text
ProfitGuard/
│
├── README.md
│
├── notebooks/
│   └── ProfitGuard_Analysis.ipynb
│
├── powerbi/
│   └── ProfitGuard.pbix
│
├── outputs/
│   ├── profitguard_full.csv
│   ├── profit_leaks.csv
│   ├── profitguard_root_cause.csv
│   └── profitguard_priority.csv
│
├── screenshots/
│   ├── executive_overview.png
│   ├── leak_explorer.png
│   ├── root_cause_analysis.png
│   └── priority_geography.png
│
└── requirements.txt
```

---

# ▶️ How to Run

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd ProfitGuard
```

### 2. Install dependencies

```bash
pip install pandas numpy scikit-learn matplotlib jupyter
```

### 3. Open Jupyter

```bash
jupyter notebook
```

Open:

```text
notebooks/ProfitGuard_Analysis.ipynb
```

Run the notebook sequentially to reproduce the analysis.

### 4. Open Power BI

Open:

```text
powerbi/ProfitGuard.pbix
```

The dashboard uses the processed analytical outputs generated during the project.

---

# 💼 Skills Demonstrated

**Python · Pandas · NumPy · Scikit-learn · Isolation Forest · Data Cleaning · Feature Engineering · Exploratory Data Analysis · Anomaly Detection · Profitability Analysis · Root Cause Analysis · Power BI · DAX · Power Query · Data Visualization · Business Intelligence · Business Analytics**

---

# 👨‍💻 Project Summary

ProfitGuard demonstrates an end-to-end **Data Analytics + Data Science workflow**:

```text
Data
 ↓
Cleaning
 ↓
Analysis
 ↓
Feature Engineering
 ↓
Machine Learning
 ↓
Anomaly Detection
 ↓
Investigation Candidates
 ↓
Root Cause Analysis
 ↓
Business Insights
 ↓
Power BI
```

The project focuses on using machine learning **as a supporting analytical tool**, while keeping the final investigation interpretable and connected to measurable business outcomes.
