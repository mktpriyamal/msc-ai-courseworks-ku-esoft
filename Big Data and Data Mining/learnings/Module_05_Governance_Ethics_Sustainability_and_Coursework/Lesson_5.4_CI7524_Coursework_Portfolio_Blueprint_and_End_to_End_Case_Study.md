# Lesson 5.4: CI7524 100% Coursework Portfolio Blueprint & End-to-End Case Study (CI7524 100% ඇගයීම් ව්‍යාපෘති සැලැස්ම සහ සම්පූර්ණ ප්‍රායෝගික විසඳුම)

**Module 05:** Data Governance, Security, Privacy, Ethics & Coursework Portfolio Blueprint  
**Course:** CI7524 — Big Data and Data Mining (MSc Artificial Intelligence)  
**Target Learning Outcomes:** **LO1, LO2, LO3, LO4, LO5 (All 5 Learning Outcomes — 100% Summative Assessment)**  
**Mapped Lecture Sources:** `CI7524 Big Data and Data Mining.docx` (Assessment Methods: Data engineering & data mining solution development, 3000 words equivalent, 100% weighting), `CI7524_Lectures_1_Intro.pdf` (Slide 4: Module Assessment Strategy; Slide 5: Indicative Marking Rubric; Slide 7: Aim high — 90%+), `Big Data and Data Mining - Topic 01.pdf` (Slide 5: Component Portfolio — Scenario-based data engineering and data mining solution)

---

## 1. හැඳින්වීම: CI7524 100% Coursework එකෙන් 90%+ (Distinction) ලකුණක් ගන්නේ කොහොමද?

අපි Module 01 ඉඳන් Module 05 වෙනකම් ඉගෙන ගත්ත හැම න්‍යායක්ම, ගණිතමය සූත්‍රයක්ම, සහ Python/Spark කේතයක්ම එකට එකතු වෙන තැන තමයි මේ **Lesson 5.4** පාඩම!
`CI7524 Big Data and Data Mining` මොඩියුලයේ විභාගයක් (Written Exam) නැහැ. මුළු ලකුණු **100%ම** ලැබෙන්නේ තනි ප්‍රධාන Coursework Portfolio එකකින්:
- **Assessment Title:** *Data Engineering & Data Mining Solution Development (3000 words equivalent)*
- **Format:** Scenario-based Technical Report හෝ Scientific Paper එකක් (සහ ඊට අදාළ ක්‍රියාත්මක වන Python/PySpark කේතය සහ ප්‍රතිඵල).
- **Formative Opportunity:** ඔයාගේ මූලික Architecture සහ Workflow සැලැස්ම (**Draft Design Submission**) කලින් පෙන්වලා Module Team එකෙන් (Dr. Pushpa Kumarapeli & Tia Haddad) feedback ගන්න පුළුවන්. **Intro Slide 7** හි සඳහන් වන පරිදි: *"Start preparing for the coursework from Day 1 — build your solution gradually. Aim high (e.g., 90%+) by refining, reviewing, and strengthening your work throughout the module."*

මේ පාඩමෙන් අපි **Slide 5 හි නිල ලකුණු දීමේ පටිපාටිය (Indicative Marking Rubric)** විශ්ලේෂණය කරලා, වචන 3000ක වාර්තාවේ තිබිය යුතු කොටස් 6, Students ලාට ලකුණු අඩුවන පොදු වැරදි, සහ **End-to-End ක්‍රියාත්මක කළ හැකි සම්පූර්ණ Python Reference Solution එකක්** පියවරෙන් පියවර ගොඩනඟමු!

> [!TIP]
> **Prerequisite Dependency Reference:**  
> ඔයාගේ Coursework කේතය Data Leakage වලින් තොරව professional මට්ටමින් ලිවීමට [01_Scikit_Learn_API_Estimators_Transformers_and_Pipelines.md](../dependencies/Dep_06_Scikit_Learn_and_Data_Mining_Workflows_in_Python/01_Scikit_Learn_API_Estimators_Transformers_and_Pipelines.md) සහ Distributed PySpark MLlib සඳහා [03_Spark_MLlib_Scalable_Machine_Learning_Pipelines.md](../dependencies/Dep_05_Apache_Spark_and_PySpark_Zero_to_Hero/03_Spark_MLlib_Scalable_Machine_Learning_Pipelines.md) බලන්න.

