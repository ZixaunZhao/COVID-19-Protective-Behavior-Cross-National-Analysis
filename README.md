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

### Data sources

Firstly, all the initial data is already included in my GitHub repository (Data.zip), If you need the original source of the data, you can go to these two links respectively:

Link 1: https://github.com/YouGov-Data/covid-19-tracker

After opening link 1, click on the folder named "Data" on the homepage, and then find the following 7 data files: `australia.zip`,  `brazil.csv`, `canada.zip`, `china.csv`, `india.csv`, `united-kingdom.zip`, and `united-states.zip`. Download these files and ensure they have the file structure required for this project. Some files may need to be unzipped.


Link 2: https://github.com/OxCGRT/covid-policy-tracker

After opening link 2, click on the folder named "Data" on the homepage, and then find the following 7 data files: `OxCGRT_AUS_latest.csv`,  `OxCGRT_BRA_latest LEGACY.csv`, `OxCGRT_CAN_latest.csv`, `OxCGRT_CHN_latest.csv`, `OxCGRT_IND_latest.csv`, `OxCGRT_GBR_latest.csv`, and `OxCGRT_USA_latest.csv`. Download these files and ensure they have the file structure required for this project.

### Dataset meaning

Imperial College London/YouGov COVID-19 Behaviour Tracker survey data(Link 1).

Oxford COVID-19 Government Response Tracker policy data(Link 2).

The survey data provide individual-level behavioural, demographic, health, wellbeing, trust, and risk-perception variables. The OxCGRT H6 face-covering indicator is used to define sustained mask-mandate periods.




## Code
### How to run the code ?



Run the notebooks:
   - `001 Uniform column format.ipynb`
   - `001_2 State mapping.ipynb`
   - `002 Data Clean.ipynb`
   - `003 Column Names update.ipynb`
   - `004 Split.ipynb`
   - `005 Random Forest (Model).ipynb`
   - `006 XGBoost (Model).ipynb`
   - `111 AUC.ipynb`
   - `111 Dataset After Cleaning.ipynb`
   - `111 Why Mandate Thousold = 2.5.ipynb`



### Code function

#### 1. `001 Uniform column format.ipynb` 




#### 2. `001_2 State mapping.ipynb`




#### 3. `002 Data Clean.ipynb`



#### 4. `003 Column Names update.ipynb`



#### 5. `004 Split.ipynb`



#### 6. `005 Random Forest (Model).ipynb`



#### 7. `006 XGBoost (Model).ipynb`



#### 8. `111 AUC.ipynb`



#### 9. `111 Dataset After Cleaning.ipynb`



#### 10. `111 Why Mandate Thousold = 2.5.ipynb`





## Modelling details

The workflow uses four binary classification tasks:

| Task name | Outcome | Policy period |
|---|---|---|
| `before_mask` | Face mask wearing | Before mandate date |
| `after_mask` | Face mask wearing | After mandate date |
| `before_protective` | Overall protective behaviour | Before mandate date |
| `after_protective` | Overall protective behaviour | After mandate date |

Class imbalance is handled using random oversampling on the training data only. The test data are not resampled.

Evaluation metrics:

- ROC AUC


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







## Key findings


- XGBoost gives the strongest cross-validated ROC AUC across the four modelling tasks.
.....
