
A Decision [Trees](../Data%20Structures%20&%20Algorithms/Data%20Structures/Trees.md) is a supervised ML algorithm for [Classification](Classification.md) and [Regression](Regression.md) tasks. Decision trees use methods like *Gini Impurity* to decide the optimal tree structure based on a dataset.

![](../Attachments/Pasted%20image%2020230223020717.png)

Impurity refers to the fact that the training data will not line up 100% with the target variable. Say we are trying to figure out if someone is fit, and the feature is that they eat pizza. Based on the training data, the Gini Impurity of each leaf (True and False) of the Boolean pizza feature is $1 - \text{probability of "True"}^2 - \text{probability of "False"}^2$ . To get the total impurity, we weight each feature by the number of observations of each class. Thus we sum all of the leaf impurities weighted by the number of observations in that feature class.

We keep calculating impurities and looking at the most optimal splits, until we get to a situation where all of the leaf nodes are pure, meaning that all observations are either True or False. We then assign output values to each leaf.


## In Practice

Decision trees are relatively powerful, and used especially for their explainability. Unlike [Neural Networks](Deep%20Learning/Neural%20Networks.md), you can actually look at exactly why a decision tree made a particular inference. This is especially important in regulated industries like Finance.

Decision Trees can also be prone to [overfitting](Bias%20vs.%20Variance.md). When you get a tree with only pure leaves, then there may be the chance that some leaves have very small numbers of observations. You can either *prune* the tree to try to get a better tree, or to simply set a minimum number of observations per leaf. This however might lead to impure leaves, which might still be preferable to overfitting.