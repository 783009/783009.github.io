---
title: "Linear Regression Tutorial"
date: 2025-11-19
categories: [Blog, Data Analysis]
tags: [Blog, Data, Analysis]
---
This is the tutorial made my Mr.Andrade with the FIXED CODE
Link to  the Jupyter notebook file(.ipynb) [**here**](https://github.com/783009/783009.github.io/blob/main/assets/linreg/lin-reg-tut.ipynb)

Link to PDF document vesion [**here**](https://783009.github.io/assets/linreg/linear-regression-tutorial.pdf)
# Linear Regression Tutorial


Author: Andrew Andrade ([andrew@andrewandrade.ca](mailto:andrew@andrewandrade.ca))

This is part one of a series of tutorials related to [regression](https://datascienceguide.github.io/regression/) used in data science. 

Recommended reading: (https://www.statlearning.com/) (Chapter 2)

(https://hastie.su.domains/ISLR2/Slides/Ch3_Linear_Regression.pdf)

(https://github.com/intro-stat-learning/ISLP_labs/blob/stable/Ch03-linreg-lab.ipynb)

In this tutorial, We will first learn to fit a simple line using Least Squares Linear Regression (LSLR), plot residuals, residual distribution, statistics approach to linear regression, horizontal residuals and end with total least squares linear regression.

## Fitting a line using LSLR

First let us import the necessary libraries and read the data file.  You can follow along by downloading the dataset from here: TODO.


```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
from math import log
from sklearn import linear_model

#comment below if not using ipython notebook
%matplotlib inline


```

Now lets read the first set of data, take a look at the dataset and make a simple scatter plot.


```python
#read csv
anscombe_i = pd.read_csv('anscombe_i.csv')
anscombe_i
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
      <th>x</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10</td>
      <td>8.04</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8</td>
      <td>6.95</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13</td>
      <td>7.58</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>8.81</td>
    </tr>
    <tr>
      <th>4</th>
      <td>11</td>
      <td>8.33</td>
    </tr>
    <tr>
      <th>5</th>
      <td>14</td>
      <td>9.96</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>7.24</td>
    </tr>
    <tr>
      <th>7</th>
      <td>4</td>
      <td>4.26</td>
    </tr>
    <tr>
      <th>8</th>
      <td>12</td>
      <td>10.84</td>
    </tr>
    <tr>
      <th>9</th>
      <td>7</td>
      <td>4.82</td>
    </tr>
    <tr>
      <th>10</th>
      <td>5</td>
      <td>5.68</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.scatter(anscombe_i.x, anscombe_i.y,  color='black')
plt.ylabel("Y")
plt.xlabel("X")

```




    Text(0.5, 0, 'X')




    
![png](/assets/linreg/output_4_1.png)
    


Luckly for us, we do not need to implement linear regression, since scikit learn already has a very efficient implementation.  The straight line can be seen in the plot below, showing how linear regression attempts to draw a straight line that will best minimize the residual sum of squares between the observed responses in the dataset, and the responses predicted by the linear approximation.

The coefficients, the residual sum of squares and the variance score are also calculated.

Note: from reading the [documentation](p://scikit-learn.org/stable/modules/linear_model.html#ordinary-least-squares) this method computes the least squares solution using a singular value decomposition of X. If X is a matrix of size (n, p) this method has a cost of O($n p^2$), assuming that $n \geq p$.  A more efficient alternative (for large number of features) is to use Stochastic Gradient Descent or another method outlined in the [linear models documentation](https://scikit-learn.org/stable/modules/linear_model.html) 


If you do not know what BigO is, please read the background information from the notes (or take a algorithms course).


y = mx + b

What is `m`?

That is the coefficient.


```python
import numpy as np
from sklearn import linear_model

regr_i = linear_model.LinearRegression()

# make X and y in the shape sklearn expects
X = anscombe_i.x.to_numpy().reshape(-1, 1)
y = anscombe_i.y.to_numpy().reshape(-1, 1)

regr_i.fit(X, y)

# The coefficients
print('Coefficients: \n', regr_i.coef_)

# The mean square error
print("Residual sum of squares: %.2f"
      % np.mean((regr_i.predict(X) - y) ** 2))
# Explained variance score: 1 is perfect prediction
print('Variance score: %.2f' % regr_i.score(X, y))

plt.plot(X,regr_i.predict(X), color='green',
         linewidth=3)

plt.scatter(anscombe_i.x, anscombe_i.y,  color='black')

plt.ylabel("X")
plt.xlabel("y")


```

    Coefficients: 
     [[0.50009091]]
    Residual sum of squares: 1.25
    Variance score: 0.67





    Text(0.5, 0, 'y')




    
![png](/assets/linreg/output_7_2.png)
    


## Residuals

From the notes, we learnt that we use ordinary linear regression when y is dependant on x since the algorithm reduces the vertical residual (y_observed - y predicted).  The figure below outlines this using a different method for linear regression (using a polyfit with 1 polynomial).


```python
import numpy as np
from pylab import *

# Convert x (Series) to NumPy array
x = anscombe_i.x.to_numpy()

# Make sure y is 1D NumPy array
y_1d = y.flatten()  # if y is already ndarray, this works
# y.flatten() is safe even if y is already 1D

# Fit the line
k, d = np.polyfit(x, y_1d, 1)
yfit = k*x + d  # this is now a NumPy array

# Plot
figure(1)
scatter(x, y_1d, color='black')
plot(x, yfit, 'green')

for ii in range(len(x)):
    plot([x[ii], x[ii]], [yfit[ii], y_1d[ii]], 'k')

xlabel('X')
ylabel('Y')
show()
```


    
![png](/assets/linreg/output_9_0.png)
    


Now let us plot the residual (y - y predicted) vs x.


```python
# Import libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
import seaborn as sns
from scipy.stats import norm

# Load Anscombe dataset (example dataset)
anscombe = sns.load_dataset("anscombe")
anscombe_i = anscombe[anscombe.dataset == "I"]  # pick dataset I

# Prepare X and y
X = anscombe_i.x.to_numpy().reshape(-1, 1)  # 2D for sklearn
y = anscombe_i.y.to_numpy().flatten()       # 1D for plotting and polyfit

# Fit linear regression
regr = LinearRegression()
regr.fit(X, y)
yfit = regr.predict(X)  # predicted values

# -------------------------------
# Figure 1: scatter + regression + vertical lines
# -------------------------------
plt.figure(1, figsize=(6,4))
plt.scatter(anscombe_i.x, y, color='black', label='Data')
plt.plot(anscombe_i.x, yfit, color='green', label='Regression line')

# vertical lines (residuals)
for ii in range(len(anscombe_i)):
    plt.plot([anscombe_i.x.iloc[ii], anscombe_i.x.iloc[ii]], 
             [yfit[ii], y[ii]], 'k', alpha=0.6)

plt.xlabel('X')
plt.ylabel('Y')
plt.title('Scatter with Regression and Residuals')
plt.legend()
plt.show()

# -------------------------------
# Figure 2: residual error vs X
# -------------------------------
residual_error = y - yfit
plt.figure(2, figsize=(6,4))
plt.scatter(anscombe_i.x, residual_error, color='blue')
plt.axhline(0, color='black', linestyle='--')
plt.xlabel('X')
plt.ylabel('Residual Error')
plt.title('Residual Error vs X')
plt.show()

# -------------------------------
# Figure 3: histogram of residuals with normal curve
# -------------------------------
error_mean = np.mean(residual_error)
error_sigma = np.std(residual_error)

plt.figure(3, figsize=(6,4))
count, bins, _ = plt.hist(residual_error, bins=10, density=True, 
                          color='skyblue', alpha=0.7, edgecolor='black')

# normal distribution curve
y_pdf = norm.pdf(bins, error_mean, error_sigma)
plt.plot(bins, y_pdf, 'k--', linewidth=1.5)
plt.xlabel('Residual Error')
plt.ylabel('Probability Density')
plt.title('Histogram of Residuals')
plt.show()

```


    
![png](/assets/linreg/output_11_0.png)
    



    
![png](/assets/linreg/output_11_1.png)
    



    
![png](/assets/linreg/output_11_2.png)
    


As seen the the histogram, the residual error should be (somewhat) normally distributed and centered around zero.  This [post](https://stattrek.com/regression/linear-regression.aspx#ReqressionPrerequisites) explains why.

If the residuals are not randomly distributed around zero, consider applying a transform to the data or applying non-linear regression.  In addition to looking at the residuals, one could use the statsmodels library to take a [statistical approach to ordinary least squares regression.](https://www.datarobot.com/blog/ordinary-least-squares-in-python/)



```python
# load statsmodels as alias ``sm``
import statsmodels.api as sm

y = anscombe_i.y
X = anscombe_i.x
# Adds a constant term to the predictor
# y = mx +b
X = sm.add_constant(X)  

#fit ordinary least squares
est = sm.OLS(y, X)
est = est.fit()

est.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>y</td>        <th>  R-squared:         </th> <td>   0.667</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.629</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   17.99</td>
</tr>
<tr>
  <th>Date:</th>             <td>Wed, 19 Nov 2025</td> <th>  Prob (F-statistic):</th>  <td>0.00217</td>
</tr>
<tr>
  <th>Time:</th>                 <td>12:57:56</td>     <th>  Log-Likelihood:    </th> <td> -16.841</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    11</td>      <th>  AIC:               </th> <td>   37.68</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>     9</td>      <th>  BIC:               </th> <td>   38.48</td>
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
    <td></td>       <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th> <td>    3.0001</td> <td>    1.125</td> <td>    2.667</td> <td> 0.026</td> <td>    0.456</td> <td>    5.544</td>
</tr>
<tr>
  <th>x</th>     <td>    0.5001</td> <td>    0.118</td> <td>    4.241</td> <td> 0.002</td> <td>    0.233</td> <td>    0.767</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.082</td> <th>  Durbin-Watson:     </th> <td>   3.212</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.960</td> <th>  Jarque-Bera (JB):  </th> <td>   0.289</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.122</td> <th>  Prob(JB):          </th> <td>   0.865</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.244</td> <th>  Cond. No.          </th> <td>    29.1</td>
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



The important parts of the summary are the:

What is the R squared?

What is the Adj. R squared?

What is the P value?

What are the 95% confidence intervals?


Helpful links:
- R-squared (or [coefficeient of determination](https://en.wikipedia.org/wiki/Coefficient_of_determination)

- [95.0% Conf. Interval](https://stattrek.com/regression/slope-confidence-interval.aspx?Tutorial=AP)

- 
(https://onlinestatbook.com) or (https://stattrek.com/tutorials/ap-statistics-tutorial.aspx) are great free resources which outlines all the necessary background to be a great statstician and data scientist.  Both (https://onlinestatbook.com/2/regression/inferential.html), and  (https://stattrek.com/regression/slope-confidence-interval.aspx?Tutorial=AP) provide the specifics of confidence intervals for linear regression

We can now plot the fitted line to the data and observe the same results as the previous two methods for linear regression.




```python
plt.scatter(anscombe_i.x, anscombe_i.y,  color='black')
X_prime = np.linspace(min(anscombe_i.x), max(anscombe_i.x), 100)[:, np.newaxis]

# add constant as we did before
X_prime = sm.add_constant(X_prime)  
y_hat = est.predict(X_prime)

# Add the regression line (provides same as above)
plt.plot(X_prime[:, 1], y_hat, 'r')  
```




    [<matplotlib.lines.Line2D at 0x7fe7b1628350>]




    
![png](/assets/linreg/output_15_1.png)
    


If we want to be even more fancier, we can use the [seaborn library](https://stanford.edu/~mwaskom/software/seaborn/examples/regression_marginals.html) to plot Linear regression with [marginal distributions](https://en.wikipedia.org/wiki/Marginal_distribution) which also states the pearsonr and p value on the plot.  Using the statsmodels approach is more rigourous, but sns provides quick visualizations.


```python
import seaborn as sns
sns.set(style="darkgrid", color_codes=True)

g = sns.jointplot(
    x="x",               # use keyword arguments
    y="y",
    data=anscombe_i,
    kind="reg",
    xlim=(0, 20),
    ylim=(0, 12),
    color="r",
    height=7            # 'size' is deprecated
)

```


    
![png](/assets/linreg/output_17_0.png)
    



Usually we calculate the (vertical) residual, or the difference in the observed and predicted in the y.  This is because "the use of the least squares method to calculate the best-fitting line through a two-dimensional scatter plot typically requires the user to assume that one of the variables depends on the other.  (We caculate the difference in the y)  However, in many cases the relationship between the two variables is more complex, and it is not valid to say that one variable is independent and the other is dependent. When analysing such data researchers should  consider plotting the three regression lines that can be calculated for any two-dimensional scatter plot."

## Regression using Horizontal Residual

If X is dependant on y, then the regression line can be made based on horizontal residuals as shown below.


```python
import numpy as np
import matplotlib.pyplot as plt
from pylab import polyfit, scatter, plot, figure, xlabel, ylabel

# Convert to NumPy arrays
X = anscombe_i.x.to_numpy().reshape(-1, 1)  # 2D for sklearn
y = anscombe_i.y.to_numpy().reshape(-1, 1)  # 2D

# Fit line (x vs y)
k, d = polyfit(anscombe_i.y.to_numpy(), anscombe_i.x.to_numpy(), 1)
xfit = k*y.flatten() + d  # flatten y to 1D for polyfit

# Plot
figure(2)
scatter(anscombe_i.x, y.flatten(), color='black')
plot(xfit, y.flatten(), 'blue')

for ii in range(len(y)):
    plot([xfit[ii], anscombe_i.x.iloc[ii]], [y[ii], y[ii]], 'k')

xlabel('X')
ylabel('Y')
plt.show()

```


    
![png](/assets/linreg/output_19_0.png)
    


## Total Least Squares Regression

Finally, a line of best fit can be made using [Total least squares regression](https://en.wikipedia.org/wiki/Total_least_squares), a least squares data modeling technique in which observational errors on both dependent and independent variables are taken into account.  This is done by minizing the errors perpendicular to the line, rather than just vertically.  It is more complicated to implement than standard linear regression, but there is Fortran code called ODRPACK that has this efficiently implemented and wrapped scipy.odr Python module (which can be used out of the box).  The details of odr are in the [Scipy documentation](https://docs.scipy.org/doc/scipy/reference/odr.html) and in even more detail in the [ODRPACK guide](https://docs.scipy.org/doc/external/odrpack_guide.pdf).

In the code below (inspired from [here](https://blog.rtwilson.com/orthogonal-distance-regression-in-python/) uses an inital guess for the parameters, and makes a fit using total least squares regression.



```python
from scipy.odr import Model, Data, ODR
from scipy.stats import linregress
import numpy as np

def orthoregress(x, y):
    # get initial guess by first running linear regression
    linregression = linregress(x, y)
    
    model = Model(fit_function)
    
    data = Data(x, y)
    
    od = ODR(data, model, beta0=linregression[0:2])
    out = od.run()
    
    return list(out.beta)

def fit_function(p, x):
    #return y = m x + b
    return (p[0] * x) + p[1]

m, b = orthoregress(anscombe_i.x, anscombe_i.y)

# determine the line-fit
y_ortho_fit = m*anscombe_i.x+b
# plot the data
scatter(anscombe_i.x,anscombe_i.y, color = 'black')
plot(anscombe_i.x, y_ortho_fit, 'r')
xlabel('X')
ylabel('Y')

```




    Text(0, 0.5, 'Y')




    
![png](/assets/linreg/output_21_1.png)
    


Plotting all three regression lines gives a fuller picture of the data, and comparing their slopes provides a simple graphical assessment of the correlation coefficient. Plotting the orthogonal regression line (red) provides additional information because it makes no assumptions about the dependence or independence of the variables; as such, it appears to more accurately describe the trend in the data compared to either of the ordinary least squares regression lines.


```python

scatter(anscombe_i.x,anscombe_i.y,color = 'black')
plot(xfit, anscombe_i.y, 'b', label= "horizontal residuals")
plot(anscombe_i.x, yfit, 'g', label= "vertical residuals")
plot(anscombe_i.x, y_ortho_fit, 'r', label = "perpendicular residuals" )
plt.legend(bbox_to_anchor=(0., 1.02, 1., .102), loc=3,
           ncol=2, mode="expand", borderaxespad=0.)

```




    <matplotlib.legend.Legend at 0x7fe7affc2450>




    
![png](/assets/linreg/output_23_1.png)
    



## Key takeaways:

1. Know the asumptions for using linear regression and ensure they are met.
2. Do not blindly apply simple linear regression, understand when to use horizonal residuals (X is dependant on y) or total least squares regression.
3. Understand the statistical significance of linear regression

## Optional Further reading:

Chapter 2 (Linear regression) of [Introduction to Statistical Learning](https://www-bcf.usc.edu/~gareth/ISL/)

Appendix D Regression of  [Introduction to Data Mining ](https://www-users.cs.umn.edu/~kumar/dmbook/index.php)

Linear models of [Data Mining](https://www.cs.waikato.ac.nz/ml/weka/book.html)

Video (for using WEKA): [Linear regression](https://www.youtube.com/watch?v=6tDnNyNZDF0)

### Scikit Learn documentation:

[Linear models](https://scikit-learn.org/stable/modules/linear_model.html)


## Homework

Now that you have seen an examples of regression using a simple linear models, see if you can predict the price of a house given the size of property from the log_regression_example.csv (found in [../datasets/log_regression_example.csv](https://github.com/datascienceguide/datascienceguide.github.io/raw/master/datasets/log_regression_example.csv)  If you are unable to fit a simple linear model, try transforming variables to achieve linearity outlined in class or  [here](https://stattrek.com/regression/linear-transformation.aspx?Tutorial=AP)

Hint: look at the log and power transform
