---
title: "Auto Data Set"
date: 2025-11-20
categories: [Blog, Data Analysis]
tags: [Blog, Data, Analysis]
---

You can veiw the Jupyter notebook file(.ipynb) [here](https://github.com/783009/783009.github.io/blob/main/assets/Auto/AutoData.ipynb)

[PDF document](https://783009.github.io/assets/Auto/AutoData.pdf)
<iframe src="/assets/Auto/AutoData.pdf" width="100%" height="600px"></iframe>


# Simple Linear Regression on the Auto Dataset
Chapter 3 – Question 8 (Applied)

This analysis investigates the relationship between **horsepower** (predictor) and **mpg** (response) using a simple linear regression model.
We will perform regression using `sm.OLS()` from `statsmodels`, evaluate the model, create plots, and interpret the results.


```python
import pandas as pd
import statsmodels.api as sm
import matplotlib.pyplot as plt

auto = pd.read_csv("/home/mlahkim15/ve/Auto/Auto.csv")
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
      <td>130</td>
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
      <td>165</td>
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
      <td>150</td>
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
      <td>150</td>
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
      <td>140</td>
      <td>3449</td>
      <td>10.5</td>
      <td>70</td>
      <td>1</td>
      <td>ford torino</td>
    </tr>
  </tbody>
</table>
</div>



## Part (a) — Fit the Linear Regression Model

We model `mpg` as the response variable and `horsepower` as the predictor variable.


```python
# Convert horsepower to numeric, coerce errors to NaN
auto['horsepower'] = pd.to_numeric(auto['horsepower'], errors='coerce')

# Drop rows with missing values in mpg or horsepower
auto = auto.dropna(subset=['horsepower', 'mpg'])

# Check types and first rows
print(auto.dtypes)
auto.head

X = auto["horsepower"]
y = auto["mpg"]

X = sm.add_constant(X)
model = sm.OLS(y, X).fit()
model.summary()

```

    mpg             float64
    cylinders         int64
    displacement    float64
    horsepower      float64
    weight            int64
    acceleration    float64
    year              int64
    origin            int64
    name             object
    dtype: object





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
  <th>Date:</th>             <td>Thu, 20 Nov 2025</td> <th>  Prob (F-statistic):</th> <td>7.03e-81</td>
</tr>
<tr>
  <th>Time:</th>                 <td>13:36:18</td>     <th>  Log-Likelihood:    </th> <td> -1178.7</td>
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



### Interpretation of Regression Output

i. **Is there a relationship between horsepower and mpg?**  
Yes — the p-value for horsepower is extremely small (much less than 0.05), which means the relationship is statistically significant.

ii. **How strong is the relationship?**  
The R-squared value is around **0.60**, meaning about **60% of the variation in mpg is explained by horsepower**.

iii. **Is the relationship positive or negative?**  
The coefficient for horsepower is **negative**, meaning **as horsepower increases, mpg decreases**.

iv. **Prediction for horsepower = 98**  
We will calculate the predicted mpg and 95% confidence and prediction intervals below.



```python
new_value = pd.DataFrame({"const":[1], "horsepower":[98]})
model.get_prediction(new_value).summary_frame(alpha=0.05)
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



## Part (b) — Plot mpg vs horsepower and the regression line


```python
fig, ax = plt.subplots(figsize=(10,6))  # make figure wider and taller
ax.scatter(auto["horsepower"], auto["mpg"], s=20, alpha=0.7)  # smaller dots, slightly transparent
ax.plot(auto["horsepower"], model.predict(sm.add_constant(auto["horsepower"])), color='red')  # regression line
ax.set_xlabel("Horsepower")
ax.set_ylabel("MPG")
ax.set_title("MPG vs Horsepower with Regression Line")
plt.show()
```


    
![png](/assets/Auto/output_7_0.png)
    


## Part (c) — Diagnostic Plots
These plots help evaluate assumptions such as linearity, constant variance, and normality of residuals.



```python
import statsmodels.api as sm
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(12, 10))  # make the figure larger
sm.graphics.plot_regress_exog(model, "horsepower", fig=fig)
plt.show()

```


    
![png](/assets/Auto/output_9_0.png)
    


### Comments on Diagnostics

- There appears to be some curvature in the residuals, suggesting the relationship may not be perfectly linear.
- There is some evidence of non-constant variance (funnel shape), which means prediction accuracy varies across horsepower values.
- A few points may be potential outliers influencing the model.

Overall, the model shows a clear negative relationship, but improvements such as polynomial regression might yield a better fit.


```python

```
