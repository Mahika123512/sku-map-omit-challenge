# sku-map-omit-challenge
Develop scalable pipelines and machine learning models to classify product SKUs as “Map” or “Omit” using only vendor product data. The challenge covers composite key logic, deduplication, class balancing, robust feature engineering, model evaluation, and audit reporting.


# 🛡️ KlinePro Product Map/Omit Challenge — Team 3: *The Inference Club*

This repository documents datasets, code, modeling pipelines, and results contributed by **Team 3: _The Inference Club_**.

***

## 🧩 Challenge Overview

The goal of this challenge is to **accurately classify vendor product SKUs as “Map” (keep/match to the catalog) or “Omit” (do not map)** based purely on their source metadata, using robust ML and fair data engineering.

### 🔹 Problem Summary:
- Review incoming SKUs (product records) and predict whether each should be **Mapped** (included in catalog) or **Omitted** (filtered out).
- Rely only on **source product attributes** — e.g., brand, category, description, size, and packaging — for decision making.
- Confront substantial **class imbalance** and highly diverse "long tail" SKU distributions (most seen only once).
- Design data pipelines for **composite key deduplication, fair sampling, and per-SKU balancing**.
- Build and evaluate ML models using **transformer embeddings + classical classifiers**.
- Ensure models are **fair, explainable, and auditable** with full coverage reports.

***

## 📁 Solution Index

| S.No | Approach Description                                | Link to Details & Results                   |
|------|----------------------------------------------------|---------------------------------------------|
| 1    | Composite Key Sampling, Stratified Map/Omit Balance | [CompositeKey_Balanced_Runs/Research.md](CompositeKey_Balanced_Runs/Research.md) |


> 📝 Further experiments and evaluations will be added as our pipeline matures.

***

## 👥 Team Credits

**Team 3 — The Inference Club**
- Arpit  
- Mahika  
- Prestha  
- Bimal  
- Ernest  

