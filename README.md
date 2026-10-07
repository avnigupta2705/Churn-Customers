# 📉 Customer Churn Analysis

An end-to-end exploratory data analysis of a subscription business, using **SQL (SQLite), Python, pandas, matplotlib and seaborn**. The project cleans raw multi-table data, builds churn-related features and KPIs, and visualises where and why customers leave.

---

## 📌 Problem Statement

For a subscription business, every cancellation means lost recurring revenue, and winning a new customer usually costs more than keeping an existing one. The business wants answers to these questions:

- What percentage of customers are leaving, and what percentage are staying?
- Which plan types, contract types and regions have the highest churn?
- Do customer support issues (complaints, escalations, low satisfaction) affect churn?
- How much revenue is at risk because of churned customers?

The data was spread across **three database tables** (customer, subscription, support) and contained missing values, inconsistent labels and wrong data types, so it had to be cleaned and combined before analysis.

---

## 🎯 Objectives

1. Extract data from a SQLite database into pandas.
2. Clean and standardise the data.
3. Merge the tables into one analysis-ready dataset.
4. Engineer churn features and calculate business KPIs.
5. Visualise churn patterns and summarise findings.

---


### Database tables

| Table | Description |
|---|---|
| `db_customer` | Customer ID, name, country, state, gender, date of birth |
| `db_subscription` | Start/renewal/cancellation dates, subscription type, plan type, contract type, monthly charges, CLTV, churn score |
| `db_support` | Complaint date, escalation status, CSAT score |

---

## 🛠️ Tech Stack

- **Python** (pandas, NumPy)
- **SQL / SQLite** (`sqlite3`)
- **matplotlib** and **seaborn** for visualisation
- **Jupyter Notebook**

---

## ⚙️ Methodology

### 1. Data extraction
- Connected to `customer_churn.db` using `sqlite3`.
- Read the table names from `sqlite_master` and loaded every table into its own DataFrame with a loop.

### 2. Data cleaning
- Renamed `name` → `customer_name`.
- Dropped unused columns (`interests`, `pincode`, `col_1`, `comment`).
- Converted date columns (`dob`, subscription start, renewal, cancellation, complaint date) to `datetime`.
- Standardised gender labels (`Men` → `Male`, `Women` → `Female`).
- Filled missing `country` values by mapping each state to its country.

### 3. Feature engineering
| Feature | Logic |
|---|---|
| `churn_flag` | `1` if a cancellation date exists, otherwise `0` |
| `complaint_count` | Number of complaints per customer |
| `tenure_days` | Cancellation date (or today, if still active) minus subscription start date |
| `churn_risk` | `low` / `med` / `high` buckets based on `churn_score` |
| `escalations` | Converted from `Y`/`N` to `1`/`0` |

Duplicate customer IDs in the support table were reduced to the latest complaint per customer before merging, so the merge did not inflate the row count. The three tables were then joined on `customerid` and exported to `exported_churn_data.csv`.

### 4. KPIs calculated
- Churn rate and retention rate
- Churn rate by plan type, state and subscription type
- ARPU (average revenue per user)
- Average customer tenure
- Revenue at risk (monthly charges of churned customers)
- Escalation rate and average complaints per user
- Correlation between escalation and churn

### 5. Visualisation
- **matplotlib:** monthly churn trend, churn by plan type, churn by state
- **seaborn:** correlation heatmap, pair plot, catplot (plan vs. charges by gender and churn risk)
- **pandas pivot tables** for plan-level summaries

---

## 📊 Key Findings

| Metric | Result |
|---|---|
| Overall churn rate | ~**28.6%** |
| Retention rate | ~**71.4%** |
| ARPU | ~**18.85** |
| Monthly revenue at risk | ~**73.94** |

**Churn by plan type**

| Plan | Churn rate |
|---|---|
| Basic | ~60% |
| Standard | ~22% |
| Premium | ~14% |

**Churn by contract type**

| Contract | Churn rate |
|---|---|
| Monthly | ~56% |
| Annual | ~8% |

**Cancellation reasons recorded:** switched to competitor, too expensive, not enough content, poor streaming quality, forgot to cancel trial.

> ⚠️ **Note:** The dataset contains only 21 customers, so these results show patterns in this sample and should not be treated as statistically conclusive.

---

## 💡 Insights and Recommendations

- **Move monthly subscribers to annual plans.** Annual contracts retain customers far better than monthly ones.
- **Improve the value of the Basic plan.** It has the highest churn, so look at pricing and content.
- **Fix support quality.** Track escalations and complaints and act on them early.
- **Target high-risk customers.** Use `churn_risk` to run retention campaigns before customers cancel.

---

## 📚 What I Learned

- Extracting data from a SQL database into pandas and loading multiple tables dynamically.
- Cleaning real-world data: type conversion, label standardisation and imputing missing values from related columns.
- Merging multiple tables safely, including handling duplicate keys before a join.
- Feature engineering with `np.where` and `np.select`.
- Building business KPIs with `groupby`, `agg` and `pivot_table`.
- **Encoding matters:** `.cat.codes` orders categories alphabetically, so I used explicit ordering (e.g. Basic < Standard < Premium) to get meaningful correlations.
- Avoiding accidental overwrites by working on a copy (`df_copy`) and noting steps that must only be run once.
- Turning analysis into business recommendations, not just charts.

---

## 🚀 How to Run

1. Clone the repository
   ```bash
   git clone https://github.com/<your-username>/<your-repo-name>.git
   cd <your-repo-name>
   ```
2. Install dependencies
   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```
3. Launch the notebook
   ```bash
   jupyter notebook Churn_customers_Analysis.ipynb
   ```
4. Run the cells from top to bottom. Keep `customer_churn.db` in the same folder as the notebook.

---

## 🔮 Future Scope

- Build a predictive churn model (logistic regression, random forest, XGBoost).
- Use a larger dataset for statistically reliable conclusions.
- Create an interactive dashboard in Power BI or Tableau.
- Fix the `churn_risk` bins so a churn score of exactly 70 is covered (the current condition uses `< 70` and `> 70`, which leaves 70 as `unknown`).
- Add customer segmentation and survival analysis.


