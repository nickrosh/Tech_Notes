

## [Classification](Classification.md) Metrics

#### 1. Accuracy, Precision, and Recall

**Accuracy**
$$Accuracy = \frac{\text{True Positives} + \text{True Negatives}}{\text{All}} $$

(Not very useful if the target class is sparse)

**Precision**
$$Precision = \frac{\text{True Positives}}{\text{True Positives}+\text{False Positives}} $$
seeing how accurate the positive classification is

**Recall**
$$Recall = \frac{\text{True Positives}}{\text{True Positives}+\text{False Negatives}} $$
What % of actual positives were correctly identified


Example: You are a cop.
You want to make sure the person you catch is a criminal (Precision)
You want to catch as many criminals as possible (Recall)


#### 2. F1 Score
This metric manages the tradeoff between Precision and Recall.

$$F1 = 2\frac{\text{Precision} * \text{Recall}}{\text{Precision} + \text{Recall}} $$
F1 is the harmonic mean of Precision and Recall. Only issue is that it gives equal weight to both. You can make weighted F1 if you want.


#### 3. Log Loss/Binary Cross Entropy

When the model output is prediction probabilities, log loss takes into account the uncertainty of your prediction based on how much it varies.


#### 4. Categorical Cross Entropy

Multiclass version of the above. Usually used for [Neural Networks](Deep%20Learning/Neural%20Networks.md). Susceptible to imbalanced datasets.


#### 5. Area Under ROC (AUC)

The integral of the receiver operating curve, plot of the true positive rate (Recall) and the false positive rate. AUC is scale invariant.



## [Regression](Regression.md) Metrics

#### 1. Mean Squared Error
Squared loss, has the effect of inflating/magnifying large errors this has the effect of "punishing" models more for larger errors when MSE is used as a loss function. It also "punishes" models by inflating the average error score when used as a metric. This metric is also differentiable and this can be optimized better.


#### 2. Root Mean Squared Error
The root of the MSE function. RMSE cannot be calculated as the average of the root of the MSE values. This is Jensen's inequality. RMSE reduces the inflation of error caused by the square term. Useful when large errors are undesirable.


#### 3. Mean Absolute Error
Absolute value of the loss for each observation. MAE is linear, and does not give any additional weight to different types of errors. MAE is robust to outliers, this it is not suitable for situations where you want to pay more attention to outliers.


#### 4. $R^2$ Error

$$R^2 = 1 - \frac{MSE(model)}{MSE(baseline)}$$
MSE(baseline) is also just the mean. R^2 is a scale-free score that compares the model versus the mean.


#### 5. Adjusted $R^2$
n = rows, K = Features
$$R_a^2 = 1 - [\frac{n-1}{n-K-1} * (1-R^2)]$$
This adjusts for the increasing predictors and only shows improvement if there is real improvement improves on the flaw that R^2 scores on increasing terms even though the model isn't improving.