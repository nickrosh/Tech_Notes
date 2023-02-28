

**Clustering** is unsupervised learning technique that clusters data into a number of groups

![](../Attachments/Pasted%20image%2020230222025546.png)


## Comparison of Algorithms


### [K Means](K%20Means.md)

| Pros | Cons |
| --- | --- |
| Fast, simple, flexible if the data is well processed | User must specify *K* number of clusters |
| | If the true underlying clusters are not globular, then K-Means will produce poor clusters |


### Affinity Propagation

| Pros | Cons |
| --- | --- |
| Creates clusters based off graph-distances between points | Slow and memory intensive |
| | Assumes underlying clusters are globular |


### Spectral Clustering

| Pros | Cons |
| --- | --- |
| Nearest-neighbor clustering allows non-globular clusters | User must specify *K* number of clusters |
| | Still memory intensive |


### Hierarchical

| Pros | Cons |
| --- | --- |
| Does not assume clusters are globular | User must specify *K* number of clusters |
| Scales well to large datasets | |


### DBSCAN

| Pros | Cons |
| --- | --- |
| Does not assume clusters are globular, this scalable | User must tune its sensitive hyperparameters |
| Doesn't require every point to be in a cluster | |


### HDBSCAN

| Pros | Cons |
| --- | --- |
| Does not assume clusters are globular, this scalable | User must tune its sensitive hyperparameters |
| Fixes the varying density clusters issue of DBSCAN | |