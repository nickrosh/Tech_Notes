
*K-Means* is an unsupervised [Clustering](Clustering.md) algorithm that partitions a dataset into K clusters in which each observation belongs to the cluster with the nearest mean.

![](../Attachments/Pasted%20image%2020230223013819.png)


## Implementation

1. Randomly select K data points in the dataset. These are our initial clusters
2. Assign every point to its nearest Euclidean cluster.
3. Calculate the Mean of each cluster now that every point is in a cluster
4. Reassign every point to the new cluster means
5. Repeat steps 3 & 4 until no point changes assignment after mean change. This means that the algorithm has reached a stable point.

This will be performed several times, and the one that comes back with the most equal variances among clusters is returned as the best one. This can be repeated as many times as wanted.


## Selecting K

We can look at the total variance among all clusters at many values of K. Each time we add a new cluster, the total variation within each cluster is smaller than before. And when there is only one point per cluster, the variation is zero. However, if we plot the reduction in variation per value for K, we usually see an "elbow" from when the variation stops dropping quickly; this is the optimal value for K. This method is similar to selecting components with [PCA](PCA.md).

![](../Attachments/Pasted%20image%2020230223015803.png)