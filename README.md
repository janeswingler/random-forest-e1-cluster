# Random Forest Classifier for e1 Cluster Detection

This repository contains an R-based machine learning pipeline for training and evaluating a Random Forest classifier that identifies e1 excitatory neuron clusters from single-cell transcriptomics data.

## Overview
The goal of this project is to classify cells into e1 or non-e1 clusters based on gene expression levels and evaluate the model’s predictive accuracy and feature interpretability. The analysis also examines whether the top-ranked features align with known biological markers of e1 excitatory neurons.

## Dataset
- **Source:** Aevermann et al. (2018), *Human Molecular Genetics*, 27(R1), R40–R47.  
- **Provider:** J. Craig Venter Institute  
- **Description:** Single-cell RNA-seq dataset where each row represents one cell with 608 numeric gene-expression features; label is binary (1 = e1 cluster, 0 = non-e1). The dataset is not included in this repository due to licensing and size constraints. Users may obtain it from the original source.

## Programming Environment
All experiments were conducted in R 4.4.0 on Ubuntu 22.04.4 LTS.  
Main R packages:
- `randomForest` – model training, OOB error estimation, feature importance  
- `caret` – accuracy, precision, recall, and F1 score calculations  
- `pROC` – ROC and AUC computation  
- `readr` – CSV import  

## Methods
A grid search was performed over:
- `ntree`: 1000, 2000, 4000, 8000  
- `mtry`: ⌊0.5√p⌋, ⌊√p⌋, ⌊2√p⌋ (with p = 608)  
- `cutoff`: (0.7, 0.3), (0.5, 0.5), (0.3, 0.7)  
The global minimum Out-of-Bag (OOB) error occurred at `ntree = 1000`, `mtry = 12`, and `cutoff = (0.5, 0.5)`. For robustness, the final model was retrained with `ntree = 8000`.

## Results
**Best Model Performance (OOB):**
- Accuracy: 0.99655  
- Precision: 0.99003  
- Recall: 1.00000  
- F1 Score: 0.99499  
- OOB Error Rate: 0.00345 (0.35%)  
These figures were derived from the model’s OOB confusion matrix (568 TN, 3 FP, 0 FN, 298 TP).

**Verification set (held-out 2 samples):**
- Correct on both samples, with probabilities 0.971 (class 1) and 0.944 (class 0).

## Feature Importance
Feature ranking (Mean Decrease in Accuracy) identified markers consistent with the literature:
- TESPA1, LINC00507, SLC17A7 ranked top; lack of expression of KCNIP1 was also highly predictive of e1 cells.

## Evaluation & Usefulness
- **Predictive performance:** Near-perfect OOB accuracy (99.655%) with perfect recall indicates the model reliably identifies e1 cells while keeping false negatives at zero in OOB evaluation. The small verification set was classified correctly with confident probabilities, consistent with strong generalization, though its size limits statistical conclusions.
- **Interpretability & domain alignment:** The top features align with known e1 biology (TESPA1, LINC00507, SLC17A7), and the lack of KCNIP1 expression is also predictive, supporting that the classifier’s signals are biologically meaningful, not spurious.
- **Limitations:** The samples-to-features ratio (871 samples, 608 features) is high-dimensional; OOB helps, but external validation on a larger independent set would better quantify generalization. The verification set here is intentionally minimal (n=2) and should be expanded before deployment.

## References
- Aevermann, B. D., et al. (2018). *Cell type discovery using single cell transcriptomics: implications for ontological representation.* Human Molecular Genetics, 27(R1), R40–R47.  
- J. Craig Venter Institute.
