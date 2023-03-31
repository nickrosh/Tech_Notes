
Recommender Systems generate recommendations for users on items. This ranges from product recommendations, to content recommendations, to ranked search results, etc. This use case is probably the most actively used ML system in real world applications. Almost every single website includes some sort of recommender system.

![](../../Attachments/Pasted%20image%2020230310004005.png)

## Content-Based Filtering

*Content-Based* recommender systems takes handmade [features](../ML%20Engineering/Preprocessing.md) and uses them to recommend other items similar to what the user likes, based on their previous actions or explicit feedback. Content-based filtering doesn't look at other users, this makes it easier to scale to a large number of users. This method can also capture the specific interests of a user, and can recommend niche items that very few other users are interested in.

However, since the feature representation of the items are hand-engineered to some extent, this technique requires a lot of domain knowledge. Therefore, the model can only be as good as the hand-engineered features. Also, the model can only make recommendations based on existing interest of the user. In other words, the model has limited ability to expand on the user's existing interests.

![](../../Attachments/Pasted%20image%2020230310005149.png)

Content-Based filtering is item to item comparison via taking their cosine similarity.

## Collaborative Filtering

Collaborative filtering recommends items that similar users have chosen. This is a user to user method where we take a similar user to the one in question to get the ideal recommendations.

![](../../Attachments/Pasted%20image%2020230310005356.png)

#### Matrix Factorization

Matrix factorization view is to take the sparse "review" matrix of the users and corresponding items, and break it down into two dense matrices that correspond to the user and item dimensions.

![](../../Attachments/Pasted%20image%2020230310005825.png)

We then use these two matrices to try to "rebuild" the review matrix. We train the weights based on a square [Loss Function](Loss%20Functions.md) with a [Regularization](../Regularization.md) term to get the loss. This training to regenerate the known values of the review matrix generates recommendations for all missing values in the matrix. The resultant User and Item matrices are the [Embeddings](Embeddings.md) for the users and items. We can also discard the review matrix as the User and Item matrices are all we need to store to regenerate the matrix whenever needed. This is the method that was made famous in the million dollar Netflix Prize.

#### Deep Learning Methods

Another way of framing collaborative filtering is to take the User and Item [Embeddings](Embeddings.md) and feed them through [Feed Forward Neural Network](Neural%20Networks.md) layers to get deep matrix factorization.

![](../../Attachments/Pasted%20image%2020230310010104.png)

Unfortunately, empirical evidence so far has shown that the deep learning method has not outperformed the traditional matrix factorization method.


## Hybrid Models

Modern recommendation systems combine multiple different methods in a hybrid model. Causal data is also used to help create better recommendations. The biggest pitfalls are feedback loops, gettering the same recommendations in a loop, and cold starts, not having good recommendations when just starting out. At the end of the day, getting the right [ML Metrics](../ML%20Metrics.md) is critical for recommenders, as the "quality" of a recommendation is a very difficult thing to measure.