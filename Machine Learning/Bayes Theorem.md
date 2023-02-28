

>*Steve is very shy and withdrawn, with little interest in people or in the world of reality. A meek and tidy soul, he has a need for order and structure, and a passion for detail*

Is Steve a Farmer or a Librarian? Most humans would say that Steve is a librarian given the description. But there are at least 60x more farmers than there are librarians, thus it is much more likely that a meek and tidy person like Steve would just be a farmer. This is a prime example of Bayesian Probabilities. New evidence should not completely determine beliefs in a vacuum, it should update *prior probabilities*.

*Bayes Theorem* describes the probability of an event, based on prior knowledge of conditions that are related to the event. The probability of a hypothesis given certain evidence, known as the "posterior" $P(H|E)$ (the probability that Steve is a librarian given the total percentage of librarians) is equal to the probability of the hypothesis, known as the "prior" $P(H)$ (proportion of librarians), times the probability that we would see the evidence given the probability is true, known as the "likelihood" $P(E|H)$ (proportion of librarians that fit Steve's description), divided by the probability of the evidence being true, $P(E)$. This is also calculated as the numerator plus the inverse of the hypothesis (proportion of farmers that fit the description times the proportion of farmers).

Bayes Theorem is elegantly described in the following equation:

$$P(H|E) = \frac{P(H)P(E|H)}{P(E)}$$


## Naïve Bayes Classifier

We can use Bayes Theorem to construct a multinomial [classifier](Classification.md). We take our training dataset and calculate the likelihoods and priors of the entire dataset. A common issue is that if a sample is not found in the training data, the probability would be zero, and would zero out an entire calculation. The way to get around this is to add 1 to the frequency of every type of data.

The reason why this model is called naïve is that it only looks at total probabilities. In an email spam classifier, it would treat every email as a bag-of-words probability model. As there is no investigation into the relationship among samples, this classifier will have high [Bias](Bias%20vs.%20Variance.md) but low variance. Even still, this classifier usually performs decently, especially given how simple it is to compute compared to a [gradient descent](Deep%20Learning/Optimizers.md) optimized model.


## Gaussian Naïve Bayes Classifier

![](../Attachments/Pasted%20image%2020230222025330.png)

This is similar to the above method. In this model, the means and standard deviations of the training dataset is converted into gaussian distributions. The priors are calculated, and these are multiplied by the likelihoods placed on the gaussian distributions. These are usually converted to log likelihoods in order to avoid [Floating Point](../Electrical%20Engineering/Digital/Floating%20Point%20Numbers.md) computation problems when a likelihood lies on a long tail of a gaussian distribution. The class with the highest probability is the selected class for the inference.

This has the same drawbacks as regular Naïve Bayes given that only the means and standard distribution of the dataset is considered for the model. If the data is not actually normally distributed, then this model will be flat out wrong.