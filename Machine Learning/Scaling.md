
Scaling is the process of changing the distribution of values, this is required for many models which are influenced by scale.

The following models are **NOT** sensitive to scale, and require no scaling
1. [Decision Trees](Decision%20Trees.md) (including [Random Forests](Ensemble%20Learning.md) and Gradient Boosted Trees)
2. [Linear Regression](Linear%20Regression.md)/[Logistic Regression](Logistic%20Regression.md) **WITHOUT** [Regularization](Regularization.md)
3. Naïve Bayes

#### 1. Normalization
rescaling the data to be between 0 and 1


#### 2. Standard Scaler
$$ X_n = \frac{x-\mu}{\sigma} $$
Sets distribution mean to 0 and standard deviation to 1. Not the best to use if the data is not Gaussian


#### 3. Min Max Scaler
$x_a$ = max
$x_m$ = min
$$ X_n = \frac{x-x_m}{x_a - x_m} $$
Scales to a set range [min, max] default is [0, 1]. Responds well if standard deviation is small and when the distribution is not Gaussian. This is sensitive to outliers.


#### 4. Max Abs Scaler
Scales each feature by its maximum absolute value. Does not shift/center data and maintains sparsity. When distribution is positive, acts like Min Max.


#### 5. Robust Scaler
Scaler that is robust to outliers. Removes the median and scales according to the quantile range.


#### 6. Quantile Transformer Scale (Rank Scaler)
Transforms data to a uniform or normal distribution. Tends to spread out the most frequent values and reduces the impact of (marginal) outliers.


#### 7. Power Transformer Scaler
Parametric monotonic transforms that are applied to make data more Gaussian. Finds the optimal scaling factor in stabilizing variance and minimizing skewness through Maximum Likelihood Estimation.


#### 8. Unit Vector Scaler
$$ x_n = \frac{x}{||x||} $$
Changes whole feature vector to be unit length, diving each component by the Euclidean length of the vector.