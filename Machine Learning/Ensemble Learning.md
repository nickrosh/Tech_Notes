
We can greatly increase the [metrics](ML%20Metrics.md) and overall performance of a model by utilizing *ensemble learning*. This refers to using multiple models trained separately, and using the quorum decision of all the models, rather than the single point of failure of one model.

## Bagging

*Bootstrap Aggregation* or *Bagging* is a meta-algorithm utilizing the simple quorum of many models training on subsets of the overall dataset. The most famous example is *Random Forests* which is multiple [Decision Trees](Decision%20Trees.md) utilizing bagging. The process is as follows:

1. Create a "bootstrapped" dataset. We randomly select observations from the dataset (note that we are allowed to select the same observation twice) until our dataset is as big as the original one.
2. Create a [Decision Trees](Decision%20Trees.md) using the bootstrapped dataset, but only use a random subset of variables (or columns) at each step.
3. Go back to steps 1 & 2 and repeat this, this can be repeated as many times as you want, or as much that your [Memory & Cache](../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) can handle. This is the random forest

![](../Attachments/Pasted%20image%2020230225150236.png)

When we want to run inference, we run the data through every tree in the random forest. The response we give is the one that has a plurality of votes from all the trees (or average for regression).


## Boosting

*Gradient Boosting* is a powerful method of ensemble learning. 

1. First weight is initialized for the target variable
2. A [Decision Tree](Decision%20Trees.md) is constructed based on the residuals, the difference between the observed values and the single value predicted by the initialized weight. Then output values are calculated for each leaf of the tree, and they are all multiplied by the learning rate (hyperparameter default = 0.1).
3. Then we build another tree based on the new residuals, the difference between the observed values and the values predicted by the initial value and the first tree (and any other previous tree). Then we calculate the outputs and scale by the learning rate
4. Step 3 is repeated until we reach the maximum number of trees specified (hyperparameter) or the residuals get too small.

![](../Attachments/Pasted%20image%2020230225150331.png)


This creates many weak learners that give us much more accurate predictions than a single strong learner.


#### XGBoost

XGBoost is an advanced version of gradient boosting with other optimizing strategies, such as second order Taylor approximation in the loss function. XGBoost is the State-of-the-Art model for tabular data.