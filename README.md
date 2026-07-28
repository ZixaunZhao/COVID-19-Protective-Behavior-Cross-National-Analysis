# COVID-19-Protective-Behavior-Cross-National-Analysis

To investigate whether the factors influencing people’s mask-wearing and other protective behaviours changed before and after the implementation of mandatory mask-wearing orders, and to examine whether such changes were observed across countries.

# Statement

This project builds upon the methodological framework of Ryan et al. (2025). Several parts, including data preprocessing, policy-period definition, outcome construction, and model-selection strategies, reference and extend their methods.

## Project aim

This project compares whether the predictive factors influencing public compliance with mask mandates differed across countries and between the periods before and after mandate implementation.

The analysis uses two binary outcome variables:

1. Face Mask Wearing.
2. Overall Protective Behaviour.

Two classification models are compared:

- Random Forest
- XGBoost

Model selection is based mainly on cross-validated ROC AUC, while final performance is assessed using held-out test ROC AUC.

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

### Data sources

All initial data used in this project are included in the GitHub repository. The original data can also be obtained from the following repositories.

Link 1: https://github.com/YouGov-Data/covid-19-tracker

After opening Link 1, locate the `Data` folder and obtain the following seven country files: `australia.zip`, `brazil.csv`, `canada.zip`, `china.csv`, `india.csv`, `united-kingdom.zip`, and `united-states.zip`. Some files must be unzipped before use.

Link 2: https://github.com/OxCGRT/covid-policy-tracker

After opening Link 2, locate the `Data` folder and obtain the following seven policy files: `OxCGRT_AUS_latest.csv`, `OxCGRT_BRA_latest LEGACY.csv`, `OxCGRT_CAN_latest.csv`, `OxCGRT_CHN_latest.csv`, `OxCGRT_IND_latest.csv`, `OxCGRT_GBR_latest.csv`, and `OxCGRT_USA_latest.csv`.

The downloaded files must be placed in the directory structure shown above.

### Dataset meaning

The Imperial College London/YouGov COVID-19 Behaviour Tracker provides the survey data in Link 1.

The Oxford COVID-19 Government Response Tracker provides the policy data in Link 2.

The survey data contain individual-level behavioural, demographic, health, wellbeing, trust, employment, and risk-perception variables. The OxCGRT `H6M_Facial Coverings` indicator is used to define sustained mask-mandate periods.

Definitions of the original survey variables can be found in `Data/codebook.xlsx`.

## Code

### How to run the code

1. Ensure that the initial file structure is correct.

2. Start Jupyter from the project root directory.

3. Run the main notebooks in the following sequence:

   - `001 Uniform column format.ipynb`
   - `001_2 State mapping.ipynb`
   - `002 Data Clean.ipynb`
   - `003 Column Names update.ipynb`
   - `004 Split.ipynb`
   - `005 Random Forest (Model).ipynb`
   - `006 XGBoost (Model).ipynb`

4. Run the supplementary notebooks after the required output files have been generated:

   - `111 Dataset After Cleaning.ipynb`
   - `111 Why Mandate Thousold = 2.5.ipynb`
   - `111 AUC.ipynb`

The Random Forest and XGBoost notebooks can be run in either order after `004 Split.ipynb`. The AUC notebook should be run after both model notebooks.

### Repository structure (after code execution)

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
|-- Cleaned Data/
|   |-- Uniform column format/
|   |   |-- YouGov_Australia.csv
|   |   |-- OxCGRT_Australia.csv
|   |   `-- corresponding YouGov and OxCGRT files for the other retained countries
|   |
|   |-- 002 Data Clean/
|   |   |-- cleaned_unencoded/
|   |   |-- cleaned_encoded/
|   |   `-- region_mandate_dates.csv
|   |
|   |-- 003 Column Names update/
|   |   |-- Columns update.csv
|   |   `-- <Country> (readable).csv
|   |
|   `-- 004 Split/
|       |-- Australia/
|       |-- Brazil/
|       |-- Canada/
|       |-- India/
|       |-- United_Kingdom/
|       `-- United_States/
|
|-- Result/
|   |-- Random Forest/
|   |   |-- random_forest_auc_results.csv
|   |   |-- random_forest_feature_importance_all.csv
|   |   `-- Figures/
|   |
|   `-- XGBoost/
|       |-- xgboost_auc_results.csv
|       |-- xgboost_feature_importance_all.csv
|       `-- Figures/
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

