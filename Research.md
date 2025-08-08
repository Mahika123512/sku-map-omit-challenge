

# 🛡️ KlinePro Product Map/Omit Challenge — Team 3: *The Inference Club*

This folder presents the **full research journey, data engineering rationale, modeling experiments, and conclusions** for the Map/Omit challenge, contributed by Team 3: *The Inference Club*.

***

## 🧩 Challenge Overview

The core aim was to **classify incoming vendor SKUs as either "Map" (add to catalog) or "Omit" (exclude)**—using only product-side source data. This demanded methods for deduplication, robust sampling, model fairness, and true production alignment.

***
🔄 Pipeline Flowchart
Below is a visual overview of the key processes and decision points in our Map/Omit pipeline, including all data engineering, sampling, modeling, and reporting steps:

<img width="530" height="797" alt="image" src="https://github.com/user-attachments/assets/248f7311-4579-4d2e-9f5e-51239c3740c0" />

***
## 🔬 Research Process & Key Insights

### 1. **Data Audit & Initial Problem Diagnosis**

- Source data exhibited severe **class imbalance** (Omit dominated Map in raw records, with >1 million Omit and ~300k Map).
- **Most Omit SKUs are singleton** (1 example only), while Map SKUs can have hundreds to hundreds of thousands of rows in the raw data.
- Early approach risked model bias, overfitting, and lack of SKU diversity.

***

### 2. **Composite Key Engineering**

- Created composite keys from `SourceMasterBrand`, `SourceBrand`, `SourceSubBrand`, `SourceCategory`, `SourceSubcategory`, and first 100 characters of `SourceDescription` for both Map and Omit.
- This sharply increased **SKU diversity**: most Map and Omit "composite keys" now had exactly one or two rows (long-tail distribution).

***

### 3. **Visualization of SKU Distribution**

- To visualize the data skew and justify our sampling/deduplication strategy, we plotted the SKU example count distribution for both classes:

#### Example Omit SKU Count Distribution:
<img width="902" height="576" alt="image" src="https://github.com/user-attachments/assets/cada5519-576d-49e0-8ed5-9d163a5a3f3a" />


#### Example Map SKU Count Distribution:
<img width="582" height="377" alt="image" src="https://github.com/user-attachments/assets/65b96036-df8a-45a9-885f-5d4945683431" />

Key Insights:

Omit: Almost all Omit composite keys are unique or appear once, prompting a 1-per-key sampling approach.

Map: Some Map SKUs are extremely frequent, necessitating a cap on per-SKU samples to ensure equitable model learning.


***

### 4. **Per-SKU Sampling and Class Balancing**

- **Map records:** For each composite key, sampled up to N=10 per SKU to avoid bias from "hot" SKUs.
- **Omit records:** By nature, most are singletons; always took one per key.
- Analysis revealed per-key capping produces a dataset where **>70% of Map and >75% of Omit SKUs appear only once**—maximizing product coverage.

***

### 5. **Stratified Downsampling with Guaranteed SKU Coverage**

- Used a method that **guarantees every SKU key gets at least one row** in the final set, even after downsampling to achieve a specific class ratio:
  - Final Map sample: 1.5× Omit count
  - Every SKU represented at least once

***

### 6. **Modeling Pipeline**

- **Feature engineering:** Included normalized brand, description, category/subcategory, size/unit, and barcode features.
- **Text embedding:** Used transformer embeddings (`all-MiniLM-L6-v2`) for product text.
- **Numeric features:** Category indices, barcode flag, etc., scaled and concatenated to embeddings.
- **Model:** Logistic regression with `class_weight='balanced'`, trained on deduped and class-balanced sets.
- **Validation:** Used a held-out set, with audit reporting of predictions, confidence, and correctness.

***

### 7. **Findings from Model Experiments**

- Models trained on **deduplicated, stratified, and balanced data** avoided bias, improved generalization, and gave fair Map/Omit treatment to long-tail SKUs.
- **Fair per-key sampling** sharply reduced overfitting and improved business relevance.
- Attempting to use only ProductMasterId for Map produced artificial results; composite keys aligned with production and led to more robust, real-world performance.

***

### 8. **Audit Output with Per-Example Reporting**

- For each validation sample, CSV contains:
  - `ServiceAndProductMappingId`
  - `PredictedResult` (Map/Omit)
  - `ConfidenceOmit` (probability of Omit)
  - `ActualResult`
  - `IsCorrect` (prediction match)

***

## 📈 Conclusions

- **Maximum SKU diversity and fairness:** With composite key deduplication, nearly every unique product form gets ML attention.
- **No SKU dropped:** Stratified downsampling kept every Map/Omit composite key in the final set.
- **Production-aligned modeling:** No use of catalog IDs during grouping—model faces only the features truly available at inference.
- **Strong generalization:** Balanced models robustly differentiated true Map vs. Omit even for rare, new, or messy vendor SKUs.
- **Auditability:** Pipeline saved all original fields, predictions, confidence, and correctness for full transparency.

***

## 💡 Key Takeaways

- **Never rely on catalog-only fields for per-SKU sampling:** composite keys from production features are essential.
- **Fair sampling and balancing absolutely matter** for model interpretability and downstream product catalog success.
- **Model audit outputs are critical** for business trust and continuous improvement.

***
