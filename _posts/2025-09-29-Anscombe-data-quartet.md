---
title: "Anscombe's Quartet"
date: 2025-09-29
categories: [Projects, Data Analysis]
tags: [anscombe, statistics, data-visualization, analysis]
---

# Anscombe's Quartet Analysis and Visualization

This notebook analyzes Anscombe's Quartet, a set of four datasets with nearly identical summary statistics but very different distributions. 

We calculate summary statistics, visualize the data with scatterplots and regression lines, and apply Tufte’s principles of effective visualization.

#### The code(part 1)
```python
# -------------------------------
# 1️⃣ Imports
# -------------------------------
import pandas as pd
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns

sns.set(style="whitegrid")
plt.rcParams['figure.figsize'] = (8, 6)

# -------------------------------
# 2️⃣ Load Anscombe's Quartet data
# -------------------------------
data = {
    "x1": [10, 8, 13, 9, 11, 14, 6, 4, 12, 7, 5],
    "y1": [8.04, 6.95, 7.58, 8.81, 8.33, 9.96, 7.24, 4.26, 10.84, 4.82, 5.68],
    "x2": [10, 8, 13, 9, 11, 14, 6, 4, 12, 7, 5],
    "y2": [9.14, 8.14, 8.74, 8.77, 9.26, 8.10, 6.13, 3.10, 9.13, 7.26, 4.74],
    "x3": [10, 8, 13, 9, 11, 14, 6, 4, 12, 7, 5],
    "y3": [7.46, 6.77, 12.74, 7.11, 7.81, 8.84, 6.08, 5.39, 8.15, 6.42, 5.73],
    "x4": [8, 8, 8, 8, 8, 8, 8, 19, 8, 8, 8],
    "y4": [6.58, 5.76, 7.71, 8.84, 8.47, 7.04, 5.25, 12.50, 5.56, 7.91, 6.89],
}
df = pd.DataFrame(data)
print("Anscombe's Quartet Data:")
display(df)

# -------------------------------
# 3️⃣ Summary statistics
# -------------------------------
results = []
datasets = [("x1", "y1"), ("x2", "y2"), ("x3", "y3"), ("x4", "y4")]

for x_col, y_col in datasets:
    x = df[x_col]
    y = df[y_col]
    
    mean_x = np.mean(x)
    mean_y = np.mean(y)
    var_x = np.var(x, ddof=1)
    var_y = np.var(y, ddof=1)
    corr = np.corrcoef(x, y)[0, 1]
    
    slope, intercept, r_value, _, _ = stats.linregress(x, y)
    r_squared = r_value**2
    
    results.append({
        "Dataset": y_col,
        "Mean X": round(mean_x, 2),
        "Mean Y": round(mean_y, 2),
        "Var X": round(var_x, 2),
        "Var Y": round(var_y, 2),
        "Correlation": round(corr, 2),
        "Regression Line": f"y = {slope:.2f}x + {intercept:.2f}",
        "R²": round(r_squared, 2)
    })

summary = pd.DataFrame(results)
print("\nSummary Statistics for All 4 Datasets:")
display(summary)
```

    Anscombe's Quartet Data:



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
      <th>x1</th>
      <th>y1</th>
      <th>x2</th>
      <th>y2</th>
      <th>x3</th>
      <th>y3</th>
      <th>x4</th>
      <th>y4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10</td>
      <td>8.04</td>
      <td>10</td>
      <td>9.14</td>
      <td>10</td>
      <td>7.46</td>
      <td>8</td>
      <td>6.58</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8</td>
      <td>6.95</td>
      <td>8</td>
      <td>8.14</td>
      <td>8</td>
      <td>6.77</td>
      <td>8</td>
      <td>5.76</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13</td>
      <td>7.58</td>
      <td>13</td>
      <td>8.74</td>
      <td>13</td>
      <td>12.74</td>
      <td>8</td>
      <td>7.71</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>8.81</td>
      <td>9</td>
      <td>8.77</td>
      <td>9</td>
      <td>7.11</td>
      <td>8</td>
      <td>8.84</td>
    </tr>
    <tr>
      <th>4</th>
      <td>11</td>
      <td>8.33</td>
      <td>11</td>
      <td>9.26</td>
      <td>11</td>
      <td>7.81</td>
      <td>8</td>
      <td>8.47</td>
    </tr>
    <tr>
      <th>5</th>
      <td>14</td>
      <td>9.96</td>
      <td>14</td>
      <td>8.10</td>
      <td>14</td>
      <td>8.84</td>
      <td>8</td>
      <td>7.04</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>7.24</td>
      <td>6</td>
      <td>6.13</td>
      <td>6</td>
      <td>6.08</td>
      <td>8</td>
      <td>5.25</td>
    </tr>
    <tr>
      <th>7</th>
      <td>4</td>
      <td>4.26</td>
      <td>4</td>
      <td>3.10</td>
      <td>4</td>
      <td>5.39</td>
      <td>19</td>
      <td>12.50</td>
    </tr>
    <tr>
      <th>8</th>
      <td>12</td>
      <td>10.84</td>
      <td>12</td>
      <td>9.13</td>
      <td>12</td>
      <td>8.15</td>
      <td>8</td>
      <td>5.56</td>
    </tr>
    <tr>
      <th>9</th>
      <td>7</td>
      <td>4.82</td>
      <td>7</td>
      <td>7.26</td>
      <td>7</td>
      <td>6.42</td>
      <td>8</td>
      <td>7.91</td>
    </tr>
    <tr>
      <th>10</th>
      <td>5</td>
      <td>5.68</td>
      <td>5</td>
      <td>4.74</td>
      <td>5</td>
      <td>5.73</td>
      <td>8</td>
      <td>6.89</td>
    </tr>
  </tbody>