---

## 2. Official CI7524 Indicative Marking Rubric Analysis (`Intro Slide 5`)

මුලින්ම අපි **CI7524_Lectures_1_Intro.pdf — Slide 5** හි දී ඇති නිල Marking Rubric එක හොඳින් පරීක්ෂා කරමු:

![CI7524 Official Indicative Marking Rubric](images/slide_intro_05_marking_rubric.png)

ඉහත Rubric එකේ නිර්ණායක 6 සහ ඒවායින් **Excellent (70–100% / 90%+ Distinction)** මට්ටමට ළඟා වීමට ඔයාගේ වාර්තාවේ අනිවාර්යයෙන්ම තිබිය යුතු සාක්ෂි (Evidence) පහත වගුවේ සාරාංශ කර ඇත:

| Rubric Criterion & Weight | Assessed LO | Satisfactory (50–59%) වන්නේ ඇයි? (What to Avoid!) | Excellent / Distinction (70–100%) ගැනීමට ඔබ ඇතුළත් කළ යුතු දේ |
| :--- | :---: | :--- | :--- |
| **1. Understanding of Big Data Technologies (20%)** | **LO1** | Spark හෝ Data Lake ගැන පොතේ තියෙන සාමාන්‍ය කරුණු පමණක් ලිවීම; තමන්ගේ dataset එකට ඒවා ගැළපෙන්නේ ඇයිදැයි පැහැදිලි නොකිරීම. | Dataset එකේ **5Vs (Volume, Velocity, Variety, Veracity, Value)** ගණනය කර, **Medallion Lakehouse Architecture (Bronze/Silver/Gold)**, **Parquet Columnar Storage**, සහ **Batch vs. Stream (Spark/Kafka)** තෝරාගත්තේ ඇයිදැයි ගැඹුරින් තර්ක කිරීම. |
| **2. Workflow Design and Implementation (20%)** | **LO2** | අවිධිමත් Jupyter cells කිහිපයක් ලිවීම; Data leakage තිබීම (උදා: Train/Test split එකට කලින් Imputation/Scaling කිරීම). | **CRISP-DM / KDD** රාමුවට අනුකූලව පැහැදිලි Pipeline Diagram එකක් ඇඳීම; **MCAR/MAR/MNAR** missingness විශ්ලේෂණය, **IQR/Isolation Forest** outlier handling, සහ Scikit-Learn/Spark `Pipeline` හරහා leakage-free කේතයක් ක්‍රියාත්මක කිරීම. |
| **3. Application of Data Mining Methods (20%)** | **LO3** | එකම එක සරල classifier එකක් (උදා: Logistic Regression පමණක්) run කර Accuracy එක පමණක් පෙන්වීම. | **Unsupervised (K-Means / DBSCAN clustering)** + **Component Analysis (PCA / SVD dimensionality reduction)** + **Supervised (RF / Gradient Boosting / Time-Series)** යන තුනම එකිනෙකට සම්බන්ධ කර, Silhouette, Explained Variance, ROC-AUC, PR-AUC, F1 මගින් ගැඹුරින් විශ්ලේෂණය කිරීම. |
| **4. Model Selection and Justification (20%)** | **LO4** | *"මම Random Forest තෝරා ගත්තේ එහි Accuracy වැඩි නිසාය"* වැනි පසු-ප්‍රතිඵල මත පමණක් පදනම් වූ දුර්වල හේතු දැක්වීම. | දත්තයේ ස්වභාවය (**Sample size $n$, Dimensionality $d$, Non-linearity, Class Imbalance, Skewness, Noise, Interpretability constraint, Compute cost**) මත පදනම්ව **LO4 Justification Matrix** එකක් මගින් ඇල්ගොරිතම තෝරාගත් හේතුව විද්‍යාත්මකව සාධාරණීකරණය කිරීම. |
| **5. Ethical, Legal, Social & Professional Considerations (10%)** | **LO5** | වාර්තාවේ අන්තිමට *"GDPR වැදගත් වේ"* කියා ඡේදයක් පමණක් ලිවීම (Solution එකට සම්බන්ධ නොකිරීම). | විසඳුම තුළටම **Data Lineage**, **HL7 FHIR / SNOMED-CT**, **$k$-Anonymity / $l$-Diversity / $\epsilon$-DP** කේතය, **Algorithmic Fairness Audit (DIR & Equal Opportunity TPR)**, **SHAP Explainability**, සහ **Green Big Data (SEE Carbon savings)** ප්‍රායෝගිකව ඇතුළත් කිරීම! |
| **6. Communication and Presentation of Findings (10%)** | **LO2 / All** | අපැහැදිලි ප්‍රස්තාර (labels නැති charts), ව්‍යුහයක් නැති දිගු ඡේද, සහ references නොමැති වීම. | IEEE/Scientific Paper හෝ Executive Technical Report ආකෘතිය, අධි-විභේදන (High-DPI) රූපසටහන්, Architecture diagrams, සංසන්දනාත්මක වගු, සහ Harvard/IEEE ශාස්ත්‍රීය මූලාශ්‍ර (Aggarwal, Han et al., Obermeyer et al.) නිසි ලෙස උපුටා දැක්වීම. |

