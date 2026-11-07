# M115: AI & Intelligent Systems for Cybersecurity

Labs and project for the course *AI & Intelligent Systems for Cybersecurity* (Master Sécurité des Systèmes Intelligents et Cyberdéfense, ENSIASD Taroudant, 2026-2027).

## Contents

| Work | Topic | Folder |
|---|---|---|
| Lab 1 | Phishing URL detection: EDA, parameterized Logistic Regression (L1/L2), SHAP explainability and SOC threshold tuning on the PhiUSIIL dataset | [Lab1](Lab1/) |
| Project | To be added | |

Each folder has its own README with the objective, method, results and how to run it.

## Repository layout

```
.
├── README.md
├── requirements.txt        # shared Python dependencies
├── .gitignore              # keeps large datasets out of the repo
└── Lab1/
    ├── README.md
    └── phishing_detection_lab.ipynb
```

## Setup

```bash
pip install -r requirements.txt
jupyter notebook Lab1/phishing_detection_lab.ipynb
```

## Data

Datasets are not stored in this repository (CSV files are ignored by `.gitignore`). Each lab README says which dataset to download and where to place it.
