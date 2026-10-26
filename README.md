# Churn Analysis and Customer Intelligence

A Python-based exploratory data analysis (EDA) project that combines customer, subscription, and support data to examine customer churn, retention, subscription-plan patterns, customer tenure, revenue at risk, and customer-support signals. The work is implemented in a Jupyter Notebook and uses pandas, NumPy, Matplotlib, Seaborn, and SQLite.

> **Project type:** Data cleaning, feature engineering, exploratory analysis, and visualization  
> **Primary notebook:** `churn_analysis_final.ipynb`

## Table of contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Project files](#project-files)
- [Dataset and tables](#dataset-and-tables)
- [Technology stack](#technology-stack)
- [Setup](#setup)
- [Running the notebook](#running-the-notebook)
- [Workflow](#workflow)
- [Key metrics and analysis](#key-metrics-and-analysis)
- [Visualizations](#visualizations)
- [Output](#output)
- [Metric definitions and interpretation](#metric-definitions-and-interpretation)
- [Limitations and considerations](#limitations-and-considerations)
- [Possible extensions](#possible-extensions)

## Overview

Customer churn occurs when a customer stops using or paying for a service. Understanding churn helps a business identify retention issues, assess revenue exposure, and decide where to investigate customer experience or subscription offerings.

This project starts with three related data tables—customer profiles, subscriptions, and support interactions. It cleans and standardizes fields, derives a churn indicator and other analytical features, joins the tables into a customer-level analysis dataset, and explores churn using summary metrics, group-by analysis, plots, correlation, and pivot tables.

The notebook is an **exploratory analysis project**, not a deployed churn-prediction service. It creates a rule-based churn-risk category from an existing `churn_score`; it does not train or validate a machine-learning model.

## Objectives

- Load structured data from a SQLite database, with an Excel-based database creation alternative.
- Inspect table names, columns, data types, and record structure.
- Clean customer, subscription, and support data.
- Standardize selected categorical fields and convert date fields to datetime.
- Handle selected missing values using information already present in the dataset.
- Create a binary `churn_flag` and a categorical `churn_risk` feature.
- Combine customer, subscription, and support information using `customerid`.
- Calculate churn and retention rates, churn by plan, tenure, revenue at risk, and escalation/churn correlation.
- Visualize churn patterns and relationships between customer or subscription attributes.
- Export the joined dataset to CSV and use pivot tables for grouped summaries.
- Demonstrate basic SQL operations from Python using a separate SQLite database.

## Project files

The project bundle contains the following files:

| File | Purpose |
|---|---|
| `churn_analysis_final.ipynb` | Main notebook containing the cleaning, feature engineering, analysis, visualization, pivot-table, and SQL exercises. |
| `customer_churn_data_raw.xlsx` | Source workbook. Its three sheets are named `db_customer`, `db_subscription`, and `db_support`. |
| `customer_churn.db` | SQLite database containing the three source tables used by the notebook. |
| `exported_churn_data.csv` | Exported joined analysis dataset. The supplied export contains 21 rows and 21 columns. |
| `test_database.sqlite` | Separate practice database used for the notebook's basic SQL demonstration; it contains a `users` table. |

The Excel workbook contains 21 rows in `db_customer`, 21 rows in `db_subscription`, and 9 rows in `db_support`. These are the dimensions of the supplied source workbook, not a guarantee that future or modified versions will have the same number of records.

## Dataset and tables

### 1. `db_customer`

Contains customer profile information.

| Field | Description |
|---|---|
| `customerid` | Customer identifier used to join tables. |
| `name` | Customer name; renamed to `customer_name` during cleaning. |
| `country` | Customer country. |
| `state` | Customer state or region. |
| `gender` | Gender category; standardized during cleaning. |
| `dob` | Date of birth; converted to a date/datetime representation. |
| `interests` | Interest field; removed during cleaning. |
| `pincode` | Postal code field; removed during cleaning. |

The notebook also attempts to fill missing country values by mapping states to countries using rows where country is already populated. This assumes a state maps consistently to one country in the source data.

### 2. `db_subscription`

Contains subscription and churn-related information.

| Field | Description |
|---|---|
| `customerid` | Customer identifier. |
| `subscription_start_date` | Date the subscription started. |
| `subscription_type` | Subscription type. |
| `renewal_date` | Renewal date. |
| `plan_type` | Plan category, such as Basic, Standard, or Premium. |
| `contract_type` | Contract category, such as Monthly or Annual. |
| `cancellation_date` | Cancellation date; used to derive churn. |
| `cancellation_reason` | Recorded cancellation reason. |
| `monthly_charges` | Monthly subscription charges. |
| `cltv` | Customer lifetime value field supplied by the dataset. |
| `churn_score` | Existing churn score used to create risk bands. |

The date fields `subscription_start_date`, `renewal_date`, and `cancellation_date` are converted to datetime values.

### 3. `db_support`

Contains customer-support interaction information.

| Field | Description |
|---|---|
| `customerid` | Customer identifier. |
| `complaint_date` | Complaint date; converted to datetime. |
| `escalations` | Escalation indicator; later encoded as a binary value for correlation analysis. |
| `csat_score` | Customer satisfaction score. |
| `col_1` | Unused field removed during cleaning. |
| `comment` | Free-text comment removed during cleaning. |

The support table has fewer rows than the customer and subscription tables, so support fields may be missing for customers without a matching support record. The notebook calculates `complaint_count` by customer before rebuilding the joined dataset.

## Technology stack

- **Python 3** — analysis language
- **Jupyter Notebook** — interactive workflow and documentation
- **pandas** — data loading, cleaning, joins, grouping, and pivot tables
- **NumPy** — conditional feature creation and numerical operations
- **Matplotlib** — charts and plotting
- **Seaborn** — statistical and categorical visualizations
- **SQLite / `sqlite3`** — relational storage and SQL practice
- **openpyxl** — commonly required by pandas to read `.xlsx` workbooks

## Setup

### Prerequisites

Install Python 3 and Jupyter Notebook or JupyterLab. A virtual environment is recommended.

```bash
python -m venv .venv
```

Activate it:

```bash
# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

Install the main dependencies:

```bash
python -m pip install pandas numpy matplotlib seaborn jupyter openpyxl
```

SQLite support is provided by Python's standard-library `sqlite3` module, so a separate Python package is not normally required.

### Expected folder layout

Keep the notebook and its input files together so the relative paths used in the notebook resolve correctly:

```text
project/
├── churn_analysis_final.ipynb
├── customer_churn.db
├── customer_churn_data_raw.xlsx
├── exported_churn_data.csv
└── test_database.sqlite
```

The notebook can regenerate `customer_churn.db` from the Excel workbook if needed. **Important:** the database-creation cell uses `if_exists='replace'`, so it replaces tables with the same names in that database. Run it only when you intend to rebuild the database from the workbook.

## Running the notebook

1. Download or clone the project files into one directory.
2. Install the dependencies described above.
3. Open a terminal in that directory and start Jupyter:

   ```bash
   jupyter notebook
   ```

   Alternatively, open the folder in JupyterLab:

   ```bash
   jupyter lab
   ```

4. Open `churn_analysis_final.ipynb`.
5. Run the notebook from top to bottom so the data frames and engineered columns are created in the correct order.
6. Review the printed summaries and generated plots.
7. Confirm that `exported_churn_data.csv` is written to the working directory.

### Database fallback

The notebook first connects to `customer_churn.db`. If that file is missing or needs to be rebuilt, run the notebook cell that reads each sheet from `customer_churn_data_raw.xlsx` and writes it to a SQLite table. Then continue to the database-import section.

If you prefer to begin directly from the workbook, load its sheets with `pandas.read_excel()` and continue with the corresponding data-cleaning steps.

## Workflow

### 1. Import and inspect data

- Connect to `customer_churn.db`.
- Query SQLite's metadata to discover tables.
- Load each table into a pandas DataFrame.
- Inspect table columns and schema using `PRAGMA table_info`.
- Check dimensions, column types, and sample records.

### 2. Clean the customer table

The notebook documents and performs the following operations:

- Rename `name` to `customer_name`.
- Drop `interests` and `pincode`.
- Convert `dob` to a date/datetime type.
- Standardize gender values.
- Fill missing country values from a state-to-country mapping where possible.

### 3. Clean the subscription table

- Inspect the subscription data.
- Convert `subscription_start_date`, `renewal_date`, and `cancellation_date` to datetime.
- Retain subscription, contract, cancellation, charge, CLTV, and churn-score fields for analysis.

### 4. Clean the support table

- Drop `col_1` and `comment`.
- Convert `complaint_date` to datetime.
- Calculate the number of support records per customer using a grouped count.

### 5. Engineer features and join tables

A binary churn flag is derived from cancellation status:

```python
df_db_subscription['churn_flag'] = np.where(
    df_db_subscription['cancellation_date'].notna(), 1, 0
)
```

The notebook joins subscription data to customer data and support data using left joins on `customerid`. A left join retains subscription records even when a corresponding customer/support record is unavailable.

The notebook checks row counts and unique customer IDs around the join process. This is important because multiple support rows for one customer can multiply rows in the merged dataset. The supplied exported CSV has 21 rows; check the final row count and customer uniqueness if you modify the source data or join logic.

### 6. Export the analysis dataset

The joined DataFrame is exported as:

```python
df.to_csv('exported_churn_data.csv', index=False)
```

The supplied CSV has 21 rows and 21 columns. The exact output depends on the data and the order in which notebook cells are executed.

### 7. Calculate metrics and explore relationships

The notebook calculates overall churn and retention, churn by plan type, tenure in days, revenue at risk, the correlation between escalations and churn, and a risk category based on the existing churn score.

### 8. Visualize and summarize

Matplotlib and Seaborn are used for time trends, group comparisons, a correlation heatmap, a pair plot, and a categorical comparison plot. Pivot tables summarize churn and subscription charges by plan.

### 9. SQL practice section

The final section demonstrates how to create a SQLite table, insert a few example records, read data into pandas, aggregate budgets by country, and close the database connection. This is a separate SQL exercise and is not part of the customer-churn dataset.

## Key metrics and analysis

### Overall churn rate

The notebook calculates the mean of the binary churn flag and expresses it as a percentage:

```python
churn_rate = df['churn_flag'].mean() * 100
```

With a 0/1 flag, the mean is the fraction of rows marked as churned. The result is rounded to two decimal places for display.

### Retention rate

```python
retention_rate = 100 - churn_rate
```

This defines retention as the complement of the churn rate for the same population and denominator.

### Churn by plan type

The notebook groups rows by `plan_type` and calculates the mean of `churn_flag`, multiplied by 100. This provides the percentage of rows marked as churned in each plan group.

### Average customer tenure

Tenure is calculated as the number of days from `subscription_start_date` to:
- `cancellation_date`, for customers with a cancellation date; or
- the date the notebook is run, for records without a cancellation date.

The average of `tenure_days` is then reported. Because the calculation uses the current date for active records, the result changes over time.

### Revenue at risk

The notebook sums `monthly_charges` for rows where `churn_flag == 1`:

```python
revenue_at_risk = df.loc[
    df['churn_flag'] == 1, 'monthly_charges'
].sum()
```

This is the sum of monthly charges associated with churned rows, not a forecast of future losses or a measure of lifetime revenue. The notebook's print label says `Rs 'K'`, but the code does not divide the sum by 1,000; interpret the numeric result in the same units as `monthly_charges` unless the data definition says otherwise.

### Escalations and churn

The notebook maps `escalations == 'Y'` to `1` and other values to `0`, then calculates the correlation with `churn_flag`. The resulting correlation describes a linear association between the encoded variables; it does not establish that escalations cause churn. Check missing values and the exact source categories before interpreting this result.

### Churn-risk bands

The notebook maps the existing `churn_score` into categories:

| Condition | Risk category |
|---|---|
| `churn_score < 50` | `low` |
| `50 <= churn_score < 70` | `med` |
| `churn_score >= 70` | `high` |
| Otherwise | `unkown` in the notebook's default value |

The default label is spelled `unkown` in the notebook. The risk bands are thresholds applied to a supplied score, not categories learned or validated by a model in this project.

### Grouped analysis and pivot tables

Pivot tables summarize:
- Mean `churn_flag` by `plan_type`.
- Sum of `monthly_charges`, unique customer count, and mean `churn_flag` by plan type.

When interpreting these summaries, remember that a mean of a binary churn flag is a proportion. For a percentage display, multiply it by 100.

## Visualizations

The notebook includes the following plots and visual summaries:

1. **Monthly churn trend** — counts rows marked as churned by cancellation month.
2. **Churn rate by plan type** — compares average churn flags across plan categories.
3. **Churn by state** — compares average churn flags across states; the notebook's chart title says “Churn Rate by Country,” while its grouping and x-axis use `state`.
4. **Correlation heatmap** — visualizes correlations among selected encoded fields.
5. **Pair plot** — shows pairwise relationships for selected numeric/encoded variables.
6. **Categorical comparison plot** — compares `monthly_charges` by plan type, split by gender and churn-risk category.
7. **Pivot-table summaries** — tabular aggregation of churn and charge metrics by plan.

The plots are exploratory. They can help identify groups or patterns for further investigation, but they do not by themselves establish causality.

## Output

The main reusable output is `exported_churn_data.csv`, a flat table combining subscription, customer, and support-related fields. In the supplied export, its columns are:

```text
customerid
subscription_start_date
subscription_type
renewal_date
plan_type
contract_type
cancellation_date
cancellation_reason
monthly_charges
cltv
churn_score
churn_flag
customer_name
country
state
gender
dob
complaint_date
escalations
csat_score
complaint_count
```

Use the CSV for additional analysis in pandas, a BI tool, or a spreadsheet application. Review data types and missing values after loading it into another tool.

## Metric definitions and interpretation

- **Unit of analysis:** the notebook intends to analyze customers/subscriptions, but joins can create multiple rows per customer if the support table contains multiple records for a customer.
- **Churn definition:** a record is considered churned when `cancellation_date` is not null.
- **Retention definition:** `100% - churn rate`, using the same rows and denominator as the churn calculation.
- **Monthly revenue at risk:** sum of `monthly_charges` on churned rows; this is not annualized revenue or predicted loss.
- **Tenure:** elapsed calendar days from subscription start to cancellation or the run date.
- **Correlation:** an association measure for encoded escalation and churn indicators; not evidence of causation.
- **Risk segment:** a threshold-based categorization of the existing `churn_score`, not a model output produced by this notebook.

## Limitations and considerations

- **Small sample:** the supplied workbook has 21 customer rows and 21 subscription rows, with 9 support rows. Findings from this sample should not be generalized to a larger customer population without additional data.
- **Join cardinality:** a customer can have more than one support record. Joining raw support records can duplicate subscription rows and affect row-based churn, retention, and revenue calculations. Consider aggregating support data to one row per customer before joining if customer-level metrics are intended.
- **Country mapping assumption:** filling country from state assumes the state-to-country relationship is unambiguous in the available non-null records.
- **Missing support fields:** customers without matching support records may have null support attributes. Decide whether to preserve nulls or fill them, and document that choice.
- **Escalation encoding:** the current code maps `Y` to `1` and all other values to `0`. Validate categories and handle nulls explicitly if they should not be treated as non-escalations.
- **Risk labels:** the notebook uses `unkown` as its default category; downstream code expecting `unknown` would need a spelling correction.
- **Plot scale:** some plots use a mean of a binary flag (a fraction from 0 to 1) while the axis label says percentage. Multiply by 100 when displaying percentages.
- **Date dependence:** tenure for non-churned records changes with the date on which the notebook runs.
- **Revenue units:** the code does not convert the `monthly_charges` sum to thousands despite the output label. Confirm the source unit before reporting it.
- **Exploratory rather than predictive:** there is no train/test split, model fitting, calibration, or predictive-performance evaluation in the notebook.
- **Data privacy:** the source includes customer identifiers and names. Avoid publishing identifiable raw rows or committing sensitive data to a public repository.

## Possible extensions

- Add automated data-quality checks for nulls, duplicates, invalid dates, and unexpected category values.
- Aggregate support interactions per customer before joining, with explicit measures such as complaint count, latest complaint date, escalation count, and average CSAT.
- Add cohort analysis based on subscription start month and churn timing.
- Compare churn across contract type, subscription type, state, and cancellation reason.
- Normalize chart scales and labels consistently, especially where churn proportions are displayed as percentages.
- Add a reproducible analysis script and a dependency file such as `requirements.txt`.
- If the goal is prediction, define a point-in-time feature set, avoid target leakage, split data appropriately, train a baseline model, and evaluate it using suitable classification metrics.
- Add a dashboard for stakeholders, with clearly defined KPI denominators and date filters.

## License and attribution

No license is specified in the supplied project files. Add a `LICENSE` file before redistributing the project if you intend to make its reuse terms explicit.

The notebook's title/author markdown credits **Rishabh Mishra**. Update this attribution if you are maintaining or publishing a fork and need to identify your own contribution separately.
