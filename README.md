# Telecom Customer Churn Analysis

## Introduction

Customer churn in the telecom industry refers to the phenomenon where subscribers discontinue their service with a provider. Retaining existing customers is significantly more cost-effective than acquiring new ones, making churn analysis a critical business function. This project investigates behavioral, contractual, and service-related signals within a telecom dataset to understand why customers leave and what patterns precede their departure.

The analysis begins with a structured literature review drawn from published telecom churn research, followed by data cleaning, exploratory data analysis, bivariate comparisons, and feature importance evaluation using a Random Forest model.

---

## Problem Statement

Telecom companies face ongoing customer attrition driven by competitive pricing, poor service experience, and weak customer engagement. The core business problem is identifying which customers are at risk of churning before they actually leave. This requires understanding the underlying behavioral and contractual factors that are associated with churn.

Three specific hypotheses were investigated in this project:

- H1 (Service Support): Customers without TechSupport or OnlineSecurity are more likely to churn because they feel unsupported.
- H2 (Pricing and Commitment): Customers on high monthly charges combined with month-to-month contracts are more likely to churn due to continuous price-value comparison.
- H3 (Low Service Usage): Customers using fewer additional services such as StreamingTV, OnlineBackup, and DeviceProtection are more likely to churn due to lower platform engagement.

---

## Project Objectives

- Analyze the distribution and characteristics of churned versus retained customers.
- Identify which features — contractual, demographic, and service-related — have the strongest association with churn.
- Perform structured univariate and bivariate exploratory analysis across all relevant columns.
- Use a Random Forest classifier to rank feature importance relative to churn prediction.
- Produce a cleaned dataset ready for downstream modeling.

---

## Dataset Information

**File:** telcom_churndataset.csv

**Original shape:** 7,043 rows, 21 columns

**After cleaning:** 7,010 rows, 19 columns

**Target variable:** Churn (Yes / No)

**Churn distribution:**
- No (Retained): 5,163 customers — 73.42%
- Yes (Churned): 1,869 customers — 26.58%

| Column | Type | Description |
|---|---|---|
| customerID | object | Unique identifier — dropped before analysis |
| gender | object | Customer gender |
| SeniorCitizen | int64 | Whether the customer is a senior citizen (1/0) |
| Partner | object | Whether the customer has a partner |
| Dependents | object | Whether the customer has dependents |
| tenure | int64 | Number of months with the company |
| PhoneService | object | Whether the customer has phone service |
| MultipleLines | object | Whether the customer has multiple lines — dropped |
| InternetService | object | Type of internet service (DSL, Fiber optic, No) |
| OnlineSecurity | object | Whether the customer has online security |
| OnlineBackup | object | Whether the customer has online backup |
| DeviceProtection | object | Whether the customer has device protection |
| TechSupport | object | Whether the customer has tech support |
| StreamingTV | object | Whether the customer has streaming TV |
| StreamingMovies | object | Whether the customer has streaming movies |
| Contract | object | Contract type (Month-to-month, One year, Two year) |
| PaperlessBilling | object | Whether the customer uses paperless billing |
| PaymentMethod | object | Payment method used |
| MonthlyCharges | float64 | Monthly charge amount |
| TotalCharges | float64 | Total charges — converted from object to numeric |
| Churn | object | Target variable — whether the customer churned |

---

## Technologies Used

| Tool / Library | Purpose |
|---|---|
| Python | Core programming language |
| Jupyter Notebook | Development and analysis environment |
| Pandas | Data loading, cleaning, and manipulation |
| NumPy | Numerical operations |
| Matplotlib | Static visualizations |
| Seaborn | Statistical visualizations |
| Plotly | Interactive charts and distribution plots |
| Scikit-learn | Random Forest classifier, Label Encoding, train/test split |

---

## Project Structure

    Churn-Risk-Investigation/
    │
    ├── data/
    │   ├── raw-data/
    │   │   └── telcom_churndataset.csv
    │   └── cleaned-data/
    │
    ├── notebooks/
    │   └── telcom-churn-analysis.ipynb
    │
    ├── LICENSE
    └── README.md

