---
title: "EDA on the Combine Data Set"
date: 2025-11-22
categories: [Projects, Data Analysis]
tags: [Data, Regression, Python, EDA]
---

You can veiw the Jupyter notebook file(.ipynb) [here](https://github.com/783009/783009.github.io/blob/main/assets/Combine/Combine.ipynb)

[PDF document](https://783009.github.io/assets/Combine/Combine.pdf)
<iframe src="/assets/Combine/Combine.pdf" width="100%" height="600px"></iframe>

# 🏋️ Combine Athlete Data Analysis Notebook
**Goal:** Merge four combine test datasets into one M.O.A.T. (Mother Of All Tables), analyze relationships between athletic performance variables, explore noise, correlations, and statistical significance.

**Tests Included:**
- Pro Agility
- Isometric Mid-Thigh Pull
- 40 Yard Dash
- Counter Movement Jump

### Steps in this Notebook:
1. Load and inspect data
2. Clean athlete names for consistency
3. Merge datasets into M.O.A.T.
4. Explore missing data
5. Visualize using seaborn pairplot
6. Compute correlations and statistical significance
7. Interpret results academically


```python
# ==============================
# STEP 1 — Setup & Imports
# ==============================

import os
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

pd.set_option("display.max_columns", None)
pd.set_option("display.max_rows", 100)
pd.set_option("display.width", 200)
sns.set_context("notebook")
%matplotlib inline
```

### 🔍 Explanation
We import the Python libraries needed for:
- **Pandas** → working with dataframes
- **Seaborn/Matplotlib** → visualizations
- **SciPy** → statistical analysis


```python
# ==============================
# STEP 2 — Load CSV Files Safely
# ==============================

paths = {
    "ProAgility": "/home/mlahkim15/ve/Combine/Combine Data - ProAgility.csv",
    "IsometricMidThighPull": "/home/mlahkim15/ve/Combine/Combine Data - IsometricMidThighPull.csv",
    "FourtyYardDash": "/home/mlahkim15/ve/Combine/Combine Data - FourtyYardDash.csv",
    "CounterMovementJump": "/home/mlahkim15/ve/Combine/Combine Data - CounterMovementJump.csv"
}

def load_csv(path):
    if not os.path.exists(path):
        print(f"❌ File not found: {path}")
        return None
    df = pd.read_csv(path)
    print(f"✔ Loaded {path} — shape: {df.shape}")
    return df

dfs = {k: load_csv(p) for k, p in paths.items()}
```

    ✔ Loaded /home/mlahkim15/ve/Combine/Combine Data - ProAgility.csv — shape: (73, 8)
    ✔ Loaded /home/mlahkim15/ve/Combine/Combine Data - IsometricMidThighPull.csv — shape: (72, 8)
    ✔ Loaded /home/mlahkim15/ve/Combine/Combine Data - FourtyYardDash.csv — shape: (90, 8)
    ✔ Loaded /home/mlahkim15/ve/Combine/Combine Data - CounterMovementJump.csv — shape: (75, 8)


### 📝 Analysis
This code loads all four combine datasets and prints their dimensions (rows = athletes, columns = metrics).

If a dataset is not found, you will see a ❌ message, and the corresponding dataframe will be skipped.


```python
# ==============================
# STEP 3 — Clean & Standardize Athlete Names
# ==============================

def clean_names(df):
    # Auto-detect a name column
    name_cols = [c for c in df.columns if "name" in c.lower() or "athlete" in c.lower()]
    if not name_cols:
        print("❌ No name column found in:", list(df.columns))
        return df
    name_col = name_cols[0]
    print(f"✔ Using '{name_col}' as name column")
    
    df = df.copy()
    df[name_col] = (
        df[name_col]
        .astype(str)
        .str.strip()
        .str.replace(r"\s+", " ", regex=True)
        .str.title()
    )
    return df.rename(columns={name_col: "Name"})

# Apply to all datasets
for k, df in dfs.items():
    if df is not None:
        print(f"\nProcessing: {k}")
        dfs[k] = clean_names(df)
```

    
    Processing: ProAgility
    ✔ Using 'name' as name column
    
    Processing: IsometricMidThighPull
    ✔ Using 'name' as name column
    
    Processing: FourtyYardDash
    ✔ Using 'name' as name column
    
    Processing: CounterMovementJump
    ✔ Using 'name' as name column


### 📝 Analysis
We standardize athlete names so all datasets merge cleanly.
Without this cleaning, tiny inconsistencies like spacing or capitalization would cause duplicate entries.

This reduces **noise** in the dataset caused by messy data entry.

## 🧠 Build the M.O.A.T — Mother Of All Tables


```python
# ==============================
# STEP 4 — Merge into M.O.A.T. (Tall Table PDF-Friendly)
# ==============================

# Step 1: Merge all dataframes with prefixed column names
moat = None
for k, df in dfs.items():
    if df is None:
        continue
    df_prefixed = df.copy()
    for col in df.columns:
        if col != "Name":
            df_prefixed = df_prefixed.rename(columns={col: f"{k}_{col}"})
    if moat is None:
        moat = df_prefixed
    else:
        moat = moat.merge(df_prefixed, on="Name", how="outer")

print("✅ M.O.A.T. created — shape:", moat.shape)

# Step 2: Convert wide M.O.A.T. to tall/stacked format for PDF readability
moat_tall = moat.melt(id_vars="Name", var_name="Variable", value_name="Value")

# Step 3: Optionally, split into blocks if too long (e.g., 50 rows per block)
block_size = 50
blocks = [moat_tall.iloc[i:i+block_size] for i in range(0, len(moat_tall), block_size)]

# Step 4: Display first block as example
display(blocks[0])

```

    ✅ M.O.A.T. created — shape: (93, 29)
    
    ✅ Tall-format M.O.A.T. (first block shown):



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
      <th>Name</th>
      <th>Variable</th>
      <th>Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alpha Alpha Tango Yankee</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alpha Mike November Sierra</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Alpha November Bravo November</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Alpha November Mike Sierra</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Alpha November Tango Romeo</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Average Elite Athlete, College</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Bravo Echo Romeo Echo</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bravo November Delta Yankee</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Bravo November Whiskey November</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Charlie Echo Echo November</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Charlie Echo Lima Golf</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Charlie November Bravo Yankee</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Charlie November Hotel Echo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Charlie November Mike Echo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Charlie Romeo Charlie Lima</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Charlie Romeo Delta November</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>16</th>
      <td>College Elite Athletes</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Cornerback Nfl Average</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>18</th>
      <td>De Nfl Average</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Dt Nfl Average</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Echo Alpha Kilo Yankee</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Echo Hotel Hotel Romeo</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Echo November Charlie November</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Echo November Mike Romeo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Free Safety Nfl Average</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Golf Echo Golf Yankee</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Golf Echo Mike Sierra</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Golf Echo Tango November</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Golf Foxtrot Hotel Sierra</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Golf November Whiskey Echo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Golf Tango Sierra Zulu</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Hotel Hotel Mike November</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Hotel Yankee Alpha Golf</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Ilb Nfl Average</td>
      <td>ProAgility_sex</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>34</th>
      <td>India Charlie Sierra Lima</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Juliett Echo Echo Lima</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Juliett Echo Foxtrot Delta</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Juliett Echo Golf Echo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Juliett Echo Papa Oscar</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Juliett Echo Tango Tango</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Juliett Kilo Papa Echo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Juliett Kilo Sierra November</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Juliett Kilo Whiskey Sierra</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Juliett November Kilo Charlie</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Kilo Alpha Charlie Delta</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Kilo Alpha November November</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Kilo November Charlie Lima</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Kilo November Hotel Echo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Lima Alpha Delta Echo</td>
      <td>ProAgility_sex</td>
      <td>Women</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Lima Mike Tango Romeo</td>
      <td>ProAgility_sex</td>
      <td>Men</td>
    </tr>
  </tbody>
</table>
</div>


### 📝 Analysis
The **M.O.A.T.** merges all athlete test results into one table.

- Uses an **outer join**, so all athletes are included even if they are missing results
- Each test column gets a prefix to prevent naming conflicts

This combined table helps us analyze relationships between different performance skills.

## 🕳 Check Missing Data



```python
# ==============================
# STEP 5 — Check Missing Data
# ==============================

missing = moat.isnull().sum().sort_values(ascending=False)
print("Top 20 columns with missing values:")
display(missing.head(20))
```

    Top 20 columns with missing values:



    IsometricMidThighPull_hold out                          21
    IsometricMidThighPull_sex                               21
    IsometricMidThighPull_source                            21
    IsometricMidThighPull_delta_total_average               21
    IsometricMidThighPull_personal_average_newton_second    21
    IsometricMidThighPull_absolute_impulse_newton_second    21
    IsometricMidThighPull_movement                          21
    ProAgility_movement                                     20
    ProAgility_sex                                          20
    ProAgility_hold out                                     20
    ProAgility_source                                       20
    ProAgility_delta_total_average                          20
    ProAgility_agility_avg_time_seconds                     20
    ProAgility_agility_total_time_seconds                   20
    CounterMovementJump_sex                                 18
    CounterMovementJump_source                              18
    CounterMovementJump_delta_total_average                 18
    CounterMovementJump_cm_jump+height_average_in           18
    CounterMovementJump_cm_jump+height_max_in               18
    CounterMovementJump_movement                            18
    dtype: int64


### 📝 Analysis
This shows which measurement categories have the most missing values.

Missing data introduces **variation (noise)** that weakens correlation accuracy and might indicate:
- Broken equipment
- Recording errors
- Skipped testing attempts


```python
# ==============================
# STEP 6 — Exploratory Data Analysis with Pairplot (hue="Sex")
# ==============================

# Make sure Sex column exists
sex_col = [c for c in moat.columns if "sex" in c.lower()]
if len(sex_col) == 0:
    print("❌ No 'Sex' column found for hue")
else:
    sex_col = sex_col[0]
    
    numeric_cols = moat.select_dtypes(include=np.number).columns.tolist()
    sample_cols = numeric_cols[:6]  # limit for readability

    sns.pairplot(moat[sample_cols + [sex_col]].dropna(), hue=sex_col)
    plt.suptitle("Pairplot of Sample Performance Metrics by Sex", y=1.02)
    plt.show()
```


    
![png](/assets/Combine/output_11_0.png)
    


### 📝 Analysis
- Pairplots visualize relationships between test metrics  
- Using **hue="Sex"** allows comparison between male and female athletes  
- Patterns, trends, and outliers are easier to detect when grouping by sex


```python
# ==============================
# STEP 7 — PDF-Friendly Correlation Summary (with short labels)
# ==============================

import warnings

# Step 1: Keep only numeric columns with variation
numeric_df = moat.select_dtypes(include=np.number)
numeric_df = numeric_df.loc[:, numeric_df.nunique() > 1]

cols = numeric_df.columns
results = []

# Step 2: Compute upper-triangle correlations only
for i in range(len(cols)):
    for j in range(i+1, len(cols)):
        x = numeric_df[cols[i]]
        y = numeric_df[cols[j]]
        pair_df = pd.concat([x, y], axis=1).dropna()
        
        with warnings.catch_warnings():
            warnings.simplefilter("ignore")
            corr, p_val = stats.pearsonr(pair_df[cols[i]], pair_df[cols[j]])
        
        # Truncate long names for PDF readability
        short_x = cols[i][:20] + "…" if len(cols[i]) > 20 else cols[i]
        short_y = cols[j][:20] + "…" if len(cols[j]) > 20 else cols[j]
        
        results.append({
            "Var 1": short_x,
            "Var 2": short_y,
            "Correlation": round(corr, 3),
            "P-Value": round(p_val, 4)
        })

# Step 3: Create DataFrame
corr_summary = pd.DataFrame(results)

# Step 4: Optional filter to show only meaningful correlations
corr_summary_filtered = corr_summary[corr_summary['Correlation'].abs() > 0.3]

# Step 5: Sort by absolute correlation
corr_summary_filtered = corr_summary_filtered.sort_values(by="Correlation", key=lambda x: x.abs(), ascending=False)

# Step 6: Display with legend/explanation
print("PDF-Friendly Correlation Table:")
print("Legend: '…' indicates truncated variable names for readability.")
display(corr_summary_filtered)

```

    PDF-Friendly Correlation Table:
    Legend: '…' indicates truncated variable names for readability.



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
      <th>Var 1</th>
      <th>Var 2</th>
      <th>Correlation</th>
      <th>P-Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>51</th>
      <td>FourtyYardDash_fourt…</td>
      <td>FourtyYardDash_fourt…</td>
      <td>0.967</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>IsometricMidThighPul…</td>
      <td>IsometricMidThighPul…</td>
      <td>0.952</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>63</th>
      <td>CounterMovementJump_…</td>
      <td>CounterMovementJump_…</td>
      <td>0.948</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>ProAgility_agility_t…</td>
      <td>ProAgility_agility_a…</td>
      <td>0.778</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>58</th>
      <td>FourtyYardDash_fourt…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.767</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>57</th>
      <td>FourtyYardDash_fourt…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.761</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>54</th>
      <td>FourtyYardDash_fourt…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.742</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>53</th>
      <td>FourtyYardDash_fourt…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.736</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>ProAgility_agility_t…</td>
      <td>FourtyYardDash_fourt…</td>
      <td>0.715</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>ProAgility_agility_t…</td>
      <td>FourtyYardDash_fourt…</td>
      <td>0.695</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>ProAgility_agility_t…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.635</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>ProAgility_agility_t…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.631</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>ProAgility_agility_a…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.537</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>ProAgility_agility_a…</td>
      <td>FourtyYardDash_fourt…</td>
      <td>0.528</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>ProAgility_agility_a…</td>
      <td>CounterMovementJump_…</td>
      <td>-0.521</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>ProAgility_agility_a…</td>
      <td>ProAgility_delta_tot…</td>
      <td>-0.518</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>ProAgility_agility_a…</td>
      <td>FourtyYardDash_fourt…</td>
      <td>0.513</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>IsometricMidThighPul…</td>
      <td>IsometricMidThighPul…</td>
      <td>0.463</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>


### 📝 Analysis
- **Correlation matrix** shows how strongly each pair of metrics relates  
- **P-values** indicate whether correlations are statistically significant (p < 0.05)  
- Together, they reveal meaningful patterns: e.g., if speed correlates with jump height or agility
