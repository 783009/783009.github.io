---
title: "Predicting Student Test Scores"
date: 2025-12-5
categories: [Projects, Data Analysis]
tags: [Exams, Analysis, Python, EDA]
math: true
---

# Predicting student test scores

Predicting student performance is a classic regression challenge, but when you're competing against thousands of other models, the difference between "good" and "elite" comes down to the smallest iterations. After 14 versions of intense experimentation, I’ve documented the all versions of my journey here.

---
# Standing on the Shoulders of Giants: Winning the Student Exam Score Competition

Let’s be real: in a competition with thousands of participants, reinventing the wheel is a waste of time. The smartest way to win isn't to start from scratch; it's to find the best-performing tools available, understand why they work, and then iterate until they’re better. 

This project wasn't a solo sprint—it was an iterative marathon. Huge credit goes to my friend **Rayan Bashir**, whose deep-dive [EDA](https://github.com/rayanbashir/TER/blob/master/assets/01_eda.ipynb) and [Feature Engineering](https://github.com/rayanbashir/TER/blob/master/assets/02_feature_engineering.ipynb) provided the bedrock for my models. Instead of fumbling through the dark with raw data, I was able to use Rayan’s insights into feature relationships and interaction terms to jump straight into high-performance modeling.

---

## The Philosophy: Iterate, Adapt, and Dominate

The strategy was simple: **If a public model or community dataset performed better than my current baseline, it became the new baseline.** My job was to take those "giants" and find the extra 0.001% of performance hidden in the margins.

### 1. Collaborative Foundations
By utilizing Rayan's feature engineering, I started with a dataset that already accounted for non-linear relationships. This meant my models didn't have to "learn" basic correlations—they could focus on the complex residuals.

### 2. Strategic Blending
Single models have "blind spots." By blending—taking weighted averages of XGBoost, LightGBM, and CatBoost—I neutralized individual model biases. As the competition progressed, this moved from simple averages to Ridge Stacking and eventually Rank-Based Weighting.

### 3. Squeezing the Margins
The final stages of this competition weren't about new models; they were about "Constant Tuning" (ct) and distribution alignment. This is where we stop being data scientists and start being data mechanics.

---

## The Evolution: Notebooks v1 through v14

Each version below represents a tangible improvement in my RMSE score. 

### [Version 1: The Baseline Pipeline](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v1.ipynb)
* **The Goal:** Establish a working end-to-end pipeline.
* **The Logic:** I started with a **Voting Regressor** combining three heavy hitters: **XGBoost, LightGBM, and CatBoost**. 
* **Analysis:** This version focused on basic preprocessing, using `OrdinalEncoder` for categorical variables and `StandardScaler` for numerical ones. It was a "sanity check" to ensure the data was being read and predicted correctly.

### [Version 2: Ridge Stacking Architecture](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v2.ipynb)
* **The Goal:** Reduce variance by combining multiple model outputs.
* **The Logic:** I moved from a simple vote to **Ridge Stacking**. I took predictions from 40 different models (OOF predictions) and used a `RidgeCV` model as a meta-learner to find the optimal weights for each.
* **Analysis:** By using 10-fold cross-validation, I was able to get a much more reliable estimate of my performance. The Ridge model acts as a "smart filter," giving more weight to the models that actually contribute to a better score.

### [Version 3: Elite File Integration](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v3.ipynb)
* **The Goal:** Break the 8.55 RMSE barrier.
* **The Logic:** I identified a high-performing public submission ("8.54853") and blended it with my local Ridge stack using a **95/5 ratio**.
* **Analysis:** This was a strategic move. I trusted the high-performing public model for the bulk of the prediction but used a 5% "injection" of my own unique model to provide diversity and potentially lower the error.

### [Version 4: The Power Blend and Clipping](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v4.ipynb)
* **The Goal:** Refine the ensemble and prevent outlier "leakage."
* **The Logic:** I introduced a three-way blend: 85% of the best file, 10% of the mean of all elite files, and 5% of my Ridge stack.
* **Analysis:** I also implemented **Target Clipping**. Since I knew the training target range was between $19.6$ and $100.0$, I forced all predictions into this window. This prevented the RMSE from being blown out by a few extreme, illogical predictions.

### [Version 5: The Titan Deduplication](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v5.ipynb)
* **The Goal:** Clean up the "Community Stack" for better meta-learning.
* **The Logic:** I noticed many public models were nearly identical. I used **MD5 Hashing** to identify and remove duplicate model files before running the Ridge stack.
* **Analysis:** Out of 40 models, I found only 34 were unique. By removing the noise of duplicate models, the RidgeCV meta-learner became much more efficient, resulting in a cleaner "Community Stack" score of 8.585.

### [Version 6: Strategic Reconstruction](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v6.ipynb)
* **The Goal:** Reverse-engineer the best possible score.
* **The Logic:** I used a "Spice" file to nudge multiple "Anchor" files using very specific coefficients (e.g., $0.025$ weighting).
* **Analysis:** This notebook created three intermediate blends and then combined them in a 50/30/20 ratio. This complex nesting allowed me to move from a score of 8.548 down to 8.547, proving that even a $0.001$ gain is worth the complexity.

