
The *loss function* (also known as the *cost function*) is how we score how well a model did in inferencing training data compared to the ground truth label of that example. The difference of the real label and what the model thought it should be is the *loss* (or *cost*). Thus, in order to get all of the [weights and biases](Neural%20Networks.md) to have better [accuracy](../ML%20Metrics.md) when handling data, we must minimize this cost function.

![](../../Attachments/Pasted%20image%2020230226190438.png)

The above image is an example of what an example loss function looks like when it is plotted. Unlike convex optimization problems like [Linear Regression](../Linear%20Regression.md), the loss function of a Neural Network has many local minima, and other peaks and valleys. There are also saddle points, where the derivative is zero, but is neither a minima or maxima. This complexity of finding a good minima of our loss function is what motivates the need for advanced optimization algorithms. We always strive to reach the global minima, but this is so far not a solved problem as of 2023, and we can't even know if we've reached the global minima or not.

#### Mean Squared Error

Mean squared error is an extremely simple loss function, and is usually the function of choice for [Regression](../Regression.md) tasks. This function is good for ensuring out trained model has no outlier predictions with huge errors, since MSE puts larger weight on these errors due to the square term. The disadvantage is that bad predictions are greatly magnified due the very same square term. Thus outliers have an outsized effect on MSE
$$MSE=\frac{1}{N}\sum_{i=1}^N(y_i-\hat{y}_i)^2$$
*Mean Absolute Error* is only slightly different than MSE, but it doesn't have the issue of magnified losses. This is the preferable function if you don't want outliers to have a large effect.

#### Cross-Entropy

*Cross Entropy* or *log loss* is the most popular loss function for [Classification](../Classification.md) tasks. Cross entropy measures the performance of a classification model whose output is a probability value between 0 and 1. Cross entropy loss increases as the predicted probability diverges from the actual label. So predicting a probability of 0.012 when the actual observation label is 1 would result in a high loss value. Cross entropy is related to divergence measures such as KL divergence, that quantify how much one distribution differs from another.
$$CE = -\log{P_i}$$
The cross entropy value for an observation is the negative log of its probability output, taken from the [SoftMax function](Activation%20Functions.md). The reason why we use cross entropy is that we want to heavily penalize very bad predictions. When the prediction is very wrong, the probability will be close to zero. The negative log of values close to zero are very large, and this pushes the [Optimizer](Optimizers.md) like gradient descent to make a large step to correct the error as the slope of the steep value will be very large.