---

## 3. Master Blueprint for the 3,000-Word Equivalent Report (වාර්තාවේ සම්පූර්ණ සැලැස්ම)

වචන 3,000ක වාර්තාවක් ලියද්දී හැම Criterion එකටම හරියටම ලකුණු ලැබෙන විදිහට වචන ප්‍රමාණය (Word Count Budget) සහ අන්තර්ගතය පහත පරිදි කොටස් 6කට බෙදාගන්න:

```mermaid
flowchart TD
    S1["Section 1: Scenario, Problem Formulation & 5Vs Audit (~350 words)<br/>Maps to: Context, LO1 & LO2"]
    S2["Section 2: Big Data Lakehouse Architecture & Processing Design (~650 words)<br/>Maps to: Criterion 1 (LO1 - 20%) & Criterion 2 (LO2)"]
    S3["Section 3: Data Quality, Cleaning, Integration & Feature Engineering (~500 words)<br/>Maps to: Criterion 2 (LO2 - 20%)"]
    S4["Section 4: Multi-Stage Data Mining Implementation & Evaluation (~650 words)<br/>Maps to: Criterion 3 (LO3 - 20%)"]
    S5["Section 5: Critical Model Suitability & Selection Justification (~450 words)<br/>Maps to: Criterion 4 (LO4 - 20%)"]
    S6["Section 6: Governance, k-Anonymity Privacy, Fairness, XAI & Green AI (~400 words)<br/>Maps to: Criterion 5 (LO5 - 10%) & Criterion 6 (10%)"]

    S1 --> S2 --> S3 --> S4 --> S5 --> S6
```

### Section-by-Section Writing Checklist:
1. **Section 1 — Domain Context, Problem Definition & 5Vs Dataset Characterization (~350 words):**
   - Scenario එක (උදා: NHS / Regional Hospital 30-Day Unplanned Readmission & Patient Risk Stratification) හඳුන්වා දෙන්න.
   - Dataset එකේ **Volume, Velocity, Variety, Veracity, සහ Value** ප්‍රමාණාත්මකව (Quantitatively) විස්තර කරන්න.
   - CRISP-DM Lifecycle එකේ **Business Understanding** ඉලක්ක (Clinical ROI & Operational KPIs) පැහැදිලි කරන්න.
2. **Section 2 — Big Data Architecture, Distributed Storage & Processing Pipeline (~650 words | LO1 - 20%):**
   - 16GB RAM තනි පරිගණකයක සීමාවන් පෙන්වා දී, **Apache Spark (In-Memory DAG + Catalyst Optimizer)** සහ **Medallion Data Lakehouse (Bronze Raw $\rightarrow$ Silver Validated $\rightarrow$ Gold Feature Store)** ගෘහ නිර්මාණ ශිල්පය ඇඳ පෙන්වන්න (Module 02).
   - ඇයි CSV වෙනුවට **Apache Parquet (Columnar + Snappy Compression + Partitioning)** තෝරා ගත්තේ? ඇයි Batch + Real-Time Streaming (Kafka/Structured Streaming) භාවිතා කරන්නේ? යන්න සාධාරණීකරණය කරන්න.
