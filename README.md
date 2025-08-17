# 🛡️ Accredian – Data Science Internship Assessment

**Fraud Detection using Machine Learning**

## 📌 Project Overview

This project builds a **fraud detection system** for a financial company to **predict fraudulent transactions in real time** and surface insights that reduce fraud losses while preserving customer trust.

The dataset contains **6.3M+ transactions** over a 30-day simulation period. Only a tiny fraction are labeled as fraudulent (`isFraud = 1`), making this a **highly imbalanced classification** problem.

---

## 📂 Dataset

**Rows × Columns:** `6,362,620 × 10`

**Key Features**

* `step` — time in hours (1 step = 1 hour)
* `type` — transaction type (`CASH-IN`, `CASH-OUT`, `DEBIT`, `PAYMENT`, `TRANSFER`)
* `amount` — transaction value
* `oldbalanceOrg`, `newbalanceOrig` — sender balances before/after
* `oldbalanceDest`, `newbalanceDest` — receiver balances (often `0` for merchants; bank has no merchant-side info)
* `isFraud` — target label (1 = fraud, 0 = not fraud)
* `isFlaggedFraud` — rule flag for large transfers (e.g., > 200k)

---

## 🧹 Data Preprocessing

* Dropped non-predictive IDs: `nameOrig`, `nameDest`
* Encoded `type` (label encoding)
* Engineered mismatch features:

  * `errorOrig = oldbalanceOrg - newbalanceOrig - amount`
  * `errorDest = newbalanceDest - oldbalanceDest - amount`
* Addressed class imbalance with **undersampling + oversampling** (✅ no SMOTE per requirements)
* Checked multicollinearity (correlation heatmap, VIF) — retained correlated but meaningful financial features since tree models handle them well

---

## 🤖 Models Implemented

1. **Random Forest Classifier**

   * Robust baseline, interpretable, handles nonlinearities & interactions
2. **XGBoost Classifier**

   * Strong tabular performance; tuned with `scale_pos_weight` for imbalance

---

## 📊 Evaluation Metrics

Accuracy is misleading under extreme imbalance, so we report:

* **Precision** — limits false alarms (customer experience)
* **Recall** — catches true frauds (loss prevention)
* **F1-score** — balance between precision & recall
* **ROC-AUC** — overall ranking quality
* **PR-AUC** — preferred for rare-event detection


---

## ✅ Results

### Random Forest

* **Recall:** \~**99.8%** (captures almost all frauds)
* **Precision:** **94–95%** (few false positives)
* **ROC-AUC:** \~**0.9998**
  

### XGBoost

* Similar or slightly better performance than Random Forest
* Higher stability across folds

> Both models **outperform** the rule-based system (`isFlaggedFraud`), which primarily flags only very large transfers and misses many true frauds.

<img width="785" height="652" alt="image" src="https://github.com/user-attachments/assets/fcf1a1ba-daa6-4a07-bd23-51b84988b92b" />


---

### - **Confusion Matrix**
  #### - Random Forest -Classification report
  <img width="818" height="901" alt="image" src="https://github.com/user-attachments/assets/e48356af-b57f-4ede-8f63-31e27a1f38a0" />
  
#### - XG BOOST- Classification report


<img width="762" height="931" alt="image" src="https://github.com/user-attachments/assets/d63fcac5-6e10-4f47-9f79-2fffa5178f9f" />



## 🔑 Key Insights

* Fraud clusters in **`TRANSFER → CASH_OUT`** flows
* **Balance mismatches** (`errorOrig`, `errorDest`) are strong predictors
* Fraudsters often **empty accounts** (post-transaction balances ≈ 0)
* A **fixed threshold** (e.g., 200k) is too rigid and misses stealthy attacks

---

## 🛡️ Business Recommendations

* Deploy **real-time ML fraud scoring** in the authorization flow
* Replace rigid limits with **adaptive thresholds** based on customer history and risk
* Use **step-up authentication** (OTP/biometric) for high-risk scores, not blanket blocking
* **Retrain regularly** (weekly/monthly) to adapt to evolving fraud patterns
* Keep a **human-in-the-loop** review queue for highest-risk transactions

---

## 📈 Measuring Success

* ↓ **Fraud losses** (absolute and as % of volume)
* ↑ **Recall** (frauds caught) with balanced **Precision** (fewer false alarms)
* ↑ **Customer satisfaction** (fewer wrongful blocks, reduced complaints)
* **A/B testing**: old controls vs. ML+rules hybrid to quantify lift

---

## 🚀 Conclusion

With targeted preprocessing, imbalance-aware training, and ensemble models (Random Forest & XGBoost), this system:

* **Protects customer funds**
* **Improves trust** through fewer false alerts
* **Outperforms** static rule-based approaches

---