### [Version 7: The Minimalist Weighted Mean](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v7.ipynb)
* **The Goal:** Simplify the process to find the "Goldilocks" weights.
* **The Logic:** I focused on a direct weighted blend between two top-tier submissions using a **2.9 to 0.1 ratio**.
* **Analysis:** Sometimes, a complex stack can overfit. This version stripped away the noise and focused on a high-weight main file supported by a small "helper" file to stabilize the mean score at exactly $62.5265$.

### [Version 8: Distribution Matching](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v8.ipynb)
* **The Goal:** Match the statistical "shape" of the winning predictions.
* **The Logic:** I moved into post-processing by adjusting the mean and standard deviation of my predictions to match a target distribution (Mean: $62.52$, Std: $22.61$).
* **Analysis:** This is a "pro-level" move. Often, models predict the middle ground too safely. By "stretching" my predictions to match the expected standard deviation, I forced the model to correctly identify more high and low scores.

### [Version 9: Rank-Based Robustness](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v9.ipynb)
* **The Goal:** Use ranking to eliminate the influence of scale differences.
* **The Logic:** I implemented a **Rank-Based Weighting** system using `scipy.stats.rankdata`. 
* **Analysis:** Instead of averaging the scores themselves, I averaged the *position* of the scores. This protects the ensemble from any one model having a weirdly high or low scale, leading to a much more robust final prediction.

### [Version 10: Trend-Based Logic Injection](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v10.ipynb)
* **The Goal:** Dynamic correction based on model agreement.
* **The Logic:** I created three "Stages" of predictions and then used "Trend Logic." If Stage 1 was greater than Stage 2, and Stage 2 was greater than Stage 3, I applied an extra correction factor.
* **Analysis:** This was my most advanced logic yet. It essentially asked: "If my models are all moving in a certain direction, should I push the prediction even further?" This trend-based approach helped capture the directional momentum of the data.

### [Version 11: The Elite Blend (8.54476)](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v11.ipynb)
* **The Goal:** Combine the "Trend Logic" from v10 with the highest-scoring public anchors.
* **The Logic:** A highly specific three-way blend: 90% of the current best-performing ensemble and 10% of a diversified "Spice" model.
* **Analysis:** This version proved that adding a small amount of high-variance "noise" could actually lower the overall RMSE by better predicting the extreme high/low scores.

### [Version 12: Constant Tuning "ct" (8.54466)](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v12.ipynb)
* **The Goal:** Fine-tune the mean of the prediction vector.
* **The Logic:** I applied a **global constant shift**. By subtracting a tiny fraction (e.g., 0.0001) from every prediction, I shifted the model mean to perfectly align with the training target mean.
* **Analysis:** On the leaderboard, this moved the needle from 8.5447 to 8.5446. It’s a "dirty" trick, but in synthetic data competitions, aligning your mean is essential.

### [Version 13: Refined Clipping (8.54465)](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v13.ipynb)
* **The Goal:** Tighten the boundaries on the upper and lower percentiles.
* **The Logic:** Instead of hard clipping at 19.6 and 100.0, I used a soft-clipping approach based on the standard deviation of the test set.
* **Analysis:** This version managed to shave off another 0.00001. At this level, we are essentially "overfitting" to the known statistical properties of the dataset—a common but effective strategy for synthetic data.

### [Version 14: The Final "ct2" Polish (8.54462)](https://github.com/783009/783009.github.io/blob/main/assets/Exams/Test_scores_v14.ipynb)
* **The Goal:** The absolute limit of post-processing.
* **The Logic:** I implemented **Distribution Stretching**. I took the predictions from v13 and "stretched" them so the standard deviation of my predictions exactly matched the $22.61$ standard deviation seen in the training data.
* **Analysis:** This was the winning move. Most models "under-predict" the variance (they stay too close to the mean). By forcing the model to be more "aggressive" at the edges, I captured the high-performing students that the standard models missed.

---

## Final Analysis & Conclusion

What did we learn? First, **EDA is everything.** Without Rayan’s work on the feature set, no amount of ensembling would have gotten us into the 8.544 range. Second, **don't be afraid to stack.** The community is a resource—use the best public ideas as your floor, not your ceiling.

The jump from Version 1 (RMSE ~8.60) to Version 14 (RMSE 8.54462) wasn't about finding a "magical" new algorithm. It was about:
1.  **Cleaning the Stack:** Using MD5 to remove redundant models.
2.  **Ranking over Rating:** Using rank-based blends to stabilize scores.
3.  **Mechanical Post-Processing:** Shifting means and stretching distributions to match the known target statistics.

In the end, data science is part math, part intuition, and part mechanics. We started by standing on the shoulders of giants, and we ended by giving those giants a very, very precise haircut.
