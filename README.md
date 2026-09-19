### Customer Churn Prediction: ML Problem Framing & Responsible Data Card

This repository contains the Machine Learning (ML) Problem Framing, Exploratory Data Analysis (EDA), and Responsible Data Card documentation for predicting customer churn based on subscription and transactional data. 

### Project Structure

* ML_Problem_Framing_Data_Card.ipynb - Jupyter Notebook containing data quality checks, class balance analysis, and EDA.
* customer_churn_data.csv - The customer retention dataset.
* README.md - Project documentation and Responsible Data Card.

### Responsible Data Card

### Dataset Purpose

* **Supported Decisions:** This dataset is intended to support the engineering and product teams in identifying customers who are at a high risk of leaving the service (churned = 1). It helps design proactive customer retention campaigns and offers.
* **Prohibited Decisions:** This dataset must not be used to automatically block users, deny them basic support services, or implement discriminatory pricing models based on their plan types or spending habits.

### Provenance and Permission

* **Data Creation:** The dataset was generated from internal system logs, CRM databases, and billing transaction platforms.
* **Access Control:** Authorized data scientists, product managers, and retention marketing teams within the organization may use it.
* **Consent & Licensing:** Data collection is covered under the standard User Terms of Service and Privacy Policy signed by customers during registration. No external third-party licensing restrictions apply.

### Population and Representation

* **Represented Groups:** Active and recently churned users who have interactive billing histories across various subscription plan types.
* **Missing Groups:** Users who cancelled their subscription immediately on Day 1 or users who signed up using completely free trial promotions that bypass the standard monthly spend tracking.
* **Imbalance:** The dataset is relatively well-balanced with **58.33%** retained users and **41.66%** churned users, minimizing severe class-imbalance issues.

### Features and Target

* **Target Label:** churned (Binary: 1 indicates the user left the service, 0 indicates they are active).
* **Features:** 

  * customer_id: Unique identifier (Non-predictive proxy, dropped before training).
  * tenure_months: Total duration of the customer relationship in months.
  * support_tickets: Total support requests raised (Proxy for user dissatisfaction).
  * monthly_spend_inr: Monthly recurring revenue from the customer in INR.
  * last_login_days: Days elapsed since the last platform login (Direct engagement metric).
  * plan_type: Categorical attribute representing the subscription tier.
* **Leakage Risks:** support_tickets or last_login_days could cause leakage if they record activities *after* the customer has already initiated the cancellation process.

### Quality Checks

* **Missingness:** Checked via .isnull().sum(). Count is **0** missing entries across all columns.
* **Duplicates:** Checked via .duplicated().sum(). Count is **0** duplicate rows.
* **Outliers:** Checked basic distributions; continuous fields like monthly_spend_inr and support_tickets fall within expected ranges.
* **Train/Test Separation:** The dataset will be cleanly split into 80% training and 20% testing subsets prior to any feature scaling or model training to prevent data contamination.

### Risks and Safeguards

* **False-Positive Risk:** Identifying a loyal customer as "about to churn". (Mitigation Cost: Wasting discount coupons or marketing budget on users who would have stayed anyway).
* **False-Negative Risk:** Missing a customer who is actually going to leave. (Mitigation Cost: Permanent loss of customer lifetime value and recurring revenue).
* **Safeguards:**线 Implement a prediction probability threshold (e.g., > 70% confidence) before triggering aggressive retention workflows, along with periodic human-in-the-loop validation for high-value enterprise accounts.

### Intended Evaluation

* **Baselines:** A simple heuristic rule-based model (e.g., if last_login_days > 30, then predict churn) will serve as the baseline benchmark.
* **Metrics:** Since predicting actual churners is critical, **Recall** and **F1-Score** will be prioritized over raw Accuracy to properly measure true positive churn captures.
* **Fairness:** Error analysis will be grouped and evaluated across different categories of plan_type to ensure the model does not disproportionately misclassify users on cheaper vs expensive tiers.
