# Financial Fraud Detection: Proactive Machine Learning Approach

## Overview
This project focuses on developing a robust machine learning model to proactively detect fraudulent financial transactions. Using a dataset of over 6.3 million records, the goal is to identify fraudulent behaviors, understand the key predictors of fraud, and provide actionable infrastructure recommendations to prevent future financial loss.

## Business Problem
A financial company needs to improve its infrastructure to prevent fraudulent agents from taking control of customer accounts, transferring funds, and cashing out. The current system relies on static rules (e.g., flagging transfers over 200,000), which are largely ineffective. This project executes a full end-to-end machine learning pipeline to replace this outdated logic with a highly accurate predictive model.

## Dataset
The dataset contains **6,362,620 rows** and **11 columns**, representing 30 days of simulated transaction data. 
Key features include:
* **Transaction Details:** `step` (hour of time), `type` (CASH-IN, CASH-OUT, DEBIT, PAYMENT, TRANSFER), `amount`.
* **Account Details:** `nameOrig`, `nameDest`.
* **Balance Information:** `oldbalanceOrg`, `newbalanceOrig`, `oldbalanceDest`, `newbalanceDest`.
* **Target Variables:** `isFraud` (actual fraud indicator), `isFlaggedFraud` (business rule flag).

## Methodology

### 1. Exploratory Data Analysis (EDA) & Data Cleaning
* **Extreme Class Imbalance:** Fraud represents an exceptionally small fraction of total transactions.
* **Behavioral Targeting:** EDA revealed that 100% of fraudulent activities occur exclusively in `TRANSFER` and `CASH_OUT` transaction types. The dataset was filtered to include only these types.
* **Account Draining:** 97.82% of fraudulent transactions involve the criminal attempting to empty the exact entire balance of the compromised account.

### 2. Feature Engineering
To help the models detect physical impossibilities and system vulnerabilities, new features were engineered:
* `HourOfDay`: Extracted from the `step` column to capture a 24-hour cycle.
* `errorBalanceOrig`: Mathematical error between expected and actual originating balances.
* `errorBalanceDest`: Mathematical error between expected and actual destination balances.

### 3. Handling Class Imbalance
The **SMOTE** (Synthetic Minority Over-sampling Technique) algorithm was applied exclusively to the training data to generate synthetic fraudulent samples, achieving a perfectly balanced training set without leaking data into the test set.

### 4. Model Training & Evaluation
Several models were evaluated using **AUPRC (Area Under the Precision-Recall Curve)** as the primary metric, given the highly imbalanced nature of the real-world data:
* **Random Forest:** AUPRC = 0.9969 (Best Performer)
* **XGBoost:** AUPRC = 0.9926
* **Decision Tree:** AUPRC = 0.9483
* **Logistic Regression:** AUPRC = 0.6155

## Key Insights & Business Recommendations

### Top Predictors of Fraud
The most critical factors driving the model's predictions are the engineered mathematical balance errors (`errorBalanceOrig`, `newbalanceOrig`) and the transaction `amount`. This makes financial sense, as fraudsters frequently bypass standard system arithmetic using backdoors and attempt to steal the maximum possible amount at once.

### Recommended Infrastructure Updates
1.  **Transition to Real-Time ML:** Deprecate the static 200,000 threshold rule and integrate the Random Forest/XGBoost model into the live production pipeline.
2.  **Targeted Friction (Step-Up Auth):** Implement multi-factor authentication specifically when a `TRANSFER` or `CASH_OUT` closely matches the account's total available balance.
3.  **Cooling-Off Periods:** Introduce temporary holds on newly transferred funds before they can be completely cashed out.

### Measuring Success
* **Shadow Mode & A/B Testing:** Run the ML model alongside the legacy system for 30 days to measure the incremental dollar value of fraud prevented.
* **Customer Friction Metrics:** Monitor the False Positive Rate and customer service complaint volumes to ensure legitimate users aren't being locked out.
* **Model Drift:** Continuously track live AUPRC to ensure the model adapts as criminals change their tactics.

## Technologies Used
* **Python:** pandas, numpy
* **Machine Learning:** scikit-learn, xgboost, imbalanced-learn (SMOTE)
* **Visualization:** matplotlib, seaborn