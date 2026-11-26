# heartrisk

An R notebook that explores the Cleveland heart disease data and compares a random forest, XGBoost and a support vector
machine at predicting whether a patient has heart disease. It is a small modelling exercise on one public dataset, not a
clinical tool.

## Results

The test set has 60 patients, 27 of them with disease. Always predicting "no disease" would score 0.55 accuracy.

| Model | Accuracy | Precision | Recall | F1 | AUC |
|---|---|---|---|---|---|
| Random forest | 0.817 | 0.833 | 0.741 | 0.784 | 0.880 |
| SVM | 0.817 | 0.808 | 0.778 | 0.792 | 0.870 |
| XGBoost | 0.750 | 0.731 | 0.704 | 0.717 | 0.835 |

These were reproduced from the notebook's code in November 2026 with R 4.6.2 and xgboost 1.7.11.1. The saved output in
the notebook shows 0.888 for the random forest AUC; a fresh run gives 0.880, and the other values match.

All of this comes from a single split without cross-validation or tuning. One patient is worth 1.7 points of accuracy,
so the differences between the models are not meaningful.

## The data

`heart.csv` has 303 rows, 13 features and a `target` column. In this copy the label means the opposite of what you
might expect:

| `target` | Meaning | Rows |
|---|---|---|
| 0 | Heart disease present | 138 |
| 1 | No heart disease | 165 |

This was checked by matching the rows against `processed.cleveland.data` from UCI. `caret` treats `0` as the positive
class, so precision, recall and F1 above describe detecting disease.

Other things to know:

- `ca = 4` (5 rows) and `thal = 0` (2 rows) are missing values in the UCI file. The notebook treats them as numbers.
- Category codes differ from UCI's. In UCI terms, `cp` 0, 1, 2, 3 are 4, 2, 3, 1; `restecg` 0, 1, 2 are 2, 0, 1;
  `slope` 0, 1, 2 are 3, 2, 1; and `thal` 1, 2, 3 are 6, 3, 7.
- One row is duplicated, and one UCI record is missing.

## Method

1. Explore the data with summary statistics, a correlation matrix and distribution plots.
2. Make a stratified 80/20 split with seed 123.
3. Centre and scale using the training rows only.
4. Train the three models with near-default settings and compare confusion matrices and ROC curves.

## Running it

Open `heartDiseasePrediction.ipynb` in Google Colab with the R runtime, or in Jupyter with
[IRkernel](https://irkernel.github.io/). Keep `heart.csv` in the working directory and run the cells in order. The first
cell installs the packages and builds xgboost 1.7 from source, which takes a few minutes.

## Data source and credit

- Janosi, A., Steinbrunn, W., Pfisterer, M. and Detrano, R. (1989). Heart Disease [dataset]. UCI Machine Learning
  Repository. <https://doi.org/10.24432/C52P4X>. Licensed CC BY 4.0.
- This project credits the Kaggle notebook
  [Heart Disease Predictions by desalegngeb](https://www.kaggle.com/code/desalegngeb/heart-disease-predictions/notebook)
  as its source.

The repository is intended for educational and research purposes only.
