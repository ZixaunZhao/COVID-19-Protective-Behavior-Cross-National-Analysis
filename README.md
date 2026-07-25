# COVID-19-Protective-Behavior-Cross-National-Analysis
To investigate whether the factors influencing people’s mask-wearing and other protective behaviours changed before and after the implementation of mandatory mask-wearing orders; and to examine whether such changes are a phenomenon observed across countries



# Statement

This project builds upon the methodological framework of Ryan et al. (2025). Several parts, including data preprocessing, policy period definition, outcome construction, and model selection strategies, reference and improve upon their methods.



## Project aim

This project compared whether the predictive factors influencing public compliance with mask mandates differed across countries and time periods before and after their implementation. The analysis results consisted of two binary variables:

1. Face Mask Wearing.
2. Overall Protective Behaviour.

Two classification models are compared:

- Random forest
- XGBoost

Model selection is based mainly on cross-validated ROC AUC.



## Repository structure (initial)


```text
Project root/
|-- Data/
|   |-- YouGov_Data/
|   |   |-- australia.csv
|   |   |-- brazil.csv
|   |   |-- canada.csv
|   |   |-- china.csv
|   |   |-- india.csv
|   |   |-- united-kingdom.csv
|   |   `-- united-states.csv
|   |   
|   |-- codebook.xlsx
|   |   
|   `-- OxCGRT_Data/
|       |-- OxCGRT_AUS_latest.csv
|       |-- OxCGRT_BRA_latest LEGACY.csv
|       |-- OxCGRT_CAN_latest.csv
|       |-- OxCGRT_CHN_latest.csv
|       |-- OxCGRT_GBR_latest.csv
|       |-- OxCGRT_IND_latest.csv
|       `-- OxCGRT_USA_latest.csv
|
|-- 001 Uniform column format.ipynb
|-- 001_2 State mapping.ipynb
|-- 002 Data Clean.ipynb
|-- 003 Column Names update.ipynb
|-- 004 Split.ipynb
|-- 005 Random Forest (Model).ipynb
|-- 006 XGBoost (Model).ipynb
|-- 111 Dataset After Cleaning.ipynb
|-- 111 Why Mandate Thousold = 2.5.ipynb
`-- 111 AUC.ipynb

```




## Dataset

Firstly, all the initial data is already included in my GitHub repository (Data.zip), If you need the original source of the data, you can go to these two links respectively:

Link 1: https://github.com/YouGov-Data/covid-19-tracker

Link 2: https://github.com/OxCGRT/covid-policy-tracker


- After opening link 1, click on the folder named "Data" on the homepage, and then find the following 7 data files: `australia.zip`,  "brazil.csv", "canada.zip", "china.csv", "india.csv", "united-kingdom.zip", and "united-states.zip". Download these files and ensure they have the file structure required for this project. Some files may need to be unzipped.



Dataset 2:




## Notebook workflow


### 1. `01_Data Clean.ipynb.ipynb`

Processes the raw Australian survey data and OxCGRT policy data. Main tasks include:

- Loading `australia.csv` and `OxCGRT_AUS_latest.csv`.
- Identifying state-specific sustained mask mandate start dates from the OxCGRT H6 face-covering indicator.
- Counting missing values.
- Cleaning invalid and missing responses.
- Standardising dates, state identifiers, and survey variables.
- Creating the preprocessed analytical dataset.


### 2. `02_Column Names update.ipynb`

Renames variables into more readable labels for modelling, reporting, and interpretation.


### 3. `03_split.ipynb`

Builds four modelling datasets by combining two outcomes with two policy periods:

- Before mandate - face mask wearing
- After mandate - face mask wearing
- Before mandate - protective behaviour
- After mandate - protective behaviour

Each task is split into 80% training data and 20% test data using stratification.


### 4. `04_Mode_Logistic Regression.ipynb`

Fits logistic regression models for the four modelling tasks. Logistic regression is used as the baseline interpretable linear classifier.

### 5. `05_Model_Classification Tree.ipynb`

Fits classification tree models. Hyperparameters are selected using randomized search with stratified cross-validation.

### 6. `06_Model_Random Forest.ipynb`

Fits random forest models. Hyperparameters are selected using randomized search with stratified cross-validation.

### 7. `07_Model_XGBoost.ipynb`

Fits XGBoost models. Hyperparameters are selected using randomized search with stratified cross-validation, and feature importance values are extracted for interpretation.

### 8. `ALL.ipynb`

Runs the integrated modelling and result-generation workflow. This is the main notebook for reproducing the reported tables and figures.


## Data sources

The project combines:

- Imperial College London/YouGov COVID-19 Behaviour Tracker survey data.
- Oxford COVID-19 Government Response Tracker policy data.

The survey data provide individual-level behavioural, demographic, health, wellbeing, trust, and risk-perception variables. The OxCGRT H6 face-covering indicator is used to define sustained mask-mandate periods.

## Modelling details

The workflow uses four binary classification tasks:

| Task name | Outcome | Policy period |
|---|---|---|
| `before_mask` | Face mask wearing | Before sustained mandate |
| `after_mask` | Face mask wearing | After sustained mandate |
| `before_protective` | Broader protective behaviour | Before sustained mandate |
| `after_protective` | Broader protective behaviour | After sustained mandate |

Class imbalance is handled using random oversampling on the training data only. The test data are not resampled.

Evaluation metrics:

- ROC AUC
- Precision
- Recall
- Accuracy
- F1 score

ROC AUC is used as the primary model-selection metric.

## Reproducibility

The random seed is set to my student ID:

```python
RANDOM_STATE = 1948883
```

The workflow uses:

- Stratified train-test splitting
- Stratified 5-fold cross-validation
- Training-only oversampling
- Held-out test evaluation



## Reproduce the analysis

1. The raw files in the `Data/` folder:
   - `australia.csv`
   - `OxCGRT_AUS_latest.csv`

2. Run the notebooks:
   - `01_Data Clean.ipynb.ipynb`
   - `02_Column Names update.ipynb`
   - `03_split.ipynb`
   - `ALL.ipynb`

3. 4 models:
   - `04_Mode_Logistic Regression.ipynb`
   - `05_Model_Classification Tree.ipynb`
   - `06_Model_Random Forest.ipynb`
   - `07_Model_XGBoost.ipynb`

4. Check the generated outputs in the `Result/` folder.

## Key findings


- XGBoost gives the strongest cross-validated ROC AUC across the four modelling tasks.
- Predictive performance is higher after sustained mask mandates than before mandates.
- State indicators carry substantial predictive information, showing that regional policy timing and local public health context played an important role.
- After excluding state indicators, the main predictors still changed before and after mandates.
- For face mask wearing, the strongest non-state predictor was non-mask protective behaviour in both periods, but its importance decreased after mandates. Survey timing and willingness to self-isolate became more important after mandates.
- For broader protective behaviour, the strongest non-state predictor shifted from physical contact count and perceived COVID severity before mandates to willingness to self-isolate after mandates.