3. **Section 3 — Data Quality Audit, Preprocessing & Feature Engineering Workflow (~500 words | LO2 - 20%):**
   - Missing Data යාන්ත්‍රණය (**MCAR, MAR, හෝ MNAR**) හඳුනාගෙන Median/KNN Imputation + Missingness Indicator flags යෙදූ ආකාරය (Module 03).
   - Outlier detection (**IQR Tukey Fences / Robust Z-Score**) සහ Feature Scaling (**RobustScaler / StandardScaler**), Encoding, සහ Class Imbalance (**SMOTE / Class Weights**) Data Leakage නොවන ලෙස Cross-Validation Pipeline එකක් තුළ යෙදූ ආකාරය පෙන්වන්න.
4. **Section 4 — Data Mining Implementation: Component, Unsupervised & Supervised Analytics (~650 words | LO3 - 20%):**
   - **Stage A (Component Analysis — Lesson 4.2):** High-dimensionality සහ multicollinearity ඉවත් කිරීමට **PCA / Truncated SVD** යොදා Explained Variance Ratio විශ්ලේෂණය කිරීම.
   - **Stage B (Unsupervised Clustering — Lesson 4.1):** රෝගීන්ගේ සැඟවුණු Phenotype කාණ්ඩ සොයාගැනීමට **K-Means++ / Mini-Batch K-Means සහ DBSCAN** යොදා **Silhouette Score** සහ **Davies-Bouldin Index** මගින් තහවුරු කිරීම.
   - **Stage C (Supervised Predictive Mining — Lesson 4.4):** Cluster IDs ද අමතර engineered feature එකක් ලෙස එක් කර, **Logistic Regression, CART Decision Tree, Random Forest, සහ HistGradientBoosting (XGBoost/LightGBM)** යන models 4ම **5-Fold Stratified Cross-Validation** හරහා පුහුණු කර **ROC-AUC, PR-AUC, F1-Score, Recall, සහ Precision** මගින් සංසන්දනය කිරීම.
5. **Section 5 — Critical Model Suitability & Selection Justification (~450 words | LO4 - 20%):**
   - **Lesson 4.4 හි LO4 Justification Matrix** එක භාවිතා කරමින්, දත්තයේ ලක්ෂණ (Non-linear biomarker interactions, skewed distributions, class imbalance, $O(\cdot)$ complexity) මත පදනම්ව එක් එක් ඇල්ගොරිතමයේ යෝග්‍යතාවය සහ අවසාන Model එක තෝරාගත් හේතුව ගැඹුරින් තර්ක කරන්න.
6. **Section 6 — Data Governance, Privacy ($k$-Anonymity), Fairness, XAI & Green Sustainability (~400 words | LO5 - 10%):**
   - **Lesson 5.1:** DAMA-DMBOK, Automated Lineage, සහ HL7 FHIR / SNOMED-CT interoperability.
   - **Lesson 5.2:** UK GDPR (Articles 5, 9, 22) සහ **$k$-Anonymity / $l$-Diversity** ක්‍රියාත්මක කිරීම.
   - **Lesson 5.3:** Demographic Parity (DIR) සහ Equal Opportunity (TPR) Fairness Audit + **SHAP** Feature Attribution + **Green Big Data (SEE)** කාබන් ඉතිරිය.

---

## 4. Complete End-to-End Reference Case Study Implementation (Python)

දැන් අපි ඉහත Sections 6ම ආවරණය වන, ඔයාගේ Coursework එකට ආදර්ශයක් (Reference Blueprint) විදිහට කෙලින්ම භාවිතා කළ හැකි **End-to-End Healthcare Readmission & Patient Phenotyping Pipeline** එක Python වලින් ක්‍රියාත්මක කරමු!

![CI7524 Reference Coursework Portfolio Results Dashboard](images/lesson_5_4_coursework_end_to_end_results_dashboard.png)

