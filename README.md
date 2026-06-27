# 🚀 Solid Rocket Propellant Performance Classification

**Comparing Random Forest, SVM, and KNN for Propulsion Quality Prediction**

> Data Mining Course Project — MB0038-1  
> Bora Koşalay · Student ID: 2300006194

---

## 📌 Overview

Solid rocket propellants remain a critical technology in both defense and space exploration, offering lower production costs and simpler manufacturing compared to liquid-fueled alternatives. However, the challenge lies in producing *high-quality* propellant consistently.

This project applies machine learning classification techniques to a solid rocket propellant dataset to identify which chemical and physical parameters most strongly determine propellant performance quality. Three classifiers — **Random Forest**, **Support Vector Machine (SVM)**, and **K-Nearest Neighbors (KNN)** — are trained, evaluated, and compared.

---

## 🧪 Dataset

- **Source:** [Kaggle — Solid Rocket Propellant Burning Rate Data](https://www.kaggle.com/datasets/ranjeetsingh7/solid-rocket-propellant-data-burning-rate)
- **Observations:** 137 samples
- **Features:** 18 variables

### Feature Groups

| Category | Features |
|---|---|
| High-energy oxidizers | NC+NG (Nitrocellulose + Nitroglycerin), RDX, HMX, CL-20 |
| Metal additives | nAl (nano), mAl (micro), Al (raw), nAlN, Al₂O₃, nNi |
| Plasticizers & stabilizers | DEP (Diethyl Phthalate), nDPN (n-Diphenylamine) |
| Combustion regulators | Catalyst, Burning rate inhibitor |
| Measurements | Pressure (MPa), Burning rate (mm/s) |

### Custom Target Variable

A novel **Propulsion Quality** metric was derived to convert this regression dataset into a classification problem:

```
Propulsion Quality = Burning Rate / Pressure  →  min-max normalized to [0, 1]
```

This was then divided into three balanced classes using the 33rd and 67th percentiles:

| Class | Propulsion Quality | Count |
|---|---|---|
| Low (0) | < 0.167 | 45 |
| Med (1) | 0.167 – 0.327 | 47 |
| High (2) | > 0.327 | 45 |

---

## 📁 Repository Structure

```
├── Rocket_Propellant_Analysis.ipynb   # Full analysis notebook
├── Kati_Yakiti_Performans_Siniflandirmasi.pdf  # Project report (Turkish)
└── README.md
```

---

## ⚙️ Methodology

### 1. Data Preprocessing
- Null value check (no missing values found)
- Label encoding: Low → 0, Med → 1, High → 2
- Propulsion Quality and Propulsion Class removed from feature inputs

### 2. Exploratory Data Analysis (EDA)
- Correlation heatmap
- Feature distribution histograms
- Per-class boxplots
- Class distribution bar chart

### 3. Train / Test Split & Scaling
- 80% training / 20% test split (stratified)
- `StandardScaler` fitted **only on training data** to prevent data leakage

### 4. Models

| Model | Key Parameters |
|---|---|
| Random Forest | `max_depth=5`, `min_samples_leaf=5`, `n_estimators=100` |
| SVM | Default `SVC` with `probability=True` |
| KNN | `k=7` (selected by cross-validating k=1..15; upper bound ≈ √137 ≈ 11.7) |

### 5. Evaluation Metrics
- Accuracy, F1-Score (macro), Recall/Sensitivity, Precision, G-Score

### 6. Interpretability
- **LIME** (Local Interpretable Model-agnostic Explanations) — chosen over SHAP due to better performance on small datasets and non-tree models
- **Venn diagram** — visualizing feature overlap across models' top-5 important features

---

## 📊 Results

### Model Performance (Test Set)

| Model | Accuracy | F1-Score | Recall | G-Score |
|---|---|---|---|---|
| Random Forest | **0.643** | **0.639** | **0.652** | **0.487** |
| SVM | 0.571 | 0.561 | 0.581 | 0.385 |
| KNN (k=7) | 0.536 | 0.511 | 0.548 | 0.314 |

**Random Forest achieved the best test performance across all metrics.**

### Key Observations

- All models show a **train–test accuracy gap**, expected given the small dataset size (n=137)
- The **Med class** (class 1) was the hardest to predict across all models — likely due to its overlapping characteristics with Low and High
- The **Low class** (class 0) was consistently the easiest to classify correctly

---

## 🔍 Feature Importance (LIME)

Top-5 features per model:

| Rank | Random Forest | SVM | KNN |
|---|---|---|---|
| 1 | Pressure, MPa | nDPN, % | nDPN, % |
| 2 | Burning rate inhibitor | Pressure, MPa | Pressure, MPa |
| 3 | Burning rate, mm/sec | HMX, % | CL-20 |
| 4 | NC+NG, % | Al₂O₃, % | Burning rate inhibitor |
| 5 | others, % | mAl, % | Al |

### Venn Diagram Intersection

- **Pressure (MPa)** is the only feature in the top-5 of all three models simultaneously — confirming it as the single most decisive variable for propellant classification.
- **nDPN** appears in both SVM and KNN top features.
- Random Forest and SVM each have 3 unique features; KNN shares the most overlap with the other two.

This finding aligns with combustion science literature: operating pressure is a fundamental driver of solid propellant burning behavior.

---

## 🔗 References

- [Kaggle Dataset — Solid Rocket Propellant Burning Rate](https://www.kaggle.com/datasets/ranjeetsingh7/solid-rocket-propellant-data-burning-rate)
- [ML-based burning rate prediction (Wiley/Propellants Journal)](https://onlinelibrary.wiley.com/doi/full/10.1002/prep.202200267)
- [Effect of aluminum content on burning rate (ScienceDirect)](https://www.sciencedirect.com/science/article/abs/pii/S0016236113004456)

---

## 🛠️ Dependencies

```bash
pip install pandas numpy matplotlib seaborn scikit-learn lime matplotlib-venn openpyxl
```

---

## 📄 License

This project was developed as an academic assignment. The dataset is sourced from Kaggle under its respective license. All analysis code is available for educational use.