---

## Data Cleaning

**1. Type conversion**
TotalCharges was stored as an object column. It was converted to numeric using pd.to_numeric with errors set to coerce, which introduced 11 NaN values where the original data was blank.

**2. Missing value removal**
The 11 rows with null TotalCharges values were removed using df.dropna(). After this step, no missing values remained in the dataset.

**3. Duplicate removal**
df.duplicated().sum() returned 22 duplicate records. These were removed using df.drop_duplicates(). After removal, the duplicate count confirmed as 0.

**4. Column removal**
Two columns were dropped: customerID as it carries no analytical value, and MultipleLines during preprocessing.

**5. Label creation**
A new column SeniorCitizen_label was created by mapping the binary SeniorCitizen column (0/1) to readable labels (Non-Senior / Senior) for visualization purposes.

The final cleaned dataset was saved as telcom_churn_clean_file.csv.

---

## Exploratory Data Analysis

### Univariate Analysis

**Tenure:** The distribution shows a bimodal pattern — a large concentration of new customers at low tenure and a smaller rise among long-term customers. Mid-tenure customers are least represented.

**MonthlyCharges:** Very few customers are in low monthly plans (20–40). Most customers are concentrated in the higher range (80–120), indicating a customer base skewed toward premium plans.

**TotalCharges:** Right-skewed distribution confirming that most customers are relatively early in their account lifecycle with lower cumulative spend.

**Churn:** 73.42% of customers are not churning; 26.58% are churning.

**SeniorCitizen:** Approximately 84% of customers are non-senior citizens.

### Bivariate Analysis

**Tenure vs Churn:** Customers who churned had significantly lower median tenure than those who stayed, confirming that newer customers are at higher churn risk.

**MonthlyCharges vs Churn:** Churned customers are concentrated in the 50–90 monthly charge range. Customers with charges below 20–40 are largely retained.

**Contract vs Churn:** Month-to-month contract customers show the highest churn rate. Customers on one-year and two-year contracts are significantly more stable.

**InternetService vs Churn:** Fiber optic customers show higher churn compared to DSL customers. Customers with no internet service churn the least.

**PaymentMethod vs Churn:** Electronic check users show the highest churn rate among all payment methods.

**Service columns vs Churn:** Customers without OnlineSecurity, TechSupport, OnlineBackup, DeviceProtection, StreamingTV, and StreamingMovies are disproportionately represented among churned customers.

**Partner and Dependents vs Churn:** Customers without partners or dependents churn at higher rates.

**PaperlessBilling vs Churn:** Customers using paperless billing churn at a higher rate than those on paper billing.

### Correlation Analysis

A correlation heatmap of numerical features (tenure, MonthlyCharges, TotalCharges) was produced. TotalCharges shows strong positive correlation with both tenure and MonthlyCharges. MonthlyCharges and tenure show a weaker direct correlation.

---

## Key Findings

- 26.58% of customers in the dataset have churned, representing a significant proportion that warrants active retention effort.
- Month-to-month contract customers are the highest-risk churn segment. Customers on longer-term contracts churn far less frequently.
- New customers with low tenure are the most vulnerable to churn. Early engagement directly affects retention.
- Fiber optic internet service customers churn at a higher rate than DSL customers, suggesting a price-value perception issue.
- Customers paying via electronic check churn more than those using automatic payment methods.
- The absence of TechSupport and OnlineSecurity is associated with higher churn, consistent with H1.
- High monthly charges combined with month-to-month contracts represent the highest-risk customer profile, consistent with H2.
- Customers using fewer additional services are more likely to churn, consistent with H3.
- Senior citizens, customers without partners, and customers without dependents all show above-average churn rates.
- Feature importance from the Random Forest model identified TotalCharges, MonthlyCharges, and tenure as the three most influential features for churn prediction.

---

## Visualizations

**Tenure Distribution:** Plotly distplot showing the bimodal distribution of customer tenure.

**Monthly Charges Distribution:** Plotly distplot revealing that most customers fall into higher monthly charge brackets.