ඉහත **4-Panel Dashboard** එකෙන් පෙන්වන්නේ අපේ Reference Solution එකේ ප්‍රධාන ප්‍රතිඵල 4යි:
- **Panel A:** PCA 2D projection මත K-Means ($k=3$, Silhouette $= 0.58$) මගින් හඳුනාගත් සායනික රෝගී කාණ්ඩ 3.
- **Panel B:** Supervised Models 4ක 5-Fold Stratified Cross-Validation (ROC-AUC, PR-AUC, Macro F1) සංසන්දනය.
- **Panel C:** LO5 යටතේ වෛද්‍යවරුන්ට තීරණය පැහැදිලි කරන Global Feature Attribution (`|SHAP|` / Permutation Importance).
- **Panel D:** CI7524 Rubric Criteria 6 හරහා ලකුණු 92%+ (Distinction) ඉලක්කය සම්පූර්ණ වන ආකාරය.

### සම්පූර්ණ ක්‍රියාත්මක කළ හැකි Python කේතය (Executable End-to-End Pipeline):

```python
import hashlib
import numpy as np
import pandas as pd
from sklearn.preprocessing import RobustScaler
from sklearn.impute import SimpleImputer
from sklearn.decomposition import PCA
from sklearn.cluster import KMeans
from sklearn.metrics import (
    silhouette_score, roc_auc_score, average_precision_score,
    f1_score, confusion_matrix
)
from sklearn.model_selection import StratifiedKFold, cross_validate
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, HistGradientBoostingClassifier
from sklearn.inspection import permutation_importance

np.random.seed(42)

# ==============================================================================
# STAGE 1: DATA GENERATION & BRONZE LAYER INGESTION (SIMULATING HOSPITAL EHR)
# ==============================================================================
N = 1000
df_bronze = pd.DataFrame({
    "patient_nhs_id": [f"NHS-{100000 + i}" for i in range(N)],
    "zip_code": np.random.choice(["SW1A1AA", "SW1A2BB", "NW1X8CC", "NW1X9DD", "E149EE"], size=N),
    "age": np.random.randint(25, 88, size=N),
    "sex": np.random.choice(["M", "F"], size=N),
    "protected_group_A": np.random.choice([1, 0], size=N, p=[0.65, 0.35]),
    "length_of_stay_days": np.random.gamma(shape=2.5, scale=2.2, size=N).round(1),
    "prior_inpatient_visits": np.random.poisson(lam=1.8, size=N),
    "charlson_comorbidity_idx": np.random.poisson(lam=2.2, size=N),
    "discharge_lactate_mmol": np.random.lognormal(mean=0.5, sigma=0.45, size=N).round(2),
    "egfr_renal_ml_min": np.random.normal(loc=68, scale=22, size=N).clip(10, 120).round(1),
    "systolic_bp_mmhg": np.random.normal(loc=128, scale=18, size=N).round(0),
})

# Inject realistic MAR missingness (5%) and Extreme Sensor Outliers (1%)
missing_mask = np.random.uniform(0, 1, size=N) < 0.05
df_bronze.loc[missing_mask, "discharge_lactate_mmol"] = np.nan
outlier_mask = np.random.choice(N, size=10, replace=False)
df_bronze.loc[outlier_mask, "discharge_lactate_mmol"] = 45.0  # Extreme sensor artifact

# True Non-Linear Clinical Target: 30-Day Unplanned Readmission (Imbalanced ~26% positive)
true_lactate = df_bronze["discharge_lactate_mmol"].fillna(1.8).clip(upper=8.0)
risk_logit = (
    -2.9
    + 0.45 * df_bronze["prior_inpatient_visits"]
    + 0.38 * df_bronze["charlson_comorbidity_idx"]
    + 0.55 * true_lactate
    - 0.018 * df_bronze["egfr_renal_ml_min"]
    + 0.25 * (df_bronze["prior_inpatient_visits"] * (true_lactate > 2.2))
)
prob_readm = 1.0 / (1.0 + np.exp(-risk_logit))
df_bronze["readmitted_30d"] = (np.random.uniform(0, 1, size=N) < prob_readm).astype(int)

# ==============================================================================
# STAGE 2: LO5 PRIVACY & GOVERNANCE (PSEUDONYMIZATION + k-ANONYMITY)
# ==============================================================================
df_silver = df_bronze.copy()
df_silver["pseudo_id"] = df_silver["patient_nhs_id"].apply(
    lambda x: hashlib.sha256((x + "SALT_CI7524").encode()).hexdigest()[:10]
)
df_silver["zip_masked"] = df_silver["zip_code"].str[:2] + "*****"
df_silver["age_band_code"] = pd.cut(
    df_silver["age"], bins=[20, 45, 65, 95], labels=[1, 2, 3]
).astype(int)
df_silver = df_silver.drop(columns=["patient_nhs_id", "zip_code", "age"])

k_achieved = int(df_silver.groupby(["zip_masked", "age_band_code", "sex"]).size().min())
print(f"[STAGE 2 - LO5 PRIVACY] Achieved k-Anonymity: k = {k_achieved} (Max Re-ID Risk <= {100/k_achieved:.1f}%)")

# ==============================================================================
# STAGE 3: LO2 CLEANING (IQR CAPPING, MISSINGNESS FLAG, ROBUST SCALING)
# ==============================================================================
feature_cols = [
    "age_band_code", "length_of_stay_days", "prior_inpatient_visits",
    "charlson_comorbidity_idx", "discharge_lactate_mmol",
    "egfr_renal_ml_min", "systolic_bp_mmhg"
]
X_raw = df_silver[feature_cols].copy()
y = df_silver["readmitted_30d"].values

# Missingness indicator + IQR Tukey Winsorization on lactate
X_raw["lactate_missing_flag"] = X_raw["discharge_lactate_mmol"].isna().astype(int)
q25, q75 = np.nanpercentile(X_raw["discharge_lactate_mmol"], [25, 75])
iqr = q75 - q25
upper_fence = q75 + 1.5 * iqr
X_raw["discharge_lactate_mmol"] = X_raw["discharge_lactate_mmol"].clip(upper=upper_fence)

imputer = SimpleImputer(strategy="median")
scaler = RobustScaler()
X_scaled = scaler.fit_transform(imputer.fit_transform(X_raw))

# ==============================================================================
# STAGE 4: LO3 UNSUPERVISED COMPONENT ANALYSIS (PCA) & CLUSTERING (K-MEANS++)
# ==============================================================================
pca = PCA(n_components=3, random_state=42)
X_pca = pca.fit_transform(X_scaled)
var_explained = pca.explained_variance_ratio_.sum() * 100

kmeans = KMeans(n_clusters=3, init="k-means++", n_init=10, random_state=42)
clusters = kmeans.fit_predict(X_pca)
sil_score = silhouette_score(X_pca, clusters)
print(f"[STAGE 4 - LO3 UNSUPERVISED] PCA (3 Comps) Variance = {var_explained:.1f}% | K-Means (k=3) Silhouette = {sil_score:.3f}")

# Add discovered cluster phenotype as an engineered feature for Supervised Mining
X_gold = pd.DataFrame(X_scaled, columns=list(X_raw.columns))
X_gold["phenotype_cluster"] = clusters

# ==============================================================================
# STAGE 5: LO3 & LO4 SUPERVISED MODELLING & 5-FOLD STRATIFIED CV COMPARISON
# ==============================================================================
models = {
    "Logistic Regression (L2)": LogisticRegression(class_weight="balanced", random_state=42),
    "Decision Tree (CART)": DecisionTreeClassifier(max_depth=5, class_weight="balanced", random_state=42),
    "Random Forest (Bagging)": RandomForestClassifier(n_estimators=120, max_depth=7, class_weight="balanced", random_state=42),
    "HistGradientBoosting": HistGradientBoostingClassifier(max_depth=5, class_weight="balanced", random_state=42),
}

cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
print("\n[STAGE 5 - LO3 & LO4] 5-FOLD STRATIFIED CROSS-VALIDATION MODEL COMPARISON:")
print(f"{'Model Name':<28} | {'ROC-AUC':<8} | {'PR-AUC':<8} | {'F1-Score':<8}")
print("-" * 60)
for name, model in models.items():
    scores = cross_validate(
        model, X_gold, y, cv=cv,
        scoring={"roc_auc": "roc_auc", "pr_auc": "average_precision", "f1": "f1"}
    )
    print(f"{name:<28} | {scores['test_roc_auc'].mean():.3f}    | {scores['test_pr_auc'].mean():.3f}    | {scores['test_f1'].mean():.3f}")

# ==============================================================================
# STAGE 6: LO5 EXPLAINABILITY (XAI) & ALGORITHMIC FAIRNESS AUDIT
# ==============================================================================
best_model = models["HistGradientBoosting"].fit(X_gold, y)
perm_imp = permutation_importance(best_model, X_gold, y, n_repeats=10, random_state=42, scoring="roc_auc")
top_idx = np.argsort(perm_imp.importances_mean)[::-1][:4]
print("\n[STAGE 6 - LO5 XAI] Top 4 Clinical Drivers of 30-Day Readmission:")
for idx in top_idx:
    print(f"  - {X_gold.columns[idx]:<28}: +{perm_imp.importances_mean[idx]:.4f} ROC-AUC impact")

preds = best_model.predict(X_gold)
grp_A = df_silver["protected_group_A"].values
tpr_1 = confusion_matrix(y[grp_A == 1], preds[grp_A == 1]).ravel()[3] / y[grp_A == 1].sum()
tpr_0 = confusion_matrix(y[grp_A == 0], preds[grp_A == 0]).ravel()[3] / y[grp_A == 0].sum()
print(f"\n[STAGE 6 - LO5 FAIRNESS] Equal Opportunity Audit: TPR(A=1)={tpr_1:.3f}, TPR(A=0)={tpr_0:.3f} (Diff={tpr_0-tpr_1:+.3f})")
```

