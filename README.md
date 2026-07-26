
# COVID-19 Protective Behavior Cross-National Analysis

This repository contains the data-processing and modelling workflow for a cross-national analysis of self-reported COVID-19 protective behaviour. The project investigates whether the predictors of mask wearing and broader protective behaviour changed before and after the onset of sustained mask-mandate periods, and whether the same predictive patterns appeared across countries.

The final analysis covers six countries:

- Australia
- Brazil
- Canada
- India
- United Kingdom
- United States

China was included in the initial data audit but was excluded from the common modelling workflow because several required survey variables were unavailable.

---

## Statement

This project builds upon the methodological framework used by Ryan et al. (2025). The workflow adapts and extends elements of their approach, including data preprocessing, policy-period construction, outcome construction, model comparison and feature-importance analysis, to support a common cross-national comparison.

The analysis is predictive rather than causal. Mask-mandate timing is used to divide observations into policy periods; the models do not estimate the causal effect of mask mandates.

---

## Project aim

The project addresses three research questions:

1. Within each country, did the leading predictors of COVID-19 protective behaviour change before and after sustained mask-mandate onset?
2. Were these changes consistent across countries?
3. Was there a common set of cross-national predictors alongside country-specific predictors?

Two binary outcomes are analysed:

1. **Face Mask Wearing**  
   Whether a respondent reported frequent or consistent mask wearing.

2. **Overall Protective Behaviour**  
   Whether a respondent generally reported frequent or consistent engagement across the retained protective-behaviour items.

Two tree-based classification models are compared:

- Random Forest
- XGBoost

Hyperparameters are selected using cross-validated ROC AUC on the training data. Final predictive performance is evaluated once on an untouched held-out test set.

---

## Repository structure before execution

The raw data may be supplied as `Data.zip`. Extract it so that the following directory structure is present before running the notebooks.

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
|-- 111 AUC.ipynb
|-- 111 Dataset After Cleaning.ipynb
`-- 111 Why Mandate Thousold = 2.5.ipynb
```

Variable definitions can be looked up in `Data/codebook.xlsx`. The workbook contains descriptions for the original survey variables that may appear in the project.

---

## Dataset

### Data sources

The project uses two public data sources.

#### 1. Imperial College London/YouGov COVID-19 Behaviour Tracker

Repository:

https://github.com/YouGov-Data/covid-19-tracker

The survey data contain individual-level information on protective behaviour, demographics, household characteristics, employment, health conditions, symptoms, mental health, confidence in institutions, perceived risk and attitudes toward public-health measures.

The required country files are:

- `australia.csv`
- `brazil.csv`
- `canada.csv`
- `china.csv`
- `india.csv`
- `united-kingdom.csv`
- `united-states.csv`

Some source files are distributed as ZIP archives and must be extracted before use.

#### 2. Oxford COVID-19 Government Response Tracker

Repository:

https://github.com/OxCGRT/covid-policy-tracker

The policy data provide daily national and subnational COVID-19 policy indicators. This project uses the face-covering policy variable `H6M_Facial Coverings` to construct region-specific mask-mandate periods.

The required policy files are:

- `OxCGRT_AUS_latest.csv`
- `OxCGRT_BRA_latest LEGACY.csv`
- `OxCGRT_CAN_latest.csv`
- `OxCGRT_CHN_latest.csv`
- `OxCGRT_GBR_latest.csv`
- `OxCGRT_IND_latest.csv`
- `OxCGRT_USA_latest.csv`

### Outcome construction

Protective-behaviour frequency responses are recoded as:

| Original response | Score |
|---|---:|
| Not at all | 1 |
| Rarely | 2 |
| Sometimes | 3 |
| Frequently | 4 |
| Always | 5 |

A score of 4 or 5 is treated as frequent or consistent performance.

The project constructs three behaviour measures:

- **Face Mask Wearing Score:** the recoded common mask item, `i12_health_1`.
- **Non-Mask Protective Behavior Score:** the median of the 13 common non-mask protective-behaviour items.
- **Overall Protective Behavior Score:** the median of all 14 common protective-behaviour items.

The face-mask and overall scores are converted to binary outcomes using a threshold of 4. The non-mask score is retained as a predictor only for the face-mask task. It is removed from the overall protective-behaviour task to avoid target leakage.

### Policy-period construction

For each OxCGRT subnational region, the workflow calculates a 14-day rolling mean of `H6M_Facial Coverings`.

A regional mandate start date is defined as:

1. the first date on which the complete 14-day rolling mean reaches or exceeds `2.5`; or
2. when the threshold is never reached, the first date on which the region reaches its maximum 14-day rolling mean.

Each survey response is then assigned to:

- `0`: before the regional mandate start date;
- `1`: on or after the regional mandate start date.

The resulting policy-period variable is used as a temporal boundary. It is not a direct measure of enforcement or individual legal compliance.

---

## Computational environment

The supplied notebooks use the `Python 3` Jupyter kernel and record Python version `3.13.14`.

Main Python packages:

- `pandas`
- `numpy`
- `matplotlib`
- `scikit-learn`
- `imbalanced-learn`
- `xgboost`
- `IPython`
- `jupyter`

A compatible environment can be created with:

```bash
python -m venv .venv
python -m pip install --upgrade pip
python -m pip install jupyter pandas numpy matplotlib scikit-learn imbalanced-learn xgboost ipython
jupyter lab
```

The notebooks construct paths using `Path.cwd()`. Therefore, Jupyter must be started from the project root, and the notebooks should remain in that directory.

Exact third-party package versions are not pinned in the repository. For archival reproduction, package versions can be recorded after creating a working environment:

```bash
python -m pip freeze > requirements.txt
```

---

## How to run the code

### Main workflow

Use **Restart Kernel and Run All** for each notebook in the following order:

1. `001 Uniform column format.ipynb`
2. `001_2 State mapping.ipynb`
3. `002 Data Clean.ipynb`
4. `003 Column Names update.ipynb`
5. `004 Split.ipynb`
6. `005 Random Forest (Model).ipynb`
7. `006 XGBoost (Model).ipynb`

The Random Forest and XGBoost notebooks are independent after Step 5 and may be run in either order.

### Supplementary notebooks

The notebooks whose names begin with `111` generate additional tables and diagnostic visualisations:

- `111 Dataset After Cleaning.ipynb`  
  Run after `002 Data Clean.ipynb`.

- `111 Why Mandate Thousold = 2.5.ipynb`  
  Run after `002 Data Clean.ipynb`, because it requires `region_mandate_dates.csv`.

- `111 AUC.ipynb`  
  Run after both model notebooks, because it reads the Random Forest and XGBoost AUC files.

### Important execution notes

- Preserve the exact raw filenames and directory names.
- Start Jupyter from the project root.
- Generated CSV files use `utf-8-sig` encoding.
- Rerunning the notebooks may overwrite previously generated files.
- The two model notebooks are the most computationally intensive stages because each performs hyperparameter search for 24 modelling tasks.

---

