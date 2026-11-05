# Phishing URL Detection: EDA, Logistic Regression & Explainable AI (SHAP)

Lab 1 of M115, *AI & Intelligent Systems for Cybersecurity* (Master SSIC, ENSIASD Taroudant, 2026-2027).

## Overview

A leakage-free machine learning pipeline that detects phishing URLs with Logistic Regression and explains each alert the way a SOC analyst needs it: which features triggered it, and which decision threshold should act on it.

- **Dataset:** PhiUSIIL Phishing URL Dataset (235,795 URLs, 56 lexical, network and HTML features; Prasad & Chandra, 2024)
- **Target:** `IsPhishing = 1 - label` (1 = phishing, 0 = legitimate), so Precision and Recall measure threat detection
- **Split:** stratified 75/25, `random_state=42`
- **Preprocessing:** `StandardScaler` fitted on the training set only (no leakage)

## Pipeline

1. **Security-oriented EDA:** data quality, class balance, distributions, binary indicators, feature-target correlations
2. **Leakage-free preprocessing:** stratified split, training-only standardization, removal of raw text identifiers (`FILENAME`, `URL`, `Domain`, `TLD`, `Title`) and the precomputed `URLSimilarityIndex`
3. **Five Logistic Regression configurations:** unregularized, strong L2, standard L2, sparse L1, class-balanced L2, plus the L1 regularization path
4. **Explainability and thresholds:** global and local SHAP, waterfall triage of TP / TN / FP cases, threshold tuning at 0.20 / 0.50 / 0.85

## Results (test set, threshold 0.50)

| Model | Precision | Recall | F1 | FP | FN | Zeroed weights |
|---|---|---|---|---|---|---|
| M1: Unregularized | 0.9996 | 0.9990 | 0.9993 | 11 | 25 | 0 / 49 |
| M2: Strong L2 (C=0.001) | 0.9985 | 0.9958 | 0.9971 | 39 | 105 | 0 / 49 |
| M3: Standard L2 (C=1.0) | 0.9996 | 0.9989 | 0.9992 | 10 | 28 | 0 / 49 |
| M4: Sparse L1 (C=0.01) | 0.9993 | 0.9971 | 0.9982 | 18 | 72 | 24 / 49 |
| M5: Balanced L2 | 0.9995 | 0.9990 | 0.9992 | 13 | 25 | 0 / 49 |

PR-AUC rounds to 1.0 for all five models, so it does not separate them on this dataset.

**Best trade-off:** M3 for performance and stability. M4 is the lean alternative, with about half the features at a small F1 cost (and more missed attacks).

## Threshold policy (M3)

| tau | Mode | Precision | Recall | FP | FN |
|---|---|---|---|---|---|
| 0.20 | Threat hunting | 0.9988 | 0.9994 | 31 | 15 |
| 0.50 | Default | 0.9996 | 0.9989 | 10 | 28 |
| 0.85 | Automated blocking | 1.0000 | 0.9975 | 0 | 64 |

Recommended dual-threshold policy:

- **P(phishing) >= 0.85:** automatic DNS/firewall block (0 false positives)
- **0.20 <= P < 0.85:** analyst queue (about 80 of 58,949 test URLs)
- **P < 0.20:** allow and log

## Key indicators

- **Phishing (+):** `LetterRatioInURL`, `DegitRatioInURL`, `SpacialCharRatioInURL`
- **Benign (-):** `IsHTTPS`, `NoOfJS`, `NoOfSelfRef`

HTTPS alone is never a safe rule: certificates are free, and the model relies on many features together.

## Repository contents

- `phishing_detection_lab.ipynb`: the full notebook, with code, plots and answers
- `requirements.txt`: Python dependencies
- The dataset is **not** included. Download `PhiUSIIL_Phishing_URL_Dataset.csv` (UCI Machine Learning Repository) and place it next to the notebook.

## Run it

```bash
pip install -r requirements.txt
jupyter notebook phishing_detection_lab.ipynb
```

## Limitations

All numbers come from a single train/test split. Thresholds should be re-validated on fresh traffic before any real deployment.