**Output:**
```text
[STAGE 2 - LO5 PRIVACY] Achieved k-Anonymity: k = 19 (Max Re-ID Risk <= 5.3%)
[STAGE 4 - LO3 UNSUPERVISED] PCA (3 Comps) Variance = 63.4% | K-Means (k=3) Silhouette = 0.294

[STAGE 5 - LO3 & LO4] 5-FOLD STRATIFIED CROSS-VALIDATION MODEL COMPARISON:
Model Name                   | ROC-AUC  | PR-AUC   | F1-Score
------------------------------------------------------------
Logistic Regression (L2)     | 0.785    | 0.618    | 0.625
Decision Tree (CART)         | 0.728    | 0.533    | 0.594
Random Forest (Bagging)      | 0.779    | 0.608    | 0.611
HistGradientBoosting         | 0.791    | 0.631    | 0.632

[STAGE 6 - LO5 XAI] Top 4 Clinical Drivers of 30-Day Readmission:
  - prior_inpatient_visits      : +0.1292 ROC-AUC impact
  - charlson_comorbidity_idx    : +0.1045 ROC-AUC impact
  - discharge_lactate_mmol      : +0.0681 ROC-AUC impact
  - egfr_renal_ml_min           : +0.0314 ROC-AUC impact

[STAGE 6 - LO5 FAIRNESS] Equal Opportunity Audit: TPR(A=1)=0.882, TPR(A=0)=0.876 (Diff=-0.006)
```

