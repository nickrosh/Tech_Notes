
*Regularization* is used to provide a purposeful penalty to training for learning models. This is done to prevent overfitting and decrease [Variance](Bias%20vs.%20Variance.md) at the cost of increased Bias.

## L2 (Ridge Regression)

![](../Attachments/Pasted%20image%2020230225182241.png)

*L2 Regularization* (Also known as Ridge Regression) is the most popular type of regularization. It changes the regular equation of a [Linear Regression](Linear%20Regression.md) by introducing a penalty term that takes a hyperparameter lambda is multiplied by the square of slope. The loss function is thus changed to the following:
$$\sum{\text{residuals}^2} +\lambda\text{slope}^2$$

Ridge Regression can also be done for [Logistic Regression](Logistic%20Regression.md). It adds a penalty to the sum of log likelihoods and thus makes the value of [Maximum Likelihood](Maximum%20Likelihood.md) less sensitive to the explanatory variables.

## L1 (Lasso Regression)

*L1 Regularization* is very similar to L2, except instead of the penalty term using the squared slope, it uses the absolute value of the slope. Due to this absolute value term, L1 tends to shrink less important feature weights to zero. This reduces dimensionality, but also tends to put the model weights to zero, which may not be truly generalizable.

$$\sum{\text{residuals}^2} +\lambda|\text{slope}|$$

## ElasticNet

![](../Attachments/Pasted%20image%2020230225182509.png)

ElasticNet combines both L1 and L2 regularization. Thus is provides the benefits of both. The generalizability of L2 and the feature selection of L1.

$$\sum{\text{residuals}^2} +\lambda|\text{slope}|+\lambda\text{slope}^2$$