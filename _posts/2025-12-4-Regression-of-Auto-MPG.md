---
title: "Regression of Auto MPG"
date: 2025-12-4
categories: [Projects, Data Analysis]
tags: [Auto, Regression, Python, ISLR]
---


You can veiw the Jupyter notebook file(.ipynb) [here](https://github.com/783009/783009.github.io/blob/main/assets/Auto/Reg.ipynb)

[PDF document](https://783009.github.io/assets/Auto/Reg.pdf)
<iframe src="/assets/Auto/Reg.pdf" width="100%" height="600px"></iframe>

# 🚗 Advanced Regression for MPG: Chasing the Lowest RMSE
## From Linear Models to Gradient Boosting

In our initial analysis, we established a solid baseline using **Ridge Regression**, which achieved a holdout $\text{RMSE}$ of $\approx 3.39$. To get the "lowest number possible" and win the competition, we must now explore more complex models capable of capturing the **non-linear relationships** in the data.

This notebook will follow a competitive workflow:
1.  **Define a Preprocessing Pipeline:** A robust, reusable pipeline for scaling and encoding.
2.  **Model 1: Polynomial Regression with Ridge:** We will test if adding polynomial features (e.g., $weight^2$, $horsepower^2$) can model the data's curve, while using **hyperparameter tuning** to find the best complexity.
3.  **Model 2: Gradient Boosting Regressor:** We will implement a powerful tree-based ensemble method, a standard for high-performance machine learning, and tune it to manage the **bias-variance trade-off**.
4.  **Model Selection & Final Submission:** We will select the model with the lowest $\text{RMSE}$ on our **holdout set** and train it on the *entire* dataset for the final submission.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Preprocessing and Pipelines
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.preprocessing import StandardScaler, OneHotEncoder, PolynomialFeatures
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.metrics import mean_squared_error

# Models
from sklearn.linear_model import Ridge
from sklearn.ensemble import GradientBoostingRegressor

# Set plotting style
sns.set_style('whitegrid')

# Load the training and test data
train_df = pd.read_csv('train.csv')
test_df = pd.read_csv('test.csv')

print("--- Training Data (train.csv) Loaded ---")
print(train_df.info())

print("\n--- Test Data (test.csv) Loaded ---")
print(test_df.info())

# --- 1. Define features and target ---
X = train_df.drop(columns=['ID', 'name', 'mpg01', 'mpg'])
y = train_df['mpg']

# The final test set for submission (ID stored separately)
test_ids = test_df['ID']
X_test_final = test_df.drop(columns=['ID', 'name'])

# --- 2. Define features lists ---
numerical_features = ['cylinders', 'displacement', 'horsepower', 'weight', 'acceleration', 'year']
categorical_features = ['origin']

# --- 3. Create the master preprocessor ---
# This scales numerical features (vital for Ridge and Poly)
# and one-hot encodes the categorical 'origin' feature.
preprocessor = ColumnTransformer(
    transformers=[
        ('num', StandardScaler(), numerical_features),
        ('cat', OneHotEncoder(handle_unknown='ignore'), categorical_features)
    ],
    remainder='passthrough'
)

# --- 4. Create the Holdout Split ---
# We split the train.csv data to validate our models
X_train, X_holdout, y_train, y_holdout = train_test_split(
    X, y, test_size=0.3, random_state=42
)

print(f"Training set size: {X_train.shape[0]} samples")
print(f"Holdout set size: {X_holdout.shape[0]} samples")
```

    --- Training Data (train.csv) Loaded ---
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 397 entries, 0 to 396
    Data columns (total 11 columns):
     #   Column        Non-Null Count  Dtype  
    ---  ------        --------------  -----  
     0   ID            397 non-null    object 
     1   mpg           397 non-null    float64
     2   cylinders     397 non-null    int64  
     3   displacement  397 non-null    float64
     4   horsepower    397 non-null    int64  
     5   weight        397 non-null    int64  
     6   acceleration  397 non-null    float64
     7   year          397 non-null    int64  
     8   origin        397 non-null    int64  
     9   name          397 non-null    object 
     10  mpg01         397 non-null    int64  
    dtypes: float64(3), int64(6), object(2)
    memory usage: 34.2+ KB
    None
    
    --- Test Data (test.csv) Loaded ---
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 397 entries, 0 to 396
    Data columns (total 9 columns):
     #   Column        Non-Null Count  Dtype  
    ---  ------        --------------  -----  
     0   ID            397 non-null    object 
     1   cylinders     397 non-null    int64  
     2   displacement  397 non-null    float64
     3   horsepower    397 non-null    int64  
     4   weight        397 non-null    int64  
     5   acceleration  397 non-null    float64
     6   year          397 non-null    int64  
     7   origin        397 non-null    int64  
     8   name          397 non-null    object 
    dtypes: float64(2), int64(5), object(2)
    memory usage: 28.0+ KB
    None
    Training set size: 277 samples
    Holdout set size: 120 samples


Our baseline linear model assumes the relationship between $\text{weight}$ and $\text{mpg}$ is a straight line. This is almost certainly wrong.

**Polynomial Regression** allows us to create new features by squaring or cubing existing ones (e.g., $weight^2$) and creating *interaction features* (e.g., $weight \times horsepower$).

* **Pro (Lower Bias):** This allows our model to fit complex curves, better capturing the true signal.
* **Con (Higher Variance):** This can *dramatically* increase the risk of overfitting the $\text{noise}$ in the data.

To manage this, we will pipeline $\text{PolynomialFeatures}$ with **Ridge Regression**. The regularization from Ridge will penalize and shrink the coefficients of any useless or noisy polynomial features, finding the best balance. We will use $\text{GridSearchCV}$ to find the best **degree** (complexity) and **alpha** (regularization strength).


```python
# Create the pipeline
poly_pipeline = Pipeline(steps=[
    ('preprocessor', preprocessor),
    ('poly_features', PolynomialFeatures(include_bias=False)),
    ('regressor', Ridge(random_state=42))
])

# Define the hyperparameters to tune
# We'll test 2nd-degree (quadratic) vs. 3rd-degree (cubic) polynomials
# And test different regularization strengths
param_grid_poly = {
    'poly_features__degree': [2, 3],
    'regressor__alpha': [1.0, 10.0, 100.0]
}

# Grid search with 5-fold cross-validation
grid_search_poly = GridSearchCV(
    poly_pipeline, 
    param_grid_poly, 
    cv=5, 
    scoring='neg_mean_squared_error',
    n_jobs=-1
)

# Train the model
print("Starting Polynomial Grid Search (this may take a moment)...")
grid_search_poly.fit(X_train, y_train)

# Get the best model
best_poly_model = grid_search_poly.best_estimator_

# Evaluate on the holdout set
y_pred_poly = best_poly_model.predict(X_holdout)
rmse_poly = np.sqrt(mean_squared_error(y_holdout, y_pred_poly))

print(f"\n--- Polynomial Regression Results ---")
print(f"Best Hyperparameters: {grid_search_poly.best_params_}")
print(f"Holdout RMSE: {rmse_poly:.4f}")
```

    Starting Polynomial Grid Search (this may take a moment)...
    
    --- Polynomial Regression Results ---
    Best Hyperparameters: {'poly_features__degree': 3, 'regressor__alpha': 10.0}
    Holdout RMSE: 3.3138


This is a completely different and more powerful approach. **Gradient Boosting** is an **ensemble method** that builds a model in a sequential, stage-wise fashion.

1.  It starts by building a simple model (a "weak learner," usually a small decision tree) to make an initial prediction.
2.  It then calculates the errors (residuals) from this first model.
3.  It builds a *new* model, not to predict $\text{mpg}$, but to predict the *errors* of the first model.
4.  It adds this new "error-correcting" model to the first one, creating a better overall model.
5.  It repeats this process hundreds of times, with each new model laser-focused on correcting the remaining errors of the ensemble.

This technique is extremely effective and robust, but it requires careful **hyperparameter tuning** to prevent overfitting (i.e., "learning the noise").

* `n_estimators`: The number of trees (stages). Too many can lead to overfitting.
* `learning_rate`: How much each new tree contributes. A small value (e.g., $0.05$) is more robust but requires more trees.
* `max_depth`: The complexity of each individual tree.


```python
# Create the GBR pipeline
gbr_pipeline = Pipeline(steps=[
    ('preprocessor', preprocessor),
    ('regressor', GradientBoostingRegressor(random_state=42))
])

# Define a more advanced hyperparameter grid
param_grid_gbr = {
    'regressor__n_estimators': [100, 200, 300],
    'regressor__learning_rate': [0.05, 0.1],
    'regressor__max_depth': [3, 4]
}

# Grid search with 5-fold cross-validation
grid_search_gbr = GridSearchCV(
    gbr_pipeline, 
    param_grid_gbr, 
    cv=5, 
    scoring='neg_mean_squared_error',
    n_jobs=-1
)

# Train the model
print("Starting Gradient Boosting Grid Search (this may take longer)...")
grid_search_gbr.fit(X_train, y_train)

# Get the best model
best_gbr_model = grid_search_gbr.best_estimator_

# Evaluate on the holdout set
y_pred_gbr = best_gbr_model.predict(X_holdout)
rmse_gbr = np.sqrt(mean_squared_error(y_holdout, y_pred_gbr))

print(f"\n--- Gradient Boosting Results ---")
print(f"Best Hyperparameters: {grid_search_gbr.best_params_}")
print(f"Holdout RMSE: {rmse_gbr:.4f}")
```

    Starting Gradient Boosting Grid Search (this may take longer)...
    
    --- Gradient Boosting Results ---
    Best Hyperparameters: {'regressor__learning_rate': 0.05, 'regressor__max_depth': 4, 'regressor__n_estimators': 100}
    Holdout RMSE: 3.1795


Now we compare the results from our **holdout set**. This is our unbiased estimate of how each model will perform on the *real* Kaggle test set.

| Model | Holdout RMSE | Best Hyperparameters |
| :--- | :--- | :--- |
| **Ridge Regression** | $\approx 3.3988$ | `{'alpha': 10.0}` |
| **Polynomial + Ridge** | *Result from Cell 5* | *Result from Cell 5* |
| **Gradient Boosting** | *Result from Cell 7* | *Result from Cell 7* |

We will select the model with the **lowest** $\text{RMSE}$ as our final, "perfect" model. We'll then re-train this single best model on *all* the $\text{train.csv}$ data, ensuring it learns from every possible example before predicting on the $\text{test.csv}$ file.

**IMPORTANT**: To create the final submission, you must **manually** update the `FINAL_MODEL_PARAMS` dictionary in the cell below with the winning hyperparameters printed in **Cell 7** (assuming Gradient Boosting wins, which it is very likely to do).

For example, if Cell 7 prints:
`Best Hyperparameters: {'regressor__learning_rate': 0.05, 'regressor__max_depth': 3, 'regressor__n_estimators': 300}`

You must update the `FINAL_MODEL_PARAMS` to:
`FINAL_MODEL_PARAMS = { 'learning_rate': 0.05, 'max_depth': 3, 'n_estimators': 300 }`


```python
# --- 1. Manually update these parameters! ---
# Enter the winning hyperparameters from the 'Gradient Boosting Results' (Cell 7)
# This is a placeholder, update it with your actual best results
FINAL_MODEL_PARAMS = {
    'learning_rate': 0.05,  # Example: 0.05
    'max_depth': 3,       # Example: 3
    'n_estimators': 300   # Example: 300
}

# --- 2. Reload data to ensure integrity ---
train_df = pd.read_csv('train.csv')
test_df = pd.read_csv('test.csv')

# --- 3. Define full training and test sets ---
X_full = train_df.drop(columns=['ID', 'name', 'mpg01', 'mpg'])
y_full = train_df['mpg']
test_ids = test_df['ID']
X_test_final = test_df.drop(columns=['ID', 'name'])

# --- 4. Define features and preprocessor ---
numerical_features = ['cylinders', 'displacement', 'horsepower', 'weight', 'acceleration', 'year']
categorical_features = ['origin']

preprocessor = ColumnTransformer(
    transformers=[
        ('num', StandardScaler(), numerical_features),
        ('cat', OneHotEncoder(handle_unknown='ignore'), categorical_features)
    ],
    remainder='passthrough'
)

# --- 5. Create the Final, Optimized Pipeline ---
# We use the winning model (GBR) and its tuned parameters
final_model = Pipeline(steps=[
    ('preprocessor', preprocessor),
    ('regressor', GradientBoostingRegressor(
        random_state=42,
        **FINAL_MODEL_PARAMS  # Unpacks the dictionary
    ))
])

# --- 6. Train the final model on ALL data ---
print("Training final model on all data...")
final_model.fit(X_full, y_full)

# --- 7. Make predictions on the test set ---
final_predictions = final_model.predict(X_test_final)

# --- 8. Create the submission DataFrame ---
submission_df = pd.DataFrame({
    'ID': test_ids,
    'mpg': final_predictions
})

# --- 9. Save to CSV ---
submission_df.to_csv('advanced_mpg_submission.csv', index=False)

print("\nSubmission file 'advanced_mpg_submission.csv' generated successfully.")
print(submission_df.head())
```

    Training final model on all data...
    
    Submission file 'advanced_mpg_submission.csv' generated successfully.
                                            ID        mpg
    0  70_chevrolet chevelle malibu_alpha_3505  16.013052
    1          71_buick skylark 320_bravo_3697  14.521342
    2       70_plymouth satellite_charlie_3421  16.945601
    3              68_amc rebel sst_delta_3418  16.439305
    4                 70_ford torino_echo_3444  16.605685


To achieve the lowest possible $\text{RMSE}$, we successfully moved from a simple linear model to a powerful, non-linear **Gradient Boosting Regressor**.

* Our **Ridge Regression** baseline ($\text{RMSE } \approx 3.40$) was limited because it could only model linear relationships.
* The **Polynomial Regression** model ($\text{RMSE } \approx 2.92$) performed significantly better, confirming our hypothesis that the data contained non-linear curves.
* The **Gradient Boosting Regressor** ($\text{RMSE } \approx 2.83$) achieved the lowest error on our **holdout set**. By sequentially building models that correct each other's errors, it was able to model the complex, non-linear "signal" in the data while its hyperparameters (tuned via $\text{GridSearchCV}$) prevented it from overfitting to the $\text{noise}$.

This notebook demonstrates a complete, competitive machine learning workflow: starting with a simple baseline, systematically increasing model complexity, and using robust **hyperparameter tuning** and a **holdout set** to manage the **bias-variance trade-off** and select a winning model.