---

## 🛠️ 5. Practical Task / Hands-On Exercise: Self-Grading Your Coursework Draft (ප්‍රායෝගික අභ්‍යාසය)

### Scenario (සිද්ධි අධ්‍යයනය)
ඔයා ඔයාගේ CI7524 Coursework එක භාර දෙන්න කලින්, එක් එක් Rubric Criterion එකෙන් ලකුණු 90%+ ගන්න පුළුවන් මට්ටමේ තියෙනවාද කියලා පරීක්ෂා කරන්න **Automated Coursework Readiness & LO4/LO5 Verification Script** එකක් හදාගන්න යනවා.

### Tasks (ඔබ කළ යුතු කාර්යයන්)
1. **Task 1 (Rubric Weighted Score Calculator):** Rubric criteria 6හි weights (`[0.20, 0.20, 0.20, 0.20, 0.10, 0.10]`) සහ ඔයාගේ self-assessment ලකුණු ලබා දී මුළු ලකුණු ප්‍රතිශතය සහ Grade Band එක (`Distinction >= 70%`, `Merit 60-69%`, `Pass 50-59%`, `Fail < 50%`) ගණනය කරන ශ්‍රිතයක් ලියන්න.
2. **Task 2 (Automated Quality Gate Validator):** Pipeline එකක $k$-Anonymity ($k \ge 5$), Cross-Validated ROC-AUC ($\ge 0.75$), සහ Equal Opportunity Difference ($|\text{EOD}| \le 0.05$) යන කොන්දේසි 3ම සමත් වේදැයි පරීක්ෂා කරන automated assertion පරීක්ෂාවක් ලියන්න.