</table>
</div>


    
    Summary Statistics for All 4 Datasets:



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
      <th>Dataset</th>
      <th>Mean X</th>
      <th>Mean Y</th>
      <th>Var X</th>
      <th>Var Y</th>
      <th>Correlation</th>
      <th>Regression Line</th>
      <th>R²</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>y1</td>
      <td>9.0</td>
      <td>7.5</td>
      <td>11.0</td>
      <td>4.13</td>
      <td>0.82</td>
      <td>y = 0.50x + 3.00</td>
      <td>0.67</td>
    </tr>
    <tr>
      <th>1</th>
      <td>y2</td>
      <td>9.0</td>
      <td>7.5</td>
      <td>11.0</td>
      <td>4.13</td>
      <td>0.82</td>
      <td>y = 0.50x + 3.00</td>
      <td>0.67</td>
    </tr>
    <tr>
      <th>2</th>
      <td>y3</td>
      <td>9.0</td>
      <td>7.5</td>
      <td>11.0</td>
      <td>4.12</td>
      <td>0.82</td>
      <td>y = 0.50x + 3.00</td>
      <td>0.67</td>
    </tr>
    <tr>
      <th>3</th>
      <td>y4</td>
      <td>9.0</td>
      <td>7.5</td>
      <td>11.0</td>
      <td>4.12</td>
      <td>0.82</td>
      <td>y = 0.50x + 3.00</td>
      <td>0.67</td>
    </tr>
  </tbody>
</table>
</div>


## Summary Statistics Reflection

All four datasets have almost identical:
- Mean of X and Y
- Sample variance of X and Y
- Correlation between X and Y
- Regression line and R²

However, the visualizations reveal differences:
- Dataset 1: simple linear relationship
- Dataset 2: curved trend
- Dataset 3: outlier heavily affects regression
- Dataset 4: vertical line with one outlier


#### The code (part 2)
```python
# -------------------------------
# 4️⃣ Visualization (scatter + regression)
# -------------------------------
fig, axes = plt.subplots(2, 2, figsize=(12, 10))
axes = axes.flatten()

for i, (x_col, y_col) in enumerate(datasets):
    x = df[x_col]
    y = df[y_col]
    
    # Scatter plot
    axes[i].scatter(x, y, color='blue', s=50, edgecolor='k')
    
    # Regression line
    slope, intercept, r_value, _, _ = stats.linregress(x, y)
    y_pred = slope * x + intercept
    axes[i].plot(x, y_pred, color='red', linestyle='--', linewidth=2)
    
    # Titles and labels
    axes[i].set_title(f"Dataset {i+1}", fontsize=14)
    axes[i].set_xlabel("X", fontsize=12)
    axes[i].set_ylabel("Y", fontsize=12)
    
    # Remove top/right spines
    axes[i].spines['top'].set_visible(False)
    axes[i].spines['right'].set_visible(False)
    
    axes[i].grid(True, linestyle=':', linewidth=0.5, alpha=0.7)

plt.tight_layout()
plt.show()

```


    
![png](/assets/output_3_0.png)
    


## Visualization Reflection

- Small multiples make it easy to compare all datasets at once.
- Data-to-ink ratio maximized: only essential elements plotted.
- Chartjunk removed: no 3D effects, clipart, or unnecessary shading.
- Clear labeling ensures regression lines and axes are readable.
- Observation: Identical statistics can hide patterns or outliers; visualization is critical to interpret data correctly.
