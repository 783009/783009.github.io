---
title: "Linear Regression"
date: 2025-11-19
categories: [Blog, Data Analysis]
tags: [Blog, Data, Analysis]
---

# Linear Regression Tutorial — Blog Post

*(Based on this PDF tutorial by Andrew Andrade)*
<iframe src="/assets/linreg/linear-regression-tutorial.pdf" width="100%" height="600px"></iframe>

---

## 📌 Introduction

In this blog post, I walk through the **Linear Regression Tutorial** and explain the core concepts in an easy-to-understand way. I also include answers to the questions asked in the notebook and demonstrate what I learned while executing the notebook cells.

Additionally, here is the link to my **GitHub repository** where the `linear-regression-tutorial.ipynb` is running with all cells executed successfully:

➡️ GitHub Link: [**here**](https://github.com/783009/783009.github.io/blob/main/assets/linreg/lin-reg-tut.ipynb)

---

## 📈 What is Linear Regression?

Linear regression is a method used to model the relationship between two numeric variables. It finds the **best straight line** that fits a set of data points. This line can be written as:

```
y = m*x + b
```

* **m**: Slope (how much y changes when x changes)
* **b**: Intercept (value of y when x = 0)
* **x**: Independent variable
* **y**: Predicted dependent variable

The goal is to minimize the **residuals**, meaning the difference between the real value and the prediction.

```
residual = y_observed − y_predicted
```

---

## 🔍 Understanding Residuals

Residuals show how far off our model's predictions are. We plot the residuals to check whether linear regression is a good fit.

A good linear regression model has:

* Residuals evenly scattered around zero
* A roughly **bell-shaped** histogram
* No pattern or shape in the residual plot

If the residuals look random → linear regression is appropriate.
If there's a pattern → consider transforming the data or another model.

---

## 🧠 Different Types of Regression

| Method                                | When to use              | What is minimized       |
| ------------------------------------- | ------------------------ | ----------------------- |
| **Vertical Least Squares (standard)** | y depends on x           | Vertical residuals      |
| **Horizontal Least Squares**          | x depends on y           | Horizontal residuals    |
| **Total Least Squares**               | Both x & y contain error | Perpendicular residuals |

Total Least Squares is the most realistic when **both variables include measurement noise**.

---

## 📊 Evaluating the Model — Key Metrics

| Statistic               | Meaning                               | Why it matters                   |
| ----------------------- | ------------------------------------- | -------------------------------- |
| **R²**                  | % of variation explained by the model | Closer to 1 = better fit         |
| **Adj. R²**             | R² adjusted for model complexity      | Useful for comparing models      |
| **p-value**             | Significance of slope                 | < 0.05 means slope is meaningful |
| **Confidence Interval** | Range where true slope likely falls   | Narrow = more certainty          |

---

## ❓ Answers to the Notebook Questions

### **What is the R² value?**

The R² value from the results is **0.667**, meaning the model explains 66.7% of the variation in the data.

### **What is the Adjusted R²?**

The Adjusted R² value is **0.629**, slightly lower because it accounts for sample size and complexity.

### **What is the p-value for the slope?**

The slope p-value is **0.002**, which is less than 0.05. This means the slope is statistically significant and the relationship between x and y is real.

### **What are the 95% confidence intervals for the slope?**

The interval is approximately between **0.233 and 0.767**, meaning we are 95% confident that the true slope lies in that range.

---

## 🎯 Key Takeaways

* Linear regression fits a straight line to data by minimizing residuals.
* Checking residuals helps determine if the model is valid.
* R² and p-values tell us how strong the relationship is.
* Different types of regression exist depending on which variable depends on which.
* Total least squares considers error in both directions and may better represent real-world scenarios.

---

## 📚 Further Reading

* *Introduction to Statistical Learning* (Chapter 2)
* [https://stattrek.com/regression/](https://stattrek.com/regression/)
* [https://scikit-learn.org/stable/modules/linear_model.html](https://scikit-learn.org/stable/modules/linear_model.html)

---

## 🏁 Conclusion

Linear Regression is one of the simplest yet most powerful statistical modeling techniques. Understanding residuals, model assumptions, and key metrics is essential before applying it. By completing this tutorial and running the notebook, I developed a stronger understanding of how regression works and how to evaluate the model results.

---
