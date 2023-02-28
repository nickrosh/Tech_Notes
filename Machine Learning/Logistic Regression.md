
*Logistic Regression* models the probability of data belonging in a class based on training data.

## Implementation

![](../Attachments/Pasted%20image%2020230225174433.png)

1. Place a [sigmoid](Deep%20Learning/Activation%20Functions.md) function on the mean of the dataset.
2. Project the data onto the function and calculate the sum of log likelihoods for the data and the distribution. Start by calculating the likelihoods of True cases, which is the same as the predicted probability (y axis value when projected onto the sigmoid). Then take the likelihoods of the False cases, which is 1 minus the predicted probability. We take the log of these likelihoods to simplify the calculation and them sum them
3. Shift the sigmoid function and recalculate the log likelihoods. Continue to shift the function until you have found the function that gives you the [Maximum Likelihood](Maximum%20Likelihood.md) set to the data. This is the best fit for the model.