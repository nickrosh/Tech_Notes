

## [Classification](Classification.md)


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


#### 3. F2 Score
The F2 score is the weighted harmonic mean of the precision and recall (given a threshold value). Unlike the F1 score, which gives equal weight to precision and recall, the F2 score gives more weight to recall than to precision.

$$F2 = 5\frac{\text{Precision} * \text{Recall}}{4\text{Precision} + \text{Recall}} $$
F2 is used when a False Negative is more costly than a False Positive. For example, if your use case is to predict which customers churn, you may consider False Negatives worse than False Positives. In this case, you want your predictions to capture all of the customers that will churn. 


#### 4. F0.5 Score
The F0.5 score is the weighted harmonic mean of the precision and recall (given a threshold value). Unlike the F1 score, which gives equal weight to precision and recall, the F0.5 score gives more weight to precision than to recall.

$$F0.5 = 1.25\frac{\text{Precision} * \text{Recall}}{0.25\text{Precision} + \text{Recall}} $$
F0.5 is used when a False Positive is more costly than a False Negative. For example, if your use case is to predict which products you will run out of, you may consider False Positives worse than False Negatives. In this case, you want your predictions to be very precise and only capture the products that will definitely run out.


#### 4. Log Loss/Binary Cross Entropy

When the model output is prediction probabilities, log loss takes into account the uncertainty of your prediction based on how much it varies.


#### 5. Categorical Cross Entropy

Multiclass version of the above. Usually used as the [Loss Functions](Deep%20Learning/Loss%20Functions.md) for training. Susceptible to imbalanced datasets.


#### 6. Area Under ROC (AUC)

![](../Attachments/Pasted%20image%2020230225023235.png)

The integral of the receiver operating curve, plot of the true positive rate (Recall) and the false positive rate. This metric gives the aggregate performance across all possible classification thresholds. AUC is scale invariant, it measures how well predications are ranked, rather than their absolute values. It is also classification-threshold-invariant. It measures the quality of the model's predictions irrespective of what classification threshold is chosen.



## [Regression](Regression.md)

#### 1. Mean Squared Error
Squared loss, has the effect of inflating/magnifying large errors this has the effect of "punishing" models more for larger errors when MSE is used as a loss function. It also "punishes" models by inflating the average error score when used as a metric. This metric is also differentiable and this can be optimized better.


#### 2. Root Mean Squared Error
The root of the MSE function. RMSE cannot be calculated as the average of the root of the MSE values. This is Jensen's inequality. RMSE reduces the inflation of error caused by the square term. Useful when large errors are undesirable.


#### 3. Mean Absolute Error
Absolute value of the loss for each observation. MAE is linear, and does not give any additional weight to different types of errors. MAE is robust to outliers, this it is not suitable for situations where you want to pay more attention to outliers.


#### 4. $R^2$ Error

$$R^2 = 1 - \frac{MSE(model)}{MSE(baseline)}$$
MSE(baseline) is also just the mean. $R^2$ is a scale-free score that compares the model versus the mean. This tells you how much of the variation between the target variable can be explained by the explanatory variables. If $R^2$ is 1 (or 100%) that means 100% of the target variable can be explained by the explanatory variables, i.e. they are perfectly linear.


#### 5. Adjusted $R^2$
n = rows, K = Features
$$R_a^2 = 1 - [\frac{n-1}{n-K-1} * (1-R^2)]$$
This adjusts for the increasing predictors and only shows improvement if there is real improvement improves on the flaw that $R^2$ scores on increasing terms even though the model isn't improving.