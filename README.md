# Cleveland Heart Disease Classification with Machine Learning

A reproducible, research-oriented machine learning study using the **UCI Cleveland Heart Disease** dataset. The notebook compares classifiers, evaluates a selected logistic regression pipeline, examines uncertainty and probability calibration, and tests whether four prespecified engineered features improve discrimination.

> **Research benchmark only.** This project is not validated for clinical diagnosis or decision-making.

## Project overview

- **Dataset:** 303 observations, 13 input features, one original outcome (`num`).
- **Binary target:** `0` if `num == 0`; `1` if `num > 0`.
- **Missing data:** UCI `?` values are treated as missing; `ca` has 4 missing values and `thal` has 2. Imputation is fitted within the training folds.
- **Split:** stratified 80/20 train/test (242/61 rows), `random_state=42`.
- **Models:** Logistic Regression, Random Forest, and support vector machine (SVM).
- **Selected logistic regression:** `solver='liblinear'`, `penalty='l1'`, `C=1.0`, selected by training-only five-fold grid search. **Do not confuse these results with earlier L2 experiments.**

## Results from the uploaded, executed notebook

### Final held-out test set (61 observations)

| Metric | Value |
| --- | ---: |
| Accuracy | 0.8689 |
| Precision | 0.8333 |
| Recall / sensitivity | 0.8929 |
| Specificity | 0.8485 |
| F1-score | 0.8621 |
| ROC-AUC | 0.9589 |
| Brier score | 0.0833 |

Confusion matrix: **TN = 28, FP = 5, FN = 3, TP = 25**. The bootstrap 95% interval reported for ROC-AUC is **[0.9113, 0.9913]**; this is based on the small holdout sample and should be interpreted cautiously.

### Repeated cross-validation (5 folds × 10 repeats; training set only)

| Model | Mean ROC-AUC | ROC-AUC SD | Mean accuracy |
| --- | ---: | ---: | ---: |
| Logistic Regression | 0.8931 | 0.0365 | 0.8185 |
| Random Forest | 0.8960 | 0.0356 | 0.8020 |
| SVM | 0.8829 | 0.0382 | 0.8090 |

Exploratory corrected repeated-CV comparisons with Holm adjustment did not detect statistically significant differences among these fixed configurations. These are **not** independent test-set estimates.

### Feature-engineering ablation

Four prespecified derived features were evaluated: `age_squared`, `age_x_thalach`, `oldpeak_x_slope`, and `cp_x_exang`. Both pipelines used identical repeated-CV splits on training data.

| Configuration | Mean ROC-AUC | ROC-AUC SD | Mean accuracy |
| --- | ---: | ---: | ---: |
| Original 13 features | 0.8931 | 0.0365 | 0.8185 |
| Original + 4 derived features | 0.8926 | 0.0377 | 0.8202 |

Mean paired ROC-AUC difference (engineered minus baseline): **−0.0005**; exploratory corrected **p = 0.86994**. The four additions did not demonstrate a meaningful ROC-AUC improvement. This does not prove that other feature-engineering methods are ineffective.

## Methods

The notebook uses median imputation and standard scaling for numerical features (`age`, `trestbps`, `chol`, `thalach`, `oldpeak`). Categorical features (`sex`, `cp`, `fbs`, `restecg`, `exang`, `slope`, `ca`, `thal`) use most-frequent imputation and one-hot encoding. All preprocessing is inside scikit-learn pipelines.

Logistic Regression tuning searches `C ∈ {0.01, 0.1, 1, 10, 100}` and `penalty ∈ {'l1', 'l2'}` with five-fold stratified CV scored by ROC-AUC. The separate repeated-CV analysis evaluates variability. The held-out test set is used for final evaluation, not for selecting features or hyperparameters.

Permutation feature importance is evaluated on a **training-only validation split**, while test-set calibration is exploratory. The study does not establish clinical validity or causal effects of predictors.

## Run in Google Colab

1. Open [Google Colab](https://colab.research.google.com/).
2. Choose **File → Upload notebook** and select `Cleveland_Heart_Disease.ipynb`.
3. Run the cells from top to bottom (**Runtime → Run all**).
4. The notebook downloads the UCI dataset. If the URL fails, upload `processed.cleveland.data` to the Colab working directory and rerun.
5. The `cleveland_outputs/` directory contains exported tables and a fitted research model. The feature-engineering section adds its own CSV files after the main export section.

To run locally, use Python 3.11+ and install dependencies:

```bash
python -m pip install -r requirements.txt
jupyter notebook Cleveland_Heart_Disease.ipynb
```

The uploaded notebook retains the original execution outputs. Running it again may produce small differences across package versions and execution environments.

## Repository structure

```text
Cleveland-Heart-Disease-ML/
├── Cleveland_Heart_Disease.ipynb
├── README.md
├── requirements.txt
├── .gitignore
└── docs/
    └── METHODOLOGY.md
```

## Limitations

The Cleveland dataset contains only 303 records, with 61 in the final test split. This is a single small benchmark rather than an external clinical cohort. Repeated CV folds are correlated, so corrected statistical tests are approximate and non-significance does not establish equivalence. Derived interactions involving encoded categorical variables are exploratory and should not be given direct clinical interpretations. External validation, prospective assessment, and broader population coverage would be required before clinical use.

## Data source and citation

Janosi, A., Steinbrunn, W., Pfisterer, M., & Detrano, R. **Heart Disease**. UCI Machine Learning Repository. https://doi.org/10.24432/C52P4X

Original Cleveland data URL: https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/processed.cleveland.data

## License

No license has been assigned to this repository. Choose and add a license before inviting reuse of the code. The dataset remains subject to its source's terms and citation requirements.
