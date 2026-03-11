# Multi-Cohort Machine Learning Pipeline for Predicting Early Treatment Response and Survival Outcomes in Multiple Myeloma

This repository contains a complete machine learning workflow designed to classify early treatment response at three months (Fast vs. Slow responders) in multiple myeloma patients using RNA-seq and microarray gene expression data.  
The project additionally evaluates the prognostic implications of machine learning–derived predictions through Kaplan–Meier survival analysis for overall survival (OS) and progression-free survival (PFS).

---



---



### Datasets Integrated
- GSE24080  
- GSE57317  
- GSE136400  
- Internal RNA-seq training and testing cohorts  
- Norwegian cohort  
- Moffitt cohort  

---

## Methods Summary

### Data Preprocessing
- Load clinical and expression matrices from structured directories.
- Standardize and match sample identifiers.
- Remove genes with zero or near-zero expression across samples.

### Expression Filtering
A custom scikit-learn transformer (`ExpressionFilter`) removes genes expressed above 1 TPM/CPM in fewer than 10 percent of samples.

### Feature Selection
`SelectKBest` with ANOVA F-test selects the top 100 informative genes.

### Class Balancing
SMOTE is applied to the training dataset to address class imbalance.

### Machine Learning Pipeline
A unified scikit-learn pipeline handles filtering, selection, and classification:

Pipeline([
('expr', ExpressionFilter(threshold=1, min_prop=0.10)),
('kbest', SelectKBest(f_classif, k=100)),
('clf', RandomForestClassifier(class_weight={0: 1.5, 1: 1.0}))
])


### Hyperparameter Optimization
GridSearchCV tunes:
- Number of estimators  
- Maximum depth  
- Minimum samples per split and leaf  
- Feature sampling strategy  
- Criterion (gini, entropy, log_loss)

The optimization target is recall of Fast responders.

### Threshold Adjustment
Predicted probabilities are converted to class labels using user-defined thresholds to allow evaluation of multiple decision boundaries.

### Survival Analysis
For each dataset:
- Generate Kaplan–Meier curves for OS (and PFS when available)
- Perform log-rank testing  
- Output plots in the `KM_Plots/` directory

---

## Outputs

### Feature Importance
CSV files containing the top genes ranked by Random Forest importance.

### Predictions
Each clinical dataset receives an appended column:
`ML_Result_Random_Forest_Tuned_<threshold>`

### Survival Plots
Kaplan–Meier survival curves exported as image files.

---

## Requirements

```bash
pip install -r requirements.txt