**Total Charges Distribution:** Right-skewed Plotly distplot showing lower-spend, shorter-tenure customers dominate.

**Churn and Contract Distribution:** Side-by-side Plotly bar charts comparing churn split alongside contract type distribution.

**Service columns vs Churn:** Six service features compared against churn across two Plotly chart panels — OnlineSecurity, OnlineBackup, DeviceProtection in one; TechSupport, StreamingTV, StreamingMovies in another.

**Contract vs Churn (Hidden Pattern 1):** Seaborn countplot — month-to-month contracts dominate the churned group.

**Tenure vs Churn (Hidden Pattern 2):** Seaborn histplot with KDE — clear separation between churned and retained customers by tenure.

**Monthly Charges vs Churn (Hidden Pattern 3):** Seaborn boxplot — churned customers have higher median monthly charges.

**Internet Service vs Churn (Hidden Pattern 4):** Seaborn countplot — fiber optic customers show disproportionate churn.

**Payment Method vs Churn (Hidden Pattern 5):** Seaborn countplot — electronic check users churn the most.

**Correlation Heatmap:** Seaborn heatmap and Plotly imshow for tenure, MonthlyCharges, and TotalCharges.

**Feature Importance:** Random Forest horizontal bar chart — TotalCharges, MonthlyCharges, and tenure rank as the top predictors.

---

## Customer Churn Patterns

**Pattern 1 — Contract Lock-In Effect:** Month-to-month customers churn at a far higher rate than customers on annual or biennial contracts. Lack of contractual commitment directly enables easier exit.

**Pattern 2 — New Customer Vulnerability:** Customers in the early months of their tenure are the most at-risk. Long-term customers are significantly more likely to stay.

**Pattern 3 — Price Pressure on Mid-Range Spenders:** Customers in the 50–90 monthly charge range show the highest churn, suggesting a group that perceives its cost as high relative to the value received.

**Pattern 4 — Fiber Optic Dissatisfaction:** Despite being a premium service, fiber optic internet customers churn more than DSL customers, indicating that higher service cost does not translate to higher loyalty.

**Pattern 5 — Electronic Check Payment as a Churn Indicator:** Customers paying by electronic check show the highest churn rates among all payment methods. Customers on automatic payment methods are more retained.

---

## Business Recommendations

**1. Incentivize longer-term contracts.**
Month-to-month customers churn at the highest rate. Offering discounts or added benefits for switching to one-year or two-year contracts would reduce the most vulnerable churn segment.

**2. Invest in early-tenure customer experience.**
New customers are at the highest churn risk. A structured onboarding program and proactive support outreach in the first three to six months could meaningfully improve early retention.

**3. Review the fiber optic value proposition.**
Fiber optic customers paying premium prices are churning at higher rates than DSL customers. Pricing, service quality, or support for this segment should be investigated and improved.

**4. Promote TechSupport and OnlineSecurity enrollment.**
Customers without these services churn more. Bundling these services at reduced cost or highlighting their value during onboarding could reduce churn and increase engagement.

**5. Encourage migration from electronic check to automatic payment methods.**
Electronic check customers churn more. A small incentive for switching to auto-pay may reduce churn and manual processing costs.

**6. Prioritize customers without partners or dependents for retention campaigns.**
These customers are more likely to churn and may be more price-sensitive or less anchored to their current plan.

---

## Future Improvements

- Build a full supervised classification pipeline with proper evaluation metrics — accuracy, precision, recall, F1-score, and ROC-AUC. The current notebook trains a Random Forest for feature importance but does not evaluate predictive performance on held-out data.
- Address class imbalance (73% retained vs 27% churned) using techniques such as SMOTE or class-weight adjustment before building a production-ready model.
- Replace the hardcoded local file path with a relative path so the notebook runs without modification after cloning.
- Add a requirements.txt file listing all dependencies with version numbers for reproducibility.
- Translate all cell-level markdown commentary currently written in Hindi/Urdu into English for broader readability.

---

## Author

**Neha Karki**

GitHub: [https://github.com/NehaKarki-35](https://github.com/NehaKarki-35)

---

## License

This project is licensed under the MIT License.
