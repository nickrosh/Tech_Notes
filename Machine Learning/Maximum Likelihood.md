
Let's say we want to model a dataset as a gaussian distribution. How are we to find the right mean and standard deviation for the distribution?

![](../Attachments/Pasted%20image%2020230225175426.png)

We can place the mean of the gaussian over different values. We can plot the *likelihoods* (probability that the data would show up in the distribution) and we see that there is specific value that maximizes the likelihood of the distribution. We can then repeat this process to get the max likelihood for standard deviation. This will then provide the max likelihood of the distribution. This simple estimation has many uses such as in [Logistic Regression](Logistic%20Regression.md) and [Neural Networks](Deep%20Learning/Neural%20Networks.md).