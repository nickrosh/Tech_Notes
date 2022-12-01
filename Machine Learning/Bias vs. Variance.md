

![](Pasted%20image%2020220831232410.png)

**Bias** is the difference between average prediction of our model and the correct value which we are trying to predict. A model with high bias pays very little attention to the training data and oversimplifies the model. It always leads to high error on training and test data. This is called underfitting

**Variance** is the variability of model prediction for a given data point or a value which tells us the spread of the data. A model with high variance is overfitted to the training data and does not generalize on the data which it hasn't seen before.

We can see these effects below:
![](Pasted%20image%2020220831232611.png)

Thus, our goal is to optimize total error by finding the balance between bias and variance. This can be done by observing validation scores and stopping early as soon as the model has reached its lowest error score.

![](Pasted%20image%2020220831232738.png)