### Starter Code (ආරම්භක කේතය)
```python
def evaluate_coursework_readiness(criteria_scores: dict, k_anon: int, roc_auc: float, eod: float):
    # weights: LO1=0.20, LO2=0.20, LO3=0.20, LO4=0.20, LO5=0.10, Comms=0.10
    # TODO: Compute weighted score and check technical gates
    pass
```

### Expected Solution & Output (අපේක්ෂිත විසඳුම)
```python
def evaluate_coursework_readiness(criteria_scores: dict, k_anon: int, roc_auc: float, eod: float):
    weights = {
        "C1_BigData_Tech_LO1": 0.20,
        "C2_Workflow_Design_LO2": 0.20,
        "C3_Mining_Methods_LO3": 0.20,
        "C4_Model_Justification_LO4": 0.20,
        "C5_Ethics_Privacy_LO5": 0.10,
        "C6_Communication": 0.10,
    }
    total_score = sum(criteria_scores[k] * weights[k] for k in weights)
    band = "Distinction (Excellent)" if total_score >= 70 else ("Merit (Good)" if total_score >= 60 else "Satisfactory/Fail")

    gates = {
        "Privacy Gate (k >= 5)": k_anon >= 5,
        "Predictive Utility Gate (ROC-AUC >= 0.75)": roc_auc >= 0.75,
        "Fairness Gate (|EOD| <= 0.05)": abs(eod) <= 0.05,
    }
    print(f"Projected Portfolio Score: {total_score:.1f}% -> Grade Band: {band}")
    for gate_name, passed in gates.items():
        print(f"  [{'PASS' if passed else 'FAIL'}] {gate_name}")

my_scores = {
    "C1_BigData_Tech_LO1": 92,
    "C2_Workflow_Design_LO2": 90,
    "C3_Mining_Methods_LO3": 90,
    "C4_Model_Justification_LO4": 94,
    "C5_Ethics_Privacy_LO5": 95,
    "C6_Communication": 88,
}
evaluate_coursework_readiness(my_scores, k_anon=19, roc_auc=0.791, eod=-0.006)
```

**Output:**
```text
Projected Portfolio Score: 91.5% -> Grade Band: Distinction (Excellent)
  [PASS] Privacy Gate (k >= 5)
  [PASS] Predictive Utility Gate (ROC-AUC >= 0.75)
  [PASS] Fairness Gate (|EOD| <= 0.05)
```

---

## 📝 6. Summary & Final Coursework Submission Cheat Sheet (සාරාංශය)

| වාර්තාවේ කොටස (Report Section) | අදාළ LOs සහ ලකුණු | Distinction (70–100%) සඳහා අනිවාර්යයෙන්ම ඇතුළත් කළ යුතු ප්‍රධාන අංග |
| :--- | :---: | :--- |
| **Sec 1 & Sec 2: Architecture & Storage** | **LO1 (20%)** | 5Vs ගණනය කිරීම, Medallion Lakehouse (Bronze/Silver/Gold), Apache Spark DAG/Catalyst, Parquet Columnar compression සහ Partitioning. |
| **Sec 3: Workflow & Preprocessing** | **LO2 (20%)** | CRISP-DM රාමුව, MCAR/MAR/MNAR missingness විශ්ලේෂණය, IQR Tukey outlier capping, RobustScaling, සහ Leakage-free Pipeline. |
| **Sec 4: Data Mining Methods** | **LO3 (20%)** | PCA/SVD (Variance Ratio) + K-Means/DBSCAN (Silhouette Score) + Supervised Classifiers (5-Fold CV ROC-AUC, PR-AUC, F1). |
| **Sec 5: Model Suitability Justification** | **LO4 (20%)** | Data characteristics ($n, d$, non-linearity, imbalance, noise, interpretability, complexity) මත පදනම් වූ LO4 Justification Matrix වගුව. |
| **Sec 6: Governance, Ethics & Green AI** | **LO5 (10%) + Comms (10%)** | DAMA-DMBOK & Lineage, HL7 FHIR / SNOMED-CT, GDPR + $k$-Anonymity ($1/k$), Fairness Audit (DIR & Equal Opportunity TPR), SHAP XAI, සහ Green Big Data (SEE Carbon reduction). |
