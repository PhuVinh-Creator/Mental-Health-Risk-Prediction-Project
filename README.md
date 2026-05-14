# Mental-Health-Risk-Prediction-Project
Using CDC BRFSS 2024 Survey Data

## ⚠️ Dataset Download 

This project uses the CDC BRFSS 2024 public-use data file. Due to large dataset size, please download the data below this link before running any code:

Warning: It will automatically download the dataset once the link clicked

**[2024 BRFSS Data (SAS Transport Format)](https://cdc.gov/brfss/annual_data/2024/files/LLCP2024XPT.zip)**

To import in python with the following code: 

import pandas as pd
df = pd.read_sas('LLCP2024.XPT', format='xport')

---

## Overview 📋

This project applies statistical learning methods to the 2024 Behavioral Risk Factor Surveillance System (BRFSS), a nationally administered telephone survey of **457,670 U.S. adults**, to evaluate whether routinely collected health, demographic, socioeconomic, and behavioral variables can predict mental health outcomes.

The core research question:
> *"To what extent can BRFSS 2024 survey variables predict frequent mental distress and the number of mentally unhealthy days among adults — and which predictors most consistently explain these outcomes?"*

---

## Scenarios 🔬

### Scenario A — Classification
- **Target:** `frequent_distress` — binary (1 if ≥ 14 poor mental health days in past 30, else 0)
- **Split:** 75/25 stratified train-test split (seed 42)
- **Models:** LDA, QDA, Logistic Regression, Random Forest

### Scenario B — Regression
- **Target:** `MENTHLTH` — continuous (0–30 days of poor mental health)
- **Split:** 80/20 random train-test split (seed 42)
- **Models:** Linear Regression, Random Forest Regression

---

## Key Findings 🔑

### Top 3 Predictors (consistent across both scenarios)
| Rank | Variable | Description |
|------|----------|-------------|
| #1 | `ADDEPEV3` | Ever diagnosed with depressive disorder |
| #2 | `DECIDE` | Difficulty concentrating or remembering |
| #3 | `PHYSHLTH` | Number of physically unhealthy days |

### Scenario A — Classification Results
| Model | Recall (Class 1) | ROC AUC | Precision (Class 1) | F1 (Class 1) |
|-------|-----------------|---------|---------------------|--------------|
| LDA | 0.422 | — | 0.569 | 0.485 |
| **QDA ★** | **0.586** | 0.803 | 0.374 | 0.457 |
| Logistic Regression | 0.307 | 0.837 | 0.650 | 0.417 |
| Random Forest | 0.298 | **0.846** | **0.666** | 0.412 |

> **QDA** is the recommended screening model for its highest recall (fewest missed cases).  
> **Random Forest** is preferred when ranking reliability (AUC) matters most.

### Scenario B — Regression Results
| Model | Test R² | Test RMSE (days) |
|-------|---------|-----------------|
| Linear Regression | 0.3048 | 6.9590 |
| **Random Forest ★** | **0.3279** | **6.8428** |

---

## Data Preprocessing 🛠️

- **301 original variables → 37 selected** based on theoretical relevance to mental health
- Survey code recoding: `77`/`99` → NaN · `88` → 0 · binary `2=No` → 0
- Columns with >200K missing values dropped (ACE series)
- Outlier removal: HEIGHT > 7'11" and invalid WEIGHT codes
- Missing values imputed with median (continuous) or mode (categorical)
- BMI engineered but dropped (r ≈ 0.04 with target)

---

## Variable Categories 📊

| Category | # Variables |
|----------|-------------|
| Physical Health & Disability | 8 |
| Socioeconomic | 6 |
| Demographics | 4 |
| Mental Health History | 3 |
| Lifestyle | 3 |
| Healthcare Access | 2 |

See **Appendix A** of the final report for the full variable list.

---

## Technologies Used 💻

- **Python** — primary language
- **scikit-learn** — LDA, QDA, Logistic Regression, Random Forest, PCA
- **pandas / numpy** — data manipulation
- **matplotlib / seaborn** — visualization

---

## Important Notes ⚠️

- The 85/15 class imbalance in Scenario A means **accuracy is a misleading metric** — evaluation focuses on Recall, Precision, F1, and ROC AUC.
- The best model explains ~33% of mental health variation. These are strong signals, not certainties — **human judgment remains essential**.
- BRFSS is a cross-sectional survey; results reflect **associations, not causal effects**.
- Unweighted modeling was used; results are **not nationally representative prevalence estimates**.

---

## Team Contributions 👥

| Member | Role |
|--------|------|
| Sheena Huang | Presentation design, visual layout, chart design, narrative flow, data cleaning & EDA |
| Bharati Sahani | Report writing (Introduction, Data, Discussion), data cleaning & EDA |
| Enestina Raradza | Report writing (Methods, Results, Conclusion), data cleaning & EDA |
| Prince M. Nkomo | ML implementation, model training, tuning & evaluation (Scenarios A & B), data cleaning & EDA |
| Steven Nguyen | ML implementation, model training, tuning & evaluation (Scenarios A & B), data cleaning & EDA |

---

## References 📚

- Breiman, L. (2001). Random forests. *Machine Learning*, 45, 5–32.
- CDC. (2025). 2024 BRFSS Survey Data and Documentation. https://www.cdc.gov/brfss/annual_data/annual_2024.html
- Kessler, R. C., et al. (2005). Lifetime prevalence of DSM-IV disorders. *Archives of General Psychiatry*, 62(6), 593–602.
- Pedregosa, F., et al. (2011). Scikit-learn: Machine learning in Python. *JMLR*, 12, 2825–2830.
- Saito, T., & Rehmsmeier, M. (2015). The precision-recall plot is more informative than the ROC plot on imbalanced datasets. *PLOS ONE*, 10(3).
