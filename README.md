# Predicting Late Deliveries and Understanding the Key Drivers of Customer Satisfaction in a Food-Delivery Platform

**AICTE | IBM SkillsBuild Data Analytics with AI Internship 2026**  
Conducted by BharatCares in association with AICTE and IBM SkillsBuild

---

## Project Overview

This project analyses a **10,000-record food-delivery transaction dataset** across **12 Indian cities**, **20 restaurants**, and **8 cuisine categories** (April 2024 – March 2026). The core business problem is:

> *Can we predict whether a food-delivery order will arrive late — using only information available at dispatch time — and what factors are most associated with delivery delays and customer satisfaction?*

**IBM Bob** (IBM SkillsBuild AI assistant) was used as the AI-assisted development and analysis component throughout.

> **Data disclaimer:** The dataset (`Swiggy Cleaned Data by MKP.xlsx`) is an educational simulation dataset. It is **not** proprietary Swiggy corporate data. All findings are presented as student analytical work.

---

## Project Files

| File | Description |
|---|---|
| `Swiggy_Food_Delivery_Analysis.ipynb` | Main Jupyter Notebook — 20 sections, 68 cells |
| `requirements.txt` | Python dependencies |
| `Project_Report.docx` | Professional project report with tables and structured sections |
| `README.md` | This file |
| `Swiggy Cleaned Data by MKP.xlsx` | Source dataset (unchanged) |

---

## Quick Start

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Place the dataset in the same directory as the notebook

```
Swiggy_Food_Delivery_Analysis.ipynb
Swiggy Cleaned Data by MKP.xlsx
requirements.txt
```

### 3. Launch the notebook

```bash
jupyter notebook Swiggy_Food_Delivery_Analysis.ipynb
```

Or in JupyterLab:

```bash
jupyter lab Swiggy_Food_Delivery_Analysis.ipynb
```

### 4. Run all cells in order

Use **Kernel → Restart & Run All** to execute the full notebook from start to finish.

---

## Dataset Summary

| Attribute | Value |
|---|---|
| Records | 10,000 (after cleaning) |
| Columns | 28 |
| Date range | April 2024 – March 2026 |
| Cities | 12 (Bengaluru, Delhi, Hyderabad, Mumbai, Pune, Chennai, Gurugram, Kolkata, Indore, Ahmedabad, Jaipur, Chandigarh) |
| Restaurants | 20 |
| Cuisine categories | 8 |
| Order value range | ₹120 – ₹2,856 (avg ₹613) |
| Customer rating range | 2.5 – 5.0 (avg 4.64) |
| Delivery distance | 0.8 – 18.0 km (avg 4.42 km) |
| Order status | Delivered 92.4% · Cancelled 5.6% · Refunded 2.1% |
| Late delivery rate | 74.0% (Actual > Estimated delivery time) |
| Top payment mode | UPI (57.1%) |

The dataset was received in **pre-cleaned form**. The cleaning steps (11 steps documented by the dataset author) are reproduced in Section 7 of the notebook and Section 6 of the project report from the workbook's companion sheets.

---

## Notebook Structure (20 Sections)

| # | Section | Content |
|---|---|---|
| 1 | Project Introduction | Background, dataset note, IBM Bob disclosure |
| 2 | Business Problem | Three core operational challenges |
| 3 | Objectives | 12 project objectives |
| 4 | Dataset Overview | 28-column reference table |
| 5 | Data Loading | Excel load, datetime parse, copy strategy |
| 6 | Data Quality Validation | Missing values, duplicates, numeric ranges, categoricals |
| 7 | Existing Cleaning Documentation | Reproduced from workbook — not re-invented |
| 8 | Feature Engineering | `delivery_delay`, `is_late`, temporal, band, and derived features |
| 9 | Exploratory Data Analysis | 11 visualisations covering distributions, trends, geography |
| 10 | Customer Satisfaction Analysis | Rating vs. delay, city, category, segment, demographics |
| 11 | Delivery Performance Analysis | KPIs, delay by city/restaurant/category/distance/time |
| 12 | Order Status & Statistical Analysis | Cancellation rates, Pearson/Spearman/Kruskal-Wallis/Chi-Square |
| 13 | Machine Learning | 3 model pipelines with leakage prevention documentation |
| 14 | Model Evaluation | Accuracy, Precision, Recall, F1, ROC-AUC, Confusion Matrices, ROC curves |
| 15 | Model Interpretation | Feature importance (RF), LR coefficients, grouped importance |
| 16 | IBM Bob AI-Assisted Analysis | 7 documented roles of IBM Bob |
| 17 | Business Insights | 5 finding areas traceable to analysis |
| 18 | Business Recommendations | 8 actionable recommendations with caveats |
| 19 | Limitations | 8 identified limitations |
| 20 | Conclusion | Summary of findings and contribution |

---

## Machine Learning Design

### Target Variable

```python
delivery_delay = Actual Delivery Min - Estimated Delivery Min
is_late = 1 if Actual Delivery Min > Estimated Delivery Min else 0
```

### Target Leakage Prevention

The following variables are **excluded from all model predictors**:

| Excluded Variable | Reason |
|---|---|
| `Actual Delivery Min` | Direct source of `is_late` — would be perfect leakage |
| `delivery_delay` | Derived from `Actual Delivery Min` |
| `Customer Rating` | Only available after delivery completion |
| `delivery_speed_kmpm` | Derived from `Actual Delivery Min` |

`Estimated Delivery Min` **is included** as a predictor — in a real food-delivery system, this estimate is generated by the platform at the time of ordering (before dispatch) and is legitimately available at prediction time.

### Models

| Model | Role | Library |
|---|---|---|
| Logistic Regression | Interpretable baseline | scikit-learn |
| Random Forest Classifier | Primary ensemble model | scikit-learn |
| HistGradientBoosting | Second ensemble | scikit-learn |

### Evaluation Metrics

Accuracy · Precision · Recall · F1 Score · ROC-AUC · Confusion Matrix · ROC Curve

**Why F1/Recall/ROC-AUC matter more than accuracy alone:**
The late delivery rate in this dataset is 74%, meaning a naive "always late" classifier achieves 74% accuracy. Recall and ROC-AUC are therefore more informative. The best model (Random Forest) achieves ROC-AUC = 0.61.

**Actual executed results:**

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Logistic Regression | 0.741 | 0.741 | 1.000 | 0.851 | 0.611 |
| Random Forest | 0.740 | 0.740 | 0.999 | 0.850 | **0.613** |
| HistGradientBoosting | 0.713 | 0.743 | 0.937 | 0.829 | 0.591 |

---

## Key Engineering Features

| Feature | Definition | Use |
|---|---|---|
| `delivery_delay` | Actual − Estimated Delivery Min | EDA + satisfaction analysis |
| `is_late` | Binary: 1 if late, 0 if on time | **ML target** |
| `order_hour` | Hour of day (0–23) | ML + EDA |
| `day_of_week` | 0=Monday, 6=Sunday | ML + EDA |
| `month` | Month (1–12) | EDA seasonality |
| `is_weekend` | Binary weekend flag | ML + EDA |
| `time_of_day` | Morning/Afternoon/Evening/Night | ML + EDA |
| `distance_band` | 4 distance categories | ML + EDA |
| `age_group` | 4 demographic age bands | ML + EDA |
| `order_value_band` | 4 spend categories | EDA |
| `value_per_item` | Order Value / Item Count | ML feature |
| `discount_flag` | Binary: discount applied | ML feature |

---

## IBM Bob — AI-Assisted Analysis Role

IBM Bob was used across seven distinct roles in this project:

1. **Dataset Inspection** — Full workbook profiling before any code was written
2. **Problem Statement Refinement** — Evaluated 8 candidate problems, recommended optimal choice
3. **Target Leakage Detection** — Identified all variables requiring exclusion from predictors
4. **Analytical Structure Design** — Designed the 20-section notebook structure
5. **Code Generation** — Generated all analysis code, ML pipelines, and visualisations
6. **Business Interpretation** — Translated findings into 8 actionable recommendations
7. **Compliance Enforcement** — Enforced data integrity rules, association vs. causation language, cleaning documentation accuracy

---

## Statistical Tests Used

| Test | Applied To |
|---|---|
| Pearson correlation | Delivery delay vs. customer rating; Distance vs. actual delivery time |
| Spearman correlation | Distance vs. actual delivery time (non-parametric confirmation) |
| Kruskal-Wallis | Rating across cities; Delay across restaurant categories |
| Chi-square | Late delivery vs. city association |

---

## Visualisations Generated

28 figures saved as `fig_01_*.png` through `fig_28_*.png`, covering:

- Order value and rating distributions
- Hourly and monthly order volume trends
- City-level orders and average order value
- Restaurant category share (pie chart)
- Payment mode distribution
- Customer segment analysis
- Correlation heatmap (all numeric features)
- Delivery delay distribution and late vs. on-time breakdown
- Rating vs. delivery delay (scatter + box plot)
- Rating by city, category, segment, demographics, value band, distance band
- Delivery delay by city, restaurant, category, distance, time of day, segment, item count
- Distance vs. actual delivery time scatter
- Order status distribution
- Cancellation rates by city, segment, category
- Confusion matrices (3 models)
- ROC curves (3 models)
- Feature importance (Random Forest)
- Feature group importance
- Logistic Regression coefficients

---

## Project Requirements

```
pandas>=2.0.0
numpy>=1.24.0
openpyxl>=3.1.0
matplotlib>=3.7.0
seaborn>=0.12.0
scipy>=1.11.0
scikit-learn>=1.3.0
jupyter>=1.0.0
notebook>=7.0.0
ipykernel>=6.25.0
```

---

## Important Notes

- The original workbook (`Swiggy Cleaned Data by MKP.xlsx`) is **not modified** by this project.
- All dataset facts (column names, value ranges, distributions) were verified by inspecting the actual workbook before any code was written.
- No external data was added. No dataset attributes were invented.
- All findings are stated as **associations**, not causal claims.
- The 8 business recommendations include explicit caveats about validation with production data.

---

*AICTE | IBM SkillsBuild Data Analytics with AI Internship 2026*  
*Conducted by BharatCares in association with AICTE and IBM SkillsBuild*
