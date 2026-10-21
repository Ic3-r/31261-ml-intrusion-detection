# Machine Learning for Cyberattack Detection

UTS 31261 Internetworking Project, Spring 2026. Supervisor: A/Prof Hoang Dinh.

This repository holds the code for our comparison of traditional machine learning and deep learning for network intrusion detection. We trained a Decision Tree, a Random Forest, a 1D CNN and an LSTM on three public datasets, and compared a baseline pipeline against an optimised one (signed log transform) for the deep models.

Team: Rawad El Taher, Ariyo Hamlehdar, Solomon Pager, Cecilia Susantio, Nathan Chan, Dennis Long Phan.

## Datasets

The datasets are not stored in this repository because of their size. Download them from the original sources and place them in the folders shown below.

| Dataset | Train / test records | Features after encoding | Classes | Source |
|---|---|---|---|---|
| NSL-KDD | 125,973 / 22,543 | 118 | 5 | [LINK] |
| UNSW-NB15 | 175,341 / 82,332 | 191 | 10 | [LINK] |
| CICIDS2017 | 1,998,384 / 499,596 | 69 | 8 | [LINK] |

NSL-KDD and UNSW-NB15 use their official train and test splits. CICIDS2017 has no official split, so we use a stratified 80/20 split with seed 0 after combining the eight daily CSV files, dropping NaN, infinite and duplicate rows, and grouping the 15 labels into 8.

## Repository structure

```
[FOLDER OR FILE NAMES GO HERE]
```

Example layout to match to the real files:

```
data/                 place downloaded datasets here (not tracked)
notebooks/
  01_preprocessing.ipynb
  02_decision_tree_random_forest.ipynb
  03_cnn.ipynb
  04_lstm.ipynb
  05_evaluation.ipynb
README.md
requirements.txt
```

## How to run

1. Install Python 3.10 or later and the packages in `requirements.txt` (pandas, numpy, scikit-learn, TensorFlow/Keras, matplotlib).
2. Download the three datasets and place them under `data/`.
3. Run the notebooks in order. Preprocessing comes first, because every model reads the same prepared arrays.
4. Each model notebook writes its metrics and confusion matrix for the three datasets.

All experiments use seed 0 and the test set is used once, after training and early stopping on a validation split.

## Method summary

**Preprocessing.** Categorical features are one-hot encoded and numeric features are scaled with StandardScaler fitted on the training set only. The optimised pipeline applies a signed log transform, sign(x) * log(1 + |x|), before scaling. No class weights are used in any configuration.

**Models.**

| Model | Settings |
|---|---|
| Decision Tree | Entropy criterion, seed 0 |
| Random Forest | Entropy criterion, 100 trees, seed 0 |
| CNN | Conv1D (64 filters, kernel 3), max pooling, dense layer (64), softmax |
| LSTM | One LSTM layer (64 units), softmax. Input is the feature sequence on NSL-KDD and a window of 10 records on UNSW-NB15 and CICIDS2017 |

Deep models use Adam, up to 50 epochs, early stopping on validation loss, a 10% stratified validation split, and batch size 256 (NSL-KDD, UNSW-NB15) or 1024 (CICIDS2017).

## Results

Macro F1 on each test set (accuracy is in the report).

| Model | NSL-KDD | UNSW-NB15 | CICIDS2017 |
|---|---|---|---|
| Decision Tree | 0.540 | 0.482 | 0.973 |
| Random Forest | 0.487 | 0.461 | 0.965 |
| CNN, baseline | 0.554 | 0.444 | 0.782 |
| CNN, optimised | 0.546 | 0.440 | 0.912 |
| LSTM, baseline | 0.490 | 0.450 | 0.854 |
| LSTM, optimised | 0.509 | 0.475 | 0.927 |

The Decision Tree matches or beats the deep models on most datasets. The log transform helped the deep models most on CICIDS2017 (+0.130 CNN, +0.073 LSTM macro F1). All results come from single runs, and the smallest attack classes have very few test records.

## Report

The full write-up is in the final report: Section 3 (methodology) and Section 4 (results).

## Licence and data

Code in this repository is submitted as coursework. The datasets belong to their original authors, so please cite them if you reuse them.
