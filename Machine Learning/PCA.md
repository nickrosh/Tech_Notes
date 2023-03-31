
*Principal Component Analysis* is a method of [feature extraction](ML%20Engineering/Preprocessing.md) to reduce dimensionality of a dataset while retaining the distribution of the dataset.

![](../Attachments/Pasted%20image%2020230223012536.png)

The *principal components* describe the directions of maximum variance of a dataset. Thus, by using only a few principal components, we can describe almost all of the variance of the dataset.

## Implementation

1. [Standardize](Scaling.md) the data. We want the mean to be zero.
2. Calculate the covariance matrix for the features in the dataset. Covariance measures how two features vary with each other. A positive covariance indicates that features increase and decrease together. Whereas, a negative covariance means that the two features vary in the opposite directions.
3. The *eigenvectors* represent the principal components of the covariance matrix. The *eigenvalues* are their corresponding magnitudes.
4. Now sort these pairs according to the eigenvalues. These will tell you which components describe the most variance in the dataset. These are your principal components
5. Finally, we recast the data along the principal components.  just take the dot product of the data and the new principal components.


## Selecting Components

An important question in the applied use of PCA is how many principal components to use. When you plot the eigenvalues of the components, you will see a curve that usually has a "elbow". 

![](../Attachments/Pasted%20image%2020230223013103.png)

We generally use this point as the number of components to use. The components after this point describe less and less of the distribution. This way you have a generally good reduction in dimensionality while keeping the majority of the variance of the dataset.