## Repository structure after execution

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
|   |   `-- corresponding YouGov and OxCGRT files for the other countries
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
|-- 111 AUC.ipynb
|-- 111 Dataset After Cleaning.ipynb
`-- 111 Why Mandate Thousold = 2.5.ipynb
```

Each country directory under `Cleaned Data/004 Split` contains four modelling tasks. Every task produces:

- `X_train_<task_name>.csv`
- `X_test_<task_name>.csv`
- `y_train_<task_name>.csv`
- `y_test_<task_name>.csv`

---

## Code functions

### 1. `001 Uniform column format.ipynb`

This notebook performs the initial audit and standardisation of the 14 raw source files.

Main functions:

- tests common encodings in the order `utf-8-sig`, `utf-8`, `cp1252` and `latin1`;
- reads a 5,000-row sample during the audit;
- checks the availability of required YouGov and OxCGRT columns;
- standardises missing-value strings;
- renames `H6_Facial Coverings` to `H6M_Facial Coverings` in the Brazil policy file;
- renames `region` to `state` in the relevant YouGov files;
- excludes China from the common modelling workflow because required variables are missing; and
- exports standardised files to `Cleaned Data/Uniform column format`.

### 2. `001_2 State mapping.ipynb`

This notebook harmonises survey state names with OxCGRT regional names.

Main functions:

- compares YouGov locations against OxCGRT locations;
- corrects bilingual, accented or alternative region names in Canada;
- maps the nine English YouGov regions to the OxCGRT region `England`;
- standardises selected Indian state and territory names;
- maps `District of Columbia` to `Washington DC`;
- removes the unmatched OxCGRT region `Ladakh`;
- removes accents after mapping; and
- verifies that all retained YouGov locations can be matched to an OxCGRT region.

The corrected files overwrite the corresponding standardised files.

### 3. `002 Data Clean.ipynb`

This notebook performs the main preprocessing and outcome construction.

Main functions:

- removes variables with more than 30% missingness within a country;
- excludes selected non-comparable or unnecessary columns;
- recodes household size and ordered survey responses;
- constructs the face-mask, non-mask and overall protective-behaviour measures;
- collapses disease-specific comorbidity indicators into one comorbidity variable;
- retains missing categorical responses as an explicit `N/A` category;
- removes rows with missing numeric or date variables required by the workflow;
- constructs a relative 14-day survey-time variable;
- calculates region-specific mandate start dates;
- assigns before- and after-mandate periods;
- applies one-hot encoding to categorical predictors; and
- saves encoded data, unencoded data and `region_mandate_dates.csv`.

### 4. `003 Column Names update.ipynb`

This notebook converts encoded feature names into labels that are easier to interpret in tables and figures.

Main functions:

- reads all country-level encoded datasets;
- creates readable names for core variables and dummy-variable categories;
- expands labels for employment, government confidence, isolation, testing, symptoms, mental health and other variables;
- converts state indicators to labels such as `State: New South Wales`;
- exports the full old-to-new name mapping as `Columns update.csv`; and
- saves one readable encoded dataset for each country.

### 5. `004 Split.ipynb`

This notebook constructs the country-period-outcome modelling datasets.

Main functions:

- validates the required outcome, score, date, identifier and period columns;
- separates each country into before- and after-mandate subsets;
- creates face-mask and overall protective-behaviour tasks;
- removes identifiers, dates, policy-period fields and leakage variables;
- retains the non-mask protective-behaviour score only for mask prediction;
- converts Boolean predictors to numeric `0/1`;
- checks that all predictors are numeric;
- applies an 80/20 train-test split;
- uses stratification when both target classes have sufficient observations; and
- saves four CSV files for each modelling task.

Across six countries, two policy periods and two outcomes, this produces 24 tasks and 96 split files.

### 6. `005 Random Forest (Model).ipynb`

This notebook trains, tunes and evaluates the Random Forest models.

Main functions:

- loads the 24 train-test tasks;
- applies `RandomOverSampler` inside an `imblearn` pipeline;
- fits a `RandomForestClassifier` with 250 trees;
- samples 40 hyperparameter combinations using `RandomizedSearchCV`;
- uses shuffled stratified cross-validation with up to five folds;
- selects hyperparameters using mean cross-validated ROC AUC;
- evaluates the selected pipeline on the untouched test set;
- extracts impurity-based feature importance;
- saves the complete AUC and feature-importance tables; and
- produces before-versus-after top-feature plots for each country and outcome.

### 7. `006 XGBoost (Model).ipynb`

This notebook repeats the model workflow using XGBoost.

Main functions:

- applies training-only random oversampling inside the model pipeline;
- fits `XGBClassifier` with 250 boosting trees;
- uses the binary logistic objective and histogram-based tree construction;
- samples 40 combinations of depth, learning rate, row sampling, column sampling, child weight, gamma and regularisation parameters;
- selects hyperparameters using cross-validated ROC AUC;
- evaluates final predictive discrimination on the held-out test set;
- extracts XGBoost feature importance;
- saves AUC and complete feature-importance files; and
- exports the top-ten feature-importance plots.

### 8. `111 AUC.ipynb`

This supplementary notebook:

- reads the Random Forest and XGBoost AUC result files;
- merges the results by country, outcome and mandate period;
- rounds the held-out AUC values for reporting; and
- generates the combined model-comparison table used in the report.

### 9. `111 Dataset After Cleaning.ipynb`

This supplementary notebook:

- compares the initial and retained row counts for each country;
- calculates country-level data-retention percentages;
- plots retained and removed survey records;
- counts records in the before- and after-mandate periods; and
- plots the policy-period sample distribution.

After cleaning, 156,772 of 227,130 survey records were retained, corresponding to an overall retention rate of 69.0%.

### 10. `111 Why Mandate Thousold = 2.5.ipynb`

This supplementary diagnostic notebook:

- recalculates regional 14-day rolling means of the OxCGRT face-covering indicator;
- displays country-level mean policy trajectories;
- shows the threshold of `2.5` and the maximum-value fallback;
- marks the resulting country-level diagnostic dates; and
- plots all region-specific mandate dates for the six retained countries.

The country-level plots are diagnostic summaries. The actual survey-period assignments use the region-specific dates created in `002 Data Clean.ipynb`.

---

## Modelling details

The workflow creates four binary classification tasks for each country:

| Task suffix | Outcome | Policy period |
|---|---|---|
| `before_mandate_mask` | Face mask wearing | Before mandate |
| `after_mandate_mask` | Face mask wearing | After mandate |
| `before_mandate_protective` | Overall protective behaviour | Before mandate |
| `after_mandate_protective` | Overall protective behaviour | After mandate |

With six retained countries, this produces:

- 24 country-period-outcome tasks;
- 24 Random Forest pipelines;
- 24 XGBoost pipelines; and
- 48 final fitted model pipelines in total.

### Train-test design

- Training set: 80%
- Test set: 20%
- Random seed: `1948883`
- Stratification: applied by the binary target when possible
- Test-set use: reserved for final evaluation only

### Class imbalance

Class imbalance is handled with `RandomOverSampler` inside the `imblearn` pipeline. Because oversampling occurs inside the pipeline, it is applied only to the training portion of each cross-validation split. Validation folds and held-out test data are not resampled.

### Hyperparameter tuning

Both models use:

- `RandomizedSearchCV`;
- 40 sampled hyperparameter combinations per task;
- shuffled stratified cross-validation;
- a maximum of five folds;
- fewer folds when the minority class contains fewer than five training observations;
- cross-validated ROC AUC as the tuning metric; and
- the common random seed `1948883`.

### Evaluation

The primary evaluation metric is:

- held-out test ROC AUC

Cross-validated ROC AUC is used for model selection within the training data. Held-out test ROC AUC is used for the final Random Forest–XGBoost comparison.

### Feature importance

Feature importance is extracted from the final refitted model for each task. The plotting workflow:

- merges before- and after-mandate values within a country and outcome;
- removes state dummy variables and explicit `N/A` indicators from the displayed plots;
- selects the ten features with the highest maximum importance across the two periods; and
- groups displayed variables into broad interpretation categories.

Feature importance describes predictive contribution within a fitted model. It must not be interpreted as a causal effect.

---

## Reproducibility

The common random seed is the student ID:

```python
RANDOM_STATE = 1948883
```

The workflow uses:

- a fixed 80/20 train-test split;
- stratified splitting when possible;
- shuffled stratified cross-validation;
- training-only oversampling;
- a fixed number of randomised hyperparameter searches;
- held-out test evaluation;
- consistent outcome definitions across retained countries; and
- relative project paths based on `Path.cwd()`.

For a clean reproduction, generated `Cleaned Data` and `Result` directories may be removed or archived before rerunning the complete workflow. The original files under `Data` should be preserved unchanged.

---

## Key findings

- XGBoost achieved the higher held-out test ROC AUC in **19 of the 24 tasks**. Random Forest achieved the higher value in five tasks.
- Mean held-out test AUC was **0.809 for XGBoost** and **0.807 for Random Forest**, so the average performance difference was small.
- For face-mask wearing, the most consistent leading predictor was the **Non-Mask Protective Behavior Score**. This suggests that mask wearing formed part of a broader protective-behaviour pattern.
- For overall protective behaviour, the most consistent leading predictor was **Isolation Willingness: Very Willing**.
- Perceived COVID-19 severity, symptom-related isolation, physical contact, survey timing, employment, testing and confidence-related variables also contributed across different countries and periods.
- Predictor rankings changed between the before- and after-mandate periods in every country, but the direction and scale of those changes were not uniform.
- The results indicate a shared behavioural core together with country-specific and policy-period-specific predictive patterns.

Country-level differences included:

- government confidence and perceived government response appearing prominently in Canada and the United States;
- household testing and employment variables appearing more prominently in India;
- mental-health variables, COVID-related fear, handwashing and survey timing appearing more prominently in Brazil;
- physical contact being especially important before the mandate in Australia; and
- survey timing becoming the leading displayed mask-wearing predictor after the mandate in the United Kingdom.

These findings are predictive associations and should not be interpreted as evidence that mask mandates caused the observed changes.

---

## Limitations

- The survey data are repeated cross-sectional rather than longitudinal.
- Protective behaviours are self-reported and may be affected by recall or social-desirability bias.
- Countries retained different numbers of predictors after missing-data screening.
- Policy-period assignment identifies a sustained policy boundary but does not measure enforcement.
- Infection prevalence, public information, social norms and survey composition also changed over time.
- Model-based feature importance is not a causal effect estimate.

---

## References

Breiman, L. (2001). Random forests. *Machine Learning, 45*(1), 5–32.  
https://doi.org/10.1023/A:1010933404324

Chen, T., & Guestrin, C. (2016). XGBoost: A scalable tree boosting system. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 785–794.  
https://doi.org/10.1145/2939672.2939785

Imperial College London & YouGov. (2022). *Imperial College London–YouGov COVID-19 Behaviour Tracker Data Hub*.  
https://github.com/YouGov-Data/covid-19-tracker

Oxford COVID-19 Government Response Tracker. (2023). *Oxford COVID-19 Government Response Tracker policy data*.  
https://github.com/OxCGRT/covid-policy-tracker

Ryan, M., Ye, J., Sexton, J., Hickson, R. I., & Brindal, E. (2025). Face mask mandates alter major determinants of adherence to protective health behaviours in Australia. *Royal Society Open Science, 12*(3), 241941.  
https://doi.org/10.1098/rsos.241941




