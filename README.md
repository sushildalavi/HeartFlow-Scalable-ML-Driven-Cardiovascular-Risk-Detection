# HeartFlow

An R notebook that explores the Cleveland heart disease data (303 rows) and compares a random forest, XGBoost and a
support vector machine at predicting whether a patient has heart disease. It was written in Google Colab with the R
kernel.

This is a small modelling exercise on one public dataset. It is not a clinical tool. Despite the repository name, there
is no distributed processing here: no PySpark, HDFS or topic modelling code.

## Contents

- `heartDiseasePrediction.ipynb`: exploratory plots, preprocessing, the three models, confusion matrices and ROC curves.
- `heart.csv`: the data, 303 rows with 13 features and a `target` column.

## Read this before using the labels

In this copy of the data the `target` column is inverted relative to what its name suggests:

| `target` | Meaning | Rows |
|---|---|---|
| 0 | Heart disease present (UCI `num` greater than 0) | 138 |
| 1 | No heart disease (UCI `num` equal to 0) | 165 |

This was checked by matching all 303 rows (302 distinct records) on eight clinical columns against `processed.cleveland.data` from UCI.
`caret` treats the first factor level, `0`, as the positive class, so the precision, recall and F1 values below refer to
detecting disease.

Other data caveats:

- `ca = 4` (5 rows) and `thal = 0` (2 rows) are missing values (`?`) in the UCI file. The notebook treats them as
  ordinary numbers.
- The category codes differ from the UCI ones. In UCI terms, `cp` 0, 1, 2, 3 are 4, 2, 3, 1; `restecg` 0, 1, 2 are
  2, 0, 1; `slope` 0, 1, 2 are 3, 2, 1; and `thal` 1, 2, 3 are 6, 3, 7. All are modelled as numbers.
- There is one duplicated row (both copies fall in the training split with the seed used), and one UCI record
  (`60,0,4,158,305,0,2,161,0,0.0,1,0.0,3.0,1`) is missing.

## Method

1. Plots: summary table, correlation matrix, histograms, pair plot, density, box and bar plots.
2. Stratified 80/20 split with `caret::createDataPartition` after `set.seed(123)`: 243 training rows, 60 test rows.
3. Centring and scaling fitted on the training rows only.
4. Models with near-default settings: `randomForest`, `xgboost` (100 rounds, logistic objective) and `e1071::svm`
   (radial kernel).

## Results

Test set of 60 rows, 27 with disease. Always predicting "no disease" would score 0.55 accuracy.

| Model | Accuracy | Precision | Recall | F1 | AUC |
|---|---|---|---|---|---|
| Random forest | 0.817 | 0.833 | 0.741 | 0.784 | 0.880 |
| SVM | 0.817 | 0.808 | 0.778 | 0.792 | 0.870 |
| XGBoost | 0.750 | 0.731 | 0.704 | 0.717 | 0.835 |

Reproduced in November 2026 with R 4.6.2, caret 7.1.0, randomForest 4.7-1.2, e1071 1.7-17, pROC 1.19.1 and xgboost
1.7.11.1, running the cells once in order. All values match the outputs saved in the notebook except the random forest
AUC, which the saved output reports as 0.888.

These come from a single split with no cross-validation or hyperparameter tuning. One test patient is 1.7 percentage
points of accuracy, so the differences between models are not meaningful.

## Running it

You need R with the Jupyter R kernel ([IRkernel](https://irkernel.github.io/)), or Google Colab with the R runtime. Keep
`heart.csv` in the working directory.

Cell numbers count from 0. Cells 0-2 install and load packages. They fetch the current `xgboost`, whose 3.x interface no longer accepts the
`xgboost(data = ...)` call used in cell 17. After cell 2 and before cell 3, install a 1.7 release (this builds from
source):

```r
install.packages(
  "https://cran.r-project.org/src/contrib/Archive/xgboost/xgboost_1.7.11.1.tar.gz",
  repos = NULL, type = "source"
)
```

If `patchwork` is missing (the notebook loads it without installing it), run `install.packages("patchwork")`.

## Data source and credit

- Janosi, A., Steinbrunn, W., Pfisterer, M. and Detrano, R. (1989). Heart Disease [dataset]. UCI Machine Learning
  Repository. <https://doi.org/10.24432/C52P4X>. Licensed CC BY 4.0.
- This project credits the Kaggle notebook
  [Heart Disease Predictions by desalegngeb](https://www.kaggle.com/code/desalegngeb/heart-disease-predictions/notebook)
  as its source.

The repository is intended for educational and research purposes only.
