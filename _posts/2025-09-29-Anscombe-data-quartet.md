---
title: "Anscombe's Quartet"
date: 2025-09-29
categories: [Projects, Data Analysis]
tags: [anscombe, statistics, data-visualization, analysis]
---



# Anscombe's Quartet Visualization

This notebook visualizes the famous **Anscombe's Quartet**, which contains four datasets with nearly identical statistical properties (mean, variance, correlation), but very different distributions when graphed.

This illustrates the importance of **visualizing data**, not just relying on summary statistics.

## Observations:

- **Dataset 1**: A simple linear relationship.
- **Dataset 2**: Curved (non-linear) relationship, even though stats match.
- **Dataset 3**: Linear trend, but with an outlier that skews results.
- **Dataset 4**: Almost all points are the same, except for a strong outlier.

This highlights how datasets with the **same summary statistics** can have **vastly different distributions**, reinforcing the importance of data visualization.

# Anscombe's Quartet Visualization

This document shows the visualizations of Anscombe's quartet datasets.

![Anscombe's Quartet](/assets/data.png)

As you can see, despite having similar statistical properties, each dataset has very different distributions.

### The code

```python
import pandas as pd
import matplotlib.pyplot as plt

# Define the data
data = {
    'x123': [10, 8, 13, 9, 11, 14, 6, 4, 12, 7, 5],
    'y1':   [8.04, 6.95, 7.58, 8.81, 8.33, 9.96, 7.24, 4.26, 10.84, 4.82, 5.68],
    'y2':   [9.14, 8.14, 8.74, 8.77, 9.26, 8.10, 6.13, 3.10, 9.13, 7.26, 4.74],
    'y3':   [7.46, 6.77, 12.74, 7.11, 7.81, 8.84, 6.08, 5.39, 8.15, 6.42, 5.73],
    'x4':   [8, 8, 8, 8, 8, 8, 8, 19, 8, 8, 8],
    'y4':   [6.58, 5.76, 7.71, 8.84, 8.47, 7.04, 5.25, 12.50, 5.56, 7.91, 6.89]
}

# Create a DataFrame
df = pd.DataFrame(data)

# Set up subplots
fig, axs = plt.subplots(2, 2, figsize=(10, 8))
fig.suptitle("Anscombe Data Quartet", fontsize=16)

# Plot each dataset
axs[0, 0].scatter(df['x123'], df['y1'], color='blue')
axs[0, 0].set_title('Dataset 1')
axs[0, 0].set_xlim(2, 20)
axs[0, 0].set_ylim(2, 14)

axs[0, 1].scatter(df['x123'], df['y2'], color='green')
axs[0, 1].set_title('Dataset 2')
axs[0, 1].set_xlim(2, 20)
axs[0, 1].set_ylim(2, 14)

axs[1, 0].scatter(df['x123'], df['y3'], color='red')
axs[1, 0].set_title('Dataset 3')
axs[1, 0].set_xlim(2, 20)
axs[1, 0].set_ylim(2, 14)

axs[1, 1].scatter(df['x4'], df['y4'], color='purple')
axs[1, 1].set_title('Dataset 4')
axs[1, 1].set_xlim(2, 20)
axs[1, 1].set_ylim(2, 14)

# Improve layout
for ax in axs.flat:
    ax.set_xlabel('x')
    ax.set_ylabel('y')
    ax.grid(True)

plt.tight_layout(rect=[0, 0, 1, 0.96])
plt.show()

```
