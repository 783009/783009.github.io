---
title: "Applied Linear Regression on Auto Dataset"
date: 2025-11-20
categories: [Projects, Data Analysis]
tags: [Auto, Regression, Python, ISLR]
---


You can veiw the Jupyter notebook file(.ipynb) [here](https://github.com/783009/783009.github.io/blob/main/assets/Auto/AutoData.ipynb)

[PDF document](https://783009.github.io/assets/Auto/AutoData.pdf)
<iframe src="/assets/Auto/AutoData.pdf" width="100%" height="600px"></iframe>

# Auto Dataset Analysis
This notebook analyzes the Auto dataset to investigate how vehicle characteristics relate to fuel efficiency (mpg).  
We apply **simple linear regression** (Q8) and **multiple linear regression** (Q9), including diagnostic plots and transformations.



```python
import pandas as pd
import statsmodels.api as sm
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np

# Load Auto.csv (make sure it's in the same folder)
auto = pd.read_csv("/assets/Auto/Auto.csv")

# Convert columns to numeric if necessary
auto['horsepower'] = pd.to_numeric(auto['horsepower'], errors='coerce')
auto = auto.dropna()  # drop rows with missing values

auto.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mpg</th>
      <th>cylinders</th>
      <th>displacement</th>
      <th>horsepower</th>
      <th>weight</th>
      <th>acceleration</th>
      <th>year</th>
      <th>origin</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18.0</td>
      <td>8</td>
      <td>307.0</td>
      <td>130.0</td>
      <td>3504</td>
      <td>12.0</td>
      <td>70</td>
      <td>1</td>
      <td>chevrolet chevelle malibu</td>
    </tr>
    <tr>
      <th>1</th>
      <td>15.0</td>
      <td>8</td>
      <td>350.0</td>
      <td>165.0</td>
      <td>3693</td>
      <td>11.5</td>
      <td>70</td>
      <td>1</td>
      <td>buick skylark 320</td>
    </tr>
    <tr>
      <th>2</th>
      <td>18.0</td>
      <td>8</td>
      <td>318.0</td>
      <td>150.0</td>
      <td>3436</td>
      <td>11.0</td>
      <td>70</td>
      <td>1</td>
      <td>plymouth satellite</td>
    </tr>
    <tr>
      <th>3</th>
      <td>16.0</td>
      <td>8</td>
      <td>304.0</td>
      <td>150.0</td>
      <td>3433</td>
      <td>12.0</td>
      <td>70</td>
      <td>1</td>
      <td>amc rebel sst</td>
    </tr>
    <tr>
      <th>4</th>
      <td>17.0</td>
      <td>8</td>
      <td>302.0</td>
      <td>140.0</td>
      <td>3449</td>
      <td>10.5</td>
      <td>70</td>
      <td>1</td>
      <td>ford torino</td>
    </tr>
  </tbody>
</table>
</div>



## Question 8 — Simple Linear Regression
We model **mpg** as the response and **horsepower** as the predictor.


```python
# Simple linear regression
X = sm.add_constant(auto["horsepower"])
y = auto["mpg"]

model_simple = sm.OLS(y, X).fit()
model_simple.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>mpg</td>       <th>  R-squared:         </th> <td>   0.606</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.605</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   599.7</td>
</tr>
<tr>
  <th>Date:</th>             <td>Fri, 21 Nov 2025</td> <th>  Prob (F-statistic):</th> <td>7.03e-81</td>
</tr>
<tr>
  <th>Time:</th>                 <td>10:22:31</td>     <th>  Log-Likelihood:    </th> <td> -1178.7</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   392</td>      <th>  AIC:               </th> <td>   2361.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   390</td>      <th>  BIC:               </th> <td>   2369.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
       <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th>      <td>   39.9359</td> <td>    0.717</td> <td>   55.660</td> <td> 0.000</td> <td>   38.525</td> <td>   41.347</td>
</tr>
<tr>
  <th>horsepower</th> <td>   -0.1578</td> <td>    0.006</td> <td>  -24.489</td> <td> 0.000</td> <td>   -0.171</td> <td>   -0.145</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>16.432</td> <th>  Durbin-Watson:     </th> <td>   0.920</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  17.305</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.492</td> <th>  Prob(JB):          </th> <td>0.000175</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 3.299</td> <th>  Cond. No.          </th> <td>    322.</td>
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



### Interpretation

- **Relationship:** Strong negative relationship — higher horsepower → lower mpg.  
- **Strength:** R² ~0.60 → 60% of mpg variation explained by horsepower.  
- **Prediction:** For horsepower = 98, see below.


```python
new_value = pd.DataFrame({"const":[1], "horsepower":[98]})
pred_simple = model_simple.get_prediction(new_value).summary_frame(alpha=0.05)
pred_simple
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mean</th>
      <th>mean_se</th>
      <th>mean_ci_lower</th>
      <th>mean_ci_upper</th>
      <th>obs_ci_lower</th>
      <th>obs_ci_upper</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>24.467077</td>
      <td>0.251262</td>
      <td>23.973079</td>
      <td>24.961075</td>
      <td>14.809396</td>
      <td>34.124758</td>
    </tr>
  </tbody>
</table>
</div>



### Scatter Plot with Regression Line


```python
fig, ax = plt.subplots(figsize=(10,6))
ax.scatter(auto["horsepower"], auto["mpg"], s=20, alpha=0.7)
ax.plot(auto["horsepower"], model_simple.predict(X), color='red')
ax.set_xlabel("Horsepower")
ax.set_ylabel("MPG")
ax.set_title("MPG vs Horsepower with Regression Line")
plt.show()
```


    
![png](/assets/Auto/output_7_0.png)
    


### Diagnostic Plots for Simple Regression


```python
fig = plt.figure(figsize=(12,10))
sm.graphics.plot_regress_exog(model_simple, "horsepower", fig=fig)
plt.show()
```


    
![png](/assets/Auto/output_9_0.png)
    


## Question 9 — Multiple Linear Regression
We now include **all other variables (except name)** to predict mpg.  
We also explore correlations, interactions, and transformations.


```python
# Convert horsepower to numeric (some values may be '?')
auto['horsepower'] = pd.to_numeric(auto['horsepower'], errors='coerce')

# Drop rows with missing values
auto = auto.dropna()

# Create numeric-only dataframe (drop 'name' column)
auto_numeric = auto.drop(columns=['name'])

# Select key variables for scatterplot matrix
subset = ["mpg", "horsepower", "weight", "year"]

# Create the scatterplot matrix
sns.pairplot(auto_numeric[subset], height=2.5)
plt.suptitle("Scatterplot Matrix: Key Predictors vs MPG", y=1.02)
plt.show()
```


    
![png](/assets/Auto/output_11_0.png)
    


### Multiple Linear Regression



```python
# Multiple regression
X_multi = auto_numeric.drop(columns=['mpg'])
X_multi = sm.add_constant(X_multi)
y_multi = auto_numeric['mpg']

model_multi = sm.OLS(y_multi, X_multi).fit()
model_multi.summary()
```

### Interpretation of Multiple Regression

- **Relationship:** The overall F-test and p-values indicate that predictors collectively explain mpg.  
- **Significant predictors:** Weight, horsepower, year, etc. (check p-values < 0.05).  
- **Coefficient of year:** Positive → newer cars tend to have higher mpg, all else equal.


```python
# Diagnostic plots for multiple regression
fig = plt.figure(figsize=(12,10))
sm.graphics.plot_regress_exog(model_multi, "weight", fig=fig)
plt.show()
```

### Interactions & Transformations

We can try interactions (e.g., horsepower*weight) or transformations (log, sqrt, squared) to improve the model.  
Check p-values for significance and whether plots look better.


```python
# Example: interaction between horsepower and weight
X_inter = auto_numeric.copy()
X_inter['hp_weight'] = X_inter['horsepower'] * X_inter['weight']
X_inter = sm.add_constant(X_inter.drop(columns=['mpg']))
y_inter = auto_numeric['mpg']

model_inter = sm.OLS(y_inter, X_inter).fit()
model_inter.summary()
```

### Example Transformation

- Try log or squared transformations to see if model fit improves:
- log(horsepower), sqrt(weight), weight^2, etc.


```python
X_trans = auto_numeric.copy()
X_trans['log_horsepower'] = np.log(X_trans['horsepower'])
X_trans['weight_squared'] = X_trans['weight'] ** 2

X_trans = sm.add_constant(X_trans.drop(columns=['mpg']))
y_trans = auto_numeric['mpg']

model_trans = sm.OLS(y_trans, X_trans).fit()
model_trans.summary()
```

### Conclusion

- **Simple regression:** mpg decreases as horsepower increases.  
- **Multiple regression:** multiple variables (weight, year, horsepower) significantly affect mpg.  
- **Interactions & transformations:** can improve model fit, but must be interpreted carefully.  
- **Diagnostics:** always check residuals, leverage, and spread to ensure reliable predictions.

### Reflective Summary

Working through this analysis helped me understand how vehicle characteristics like horsepower, weight, and year influence fuel efficiency. I learned how to interpret regression coefficients, evaluate model fit using diagnostic plots, and explore improvements through interactions and transformations. This project also strengthened my skills in presenting data analysis clearly in a professional blog format.