### Code function

#### 1. `001 Uniform column format.ipynb`

Audits the raw survey and policy files, checks file encodings and required columns, standardises selected column names, and exports the files into a common format. China is excluded from the later common modelling workflow because several required survey variables are unavailable.

#### 2. `001_2 State mapping.ipynb`

Harmonises YouGov state or region names with the corresponding OxCGRT region names. This includes corrections for Canada, India, the United Kingdom, and the United States.

#### 3. `002 Data Clean.ipynb`

Performs missing-data screening, variable recoding, outcome construction, comorbidity construction, and mandate-period assignment. It also creates encoded and unencoded cleaned datasets and saves `region_mandate_dates.csv`.

#### 4. `003 Column Names update.ipynb`

Converts encoded variable names into more readable labels for interpretation, tables, and feature-importance plots.

#### 5. `004 Split.ipynb`

Creates the before- and after-mandate modelling tasks for the two outcomes. It removes identifiers and leakage variables, applies an 80/20 stratified train-test split, and saves the training and test files.

#### 6. `005 Random Forest (Model).ipynb`

Tunes and evaluates Random Forest models using random oversampling, randomised hyperparameter search, cross-validated ROC AUC, and held-out test ROC AUC. It also exports feature-importance results and figures.

#### 7. `006 XGBoost (Model).ipynb`

Tunes and evaluates XGBoost models using the same training, oversampling, cross-validation, and test-evaluation framework. It also exports AUC results, feature-importance tables, and figures.

#### 8. `111 AUC.ipynb`

Combines the Random Forest and XGBoost AUC results and produces the final model-comparison table.

#### 9. `111 Dataset After Cleaning.ipynb`

Summarises the number of retained and removed observations after cleaning and displays the distribution of observations before and after mandate onset.

#### 10. `111 Why Mandate Thousold = 2.5.ipynb`

Provides diagnostic plots for the 14-day rolling mean of the mask-policy indicator, the threshold of 2.5, and the selected regional mandate dates.

## Modelling details

The workflow uses four binary classification tasks:

| Task name | Outcome | Policy period |
|---|---|---|
| `before_mask` | Face mask wearing | Before mandate date |
| `after_mask` | Face mask wearing | After mandate date |
| `before_protective` | Overall protective behaviour | Before mandate date |
| `after_protective` | Overall protective behaviour | After mandate date |

These four tasks are created separately for each of the six retained countries, producing 24 modelling tasks in total.

Class imbalance is handled using random oversampling on the training data only. Validation folds and test data are not resampled.

Evaluation metric:

- ROC AUC

## Reproducibility

The random seed is set to the student ID:

```python
RANDOM_STATE = 1948883
```

The workflow uses:

- Stratified train-test splitting
- Stratified cross-validation with a maximum of five folds
- Training-only oversampling
- Randomised hyperparameter search
- Held-out test evaluation

## Key findings

- XGBoost achieved the higher held-out test ROC AUC in 19 of the 24 modelling tasks.
- The mean held-out test AUC was 0.809 for XGBoost and 0.807 for Random Forest, so the average performance difference was small.
- Face-mask wearing was most consistently predicted by engagement in other non-mask protective behaviours.
- Overall protective behaviour was most consistently predicted by willingness to self-isolate.
- Predictor importance changed between the before- and after-mandate periods in every country, but the pattern of change differed across countries.
- The results suggest a shared behavioural core together with country-specific and policy-period-specific predictive patterns.

These results describe predictive associations and should not be interpreted as causal effects of mask mandates.

