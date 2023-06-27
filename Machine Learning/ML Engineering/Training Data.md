
Data is the heart of machine learning. Acquiring more data and improving data quality can help [model metrics](../ML%20Metrics.md) much more than hyperparameter tuning. Thus a huge premium is placed on the acquisition, curation, maintenance, and [Preprocessing](Preprocessing.md) of training data.


## Sampling

Sampling happens in many steps of an ML project lifecycle, such as sampling from all possible real-world data to create training data; sampling from a given dataset to create splits for training, validation, and testing; or sampling from all possible events that happen within your ML system for monitoring purposes. In many cases, sampling is necessary. One case is when you don't have access to all possible data in the real world, the data that you use to train your model is a subset of real-world data, created by one sampling method or another. Another case is when it's infeasible to process all the data that you have access to, because it requires too much time or resources, so you have to sample that data to create a subset that is feasible to process.

#### Nonprobability Sampling

*Nonprobability Sampling* is when the selection of data isn't based on any probability criteria. Here are some of the criteria for nonprobability sampling.
- *Convenience Sampling*: Samples of data are selected based on their availability. This sampling method is popular because, well, it's convenient.
- *Snowball Sampling*: Future samples are selected based on existing samples. For example, to scrape legitimate Twitter accounts without having access to Twitter databases, you start with a small number of accounts, then you scrape all the accounts they follow, and so on.
- *Judgement Sampling*: Experts decide what samples to include.
- *Quota Sampling*: You select samples based on quotas for certain slices of data without any randomization. For example, when doing a survey, you might want 100 responses from each of the age groups, regardless of the actual age distribution.

These methods are not representative of the real-world distribution of data and are thus prone to bias. It is not recommended to use these methods when possible

#### Random Sampling

With *Simple Random Sampling*, you give all samples in the population equal probabilities of being selected. The advantage of this method is that it's easy to implement. The drawback is that rare categories of data might not appear in your selection. Consider the case where a class appears in only 0.01% of your data population. If you randomly select 1% of your data, samples of this rare class will unlikely be selected. Models trained on this selection might think that this rare class doesn't exist

We can use *Stratified Sampling* to avoid this drawback. You can first divide your population into the groups that you care about and sample from each group separately. For example, to sample 1% of data that has two classes, A and B, you can sample 1% of class A and 1% of class B. This way, no matter how rare class A or B is, you'll ensure that samples from it will be included in the selection. Each group is called a *stratum*. One possible drawback of this sampling method is that it isn't always possible, such as when it's impossible to divide all samples into groups. This is especially challenging when one sample might belong to multiple groups, as in the case of multilabel tasks. For instance, a sample can be in both class A and B.

#### Weighted Sampling

In Weighted Sampling, each sample is given a weight, which determines the probability of it being selected. For example, if you have three samples, A, B, and C, and want them to be selected with the probabilities of 50%, 30%, and 20% respectively, you can given them the weights of 0.5, 0.3, and 0.2. This method allows you to leverage domain expertise. For example, if you know that a certain subpopulation of data, such as more recent data, is more valuable to your model and want it to have a higher chance of being selected, you can give it a higher weight. This also helps with the case when the data you have comes from a different distribution compared to the true data. For example, if in your data, red samples account for 25% and blue samples account for 75%, but you know that in the real world, red and blue have equal probabilities, you have give red samples weights three times that of blue.

A common concept in ML that is closely related to weighted sampling is *sample weights*. Weighted sampling is used to select samples to train your model with, whereas sample weights are used to assign "weights" or "importance" to training samples. Samples with higher weights affect the loss function more. Changing sample weights can change your model's decision boundaries significantly.

#### Reservoir Sampling

*Reservoir Sampling* is an algorithm that is especially useful when you have to deal with streaming data, which is usually what you have in production. Imagine you have an incoming stream of tweets and you want to sample a certain number *K*, of tweets to do analysis or train a model on. You don't know how many tweets there are, but you know you can't fit them all in memory, which means you don't know in advance the probability at which a tweet should be selected. You want to ensure that:
- Every tweet has an equal probability of being selected.
- You can stop the algorithm at any time and the tweets are sampled with the correct probability.

One solution for this problem is reservoir sampling. The algorithm involves a reservoir, which can be an [Array](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Arrays.md), and consists of three steps:
1. Put the first K elements in the reservoir
2. For each incoming Nth element, generate a random number i such that $1 \le i \le n$.
3. If $1 \le i \le k$: replace the ith element in the reservoir with the Nth element. Else, do nothing.

![](../../Attachments/Pasted%20image%2020230611182909.png)

This means that each incoming Nth element has $\frac{k}{n}$ probability of being in the reservoir. This means that all samples have an equal change of being selected. If we stop the algorithm at any time, all samples in the reservoir have been sampled with the correct probability. 

#### Importance Sampling

*Importance Sampling* is one of the most important sampling methods, not just in ML. It allows us to sample from a distribution when we only have access to another distribution. Imagine you have to sample x from a distribution P(x), but P(x) is really expensive, slow, or infeasible to sample from. However, you have a distribution Q(x) that is a lot easier to sample from. So you sample x from Q(x) instead and weight this sample  by $\frac{P(x)}{Q(X)}$. Q(x) is called the *Proposal Distribution* or the *Importance Distribution*. Q(x) can be any distribution as long as Q(x) > 0 whenever P(x) != 0. 

One example where importance sampling is used in ML is policy-based reinforcement learning. Consider the case when you want to update your policy. You want to estimate the value function of the new policy, but calculating the total rewards of taking an action can be costly because it requires considering all possible outcomes until the end of the time horizon after that action. However, if the new policy is relatively close to the old policy, you can calculate the total rewards based on the old policy instead and reweight them according to the new policy. The rewards from the old policy make up the proposal distribution.

## Labeling

Supervised learning is still the king of ML, meaning that most production models require labeled data. The performance of an ML model still depends heavily on the quality and quantity of the labeled data it's trained on.

#### Hand Labels

Acquiring hand labels for your data can be difficult for many, many reasons. First: hand labeling data can be expensive, especially if subject matter expertise is required. To classify whether a comment is spam, you might be able to find 20 annotators on a crowdsourcing platform and train them in 15 minutes to label your data. However, if you want to label chest X-rays, you'd need to find board-certified radiologists, whose time is limited and expensive. Second, hand labeling poses a threat to data privacy. Hand labeling means that someone has to look at your data, which isn't always possible if your data has strict privacy requirements. For example, you can't just ship your patients' medical records or your company's confidential financial information to a third part service for labeling. Third, hand labeling is slow. For example, accurate transcription of speech utterance at the phonetic level can take 400 times longer than the utterance duration. This slow labeling leads to a slow iteration speed and makes your model less adaptive to changing environments and requirements. If there is [concept drift or model drift](Model%20Monitoring.md), you'll have to wait for your data to get relabeled.

Often to obtain enough labeled data, companies have to use data from multiple sources and rely on multiple annotators who have different levels of expertise. This leads to the problem of *label ambiguity* or *label multiplicity*: what to do when there are multiple conflicting labels for a data instance? Disagreements among annotators are extremely common. The higher the level of domain expertise required, the higher the potential for annotating disagreements. To minimize disagreements among annotators, it's important to first have a clear problem definition. For example, saying that in the case of a string with multiple possible entities, pick the entity that comprises the longest substring.

Indiscriminately using data from multiple sources, generated with different annotators, without examining their quality can cause your model to fail mysteriously. Consider a case when you've trained a moderately good model with 100K data samples. Your ML engineers are confident that more data will improve the model performance, so you spend a lot of money to hire annotators to label another million samples. However, you see the performance actually decrease. The reason is that the labeling was crowdsourced and of lower quality than the original data. It's good practice to keep track of the origin of each of your data samples as well as its labels, a technique known as *Data Lineage*. Data lineage helps you both flag potential biases in your data and debug your models. For example, if your model fails mostly on the recently acquired data samples, you might want to look into how the new data was acquired. Bad labels are a common reason for model degradation.

#### Natural Labels

If you're lucky, you might work on tasks with natural ground truth labels. Tasks with *Natural Labels* are tasks where the model's predictions can be automatically evaluated or partially evaluated by the system. An example is the model that estimates time of arrival for a certain route on Google Maps. If you take that route, by the end of your trip, Google Maps knows how long the trip actually took, and thus can evaluate the accuracy of the predicted time of arrival. Another example is stock price predictions. The canonical example of tasks with natural labels is [Recommender Systems](../Deep%20Learning/Recommender%20Systems.md). The goal of a recommender system is to recommend to users items relevant to them. Whether a user clicks on the recommended item or not can be seen as the feedback for that recommendation. A recommendation that get s clicked on can be presumed to be good (i.e. label is POSITIVE) and a recommendation that doesn't get clicked on after a period of time can be presumed to be bad.

Many tasks can be framed as recommendation tasks. For example, you can frame the task of predicting ads click-through rates as recommending the most relevant ads to users based on their activity histories and profiles. Natural labels that are inferred from user behaviors like clicks and ratings are also known as *behavioral labels.*

Even if your task doesn't inherently have natural labels, it might be possible to set up your system in a way that allows you to collect some feedback on your model. For example, if you're building a machine if you're building a machine translation system like Google Translate, you can have the option for the community to submit alternative translations for bad translations. These alternative translations can be used to train the next iteration of your models (though you might want to review these suggested translations first). Newsfeed ranking is not a task with inherent labels, but by adding the Like button and other reactions to each newsfeed item, Facebook is able to collect feedback on their ranking algorithm.

In the previous example, a recommendation that doesn't get clicked on after a period of time can be presumed to be bad. This is called an *implicit label*, as this negative label is presumed from a lack of positive label. This is different than the *explicit label* where users explicitly demonstrate their feedback.


#### Feedback Loop Length

For tasks with natural ground truth labels, the time it takes from when a prediction is served until when the feedback on it is provided is the feedback loop length. Tasks with short feedback loops are tasks where labels are generally available within minutes. Many recommender systems have short feedback loops. If the recommended items are related products on Amazon or people to follow on Twitter, the time between when the item is recommended until it's clicked on, if it's clicked on at all, is short.

However, not all recommender systems have minute-long feedback loops. If you work with longer content types like blog posts or articles or YouTube videos, the feedback loop can be hours. If you build a system to recommend clothes for users like the one Stitch Fix has, you wouldn't get feedback until users have received the items and tried them on, which could be weeks later.

If you want to extract labels from user feedback, it's important to note that there are different types of user feedback. They can occur at different stages during a user journey on your app and differ by volume, strength of signal, and feedback loop length. For example, consider an ecommerce application similar to Amazon. Types of feedback a user on this application can provide might include clicking on a product and returning a previously bought product. Clicking on a product happens much faster and more frequently (and therefore incurs a higher volume) than purchasing a product. However, buying a product is a much stronger signal on whether a user likes that product compared to just clicking on it. Many companies focus on clicks, and others focus on purchases. Both are valid metrics, it depends on your use case.


## Handling a Lack of Labels

Because of the challenges in acquiring sufficient high-quality labels, many techniques have been developed to address the problems that result. Here is a summary:

| Method            | How                                                           | Ground Truths Required?                                                                                                                                                        |
| ----------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Weak Supervision  | Leverages (often noisy) heuristics to generate labels         | No, but a small number of labels are recommended to guide the development of heuristics                                                                                        |
| Semi-Supervision  | Leverages structural assumptions to generate labels           | Yes, a small number of initial labels as seeds to generate more labels                                                                                                         |
| Transfer Learning | Leverages models pretrained on another task for your new task | No for zero-shot learning. Yes for fine-tuning though the number of ground truths required is often much smaller than what would be needed if you train the model from scratch |
| Active Learning   | Labels data samples that are most useful to your model        | Yes                                                                                                                                                                            |


#### Weak Supervision

The insight behind Weak Supervision is that people rely on heuristics, which can be developed with subject matter expertise to label data. Here are some heuristics that can be used:
- Keyword Heuristic: if one or more keywords show up in a text
- Regular Expressions: using [Regex](../../Software%20Engineering/Regex.md) to match on a pattern
- Database Lookup: Such as if there is a match with an index of things you are looking for
- The outputs of other models

These heuristic based labels are noisy and imperfect. It's important to combine, denoise, and reweight all functions to get a set of most likely to be correct labels. You technically don't need any hand labels for weak supervision. However to get a sense of how accurate your heuristics are, it is recommended to get a small number of hand labels.

Weak supervision can be especially useful when your data has strict privacy requirements. You only need to see a small, cleared subset of data to write the functions, which can be applied to the rest of the dataset. With heuristics, subject matter expertise can be versioned, reused, and shared. Expertise owned by one team can be encoded and used by another team. If your data changes or your requirements change, you can just reapply your functions to your data samples. This is also known as *programmatic labeling*. Here are the advantages versus Hand Labels:

| Hand Labeling                                                    | Programmatic Labeling                                                                                                                 |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Expensive: Especially when subject matter expertise is required  | Cost Saving: Expertise can be versioned, shared, and reused across an organization                                                    |
| Lack of Privacy: Need to ship data to human annotators           | Privacy: Create functions using a cleared data subsample and then apply functions to other data without looking at individual samples |
| Slow: Time required scales linearly with number of labels needed | Fast: easily scale from 1K to 1M samples                                                                                              |
| Non-adaptive: Every change requires relabeling the data          | Adaptive: When changes happen, just reapply functions                                                                                 |
|                                                                  |                                                                                                                                       |


#### Semi-Supervision

If weak supervision leverages heuristics to obtain noisy labels, *semi-supervision* leverages structural assumptions to generate new labels based on a small set of initial labels. Unlike weak supervision, semi-supervision requires an initial set of labels. A classic semi-supervision method is self-training. You start by training a model on your existing set of labeled data and use this model to make predictions for unlabeled samples. Assuming that predictions with high raw probabilities scores are correct, you add the labels predicted with high probability to your training set and train a new model on this expanded training set. This goes on until you're happy with your model performance. Another method is using the assumption that like-labeled data should be similar, and thus you can use [Clustering](../Clustering.md) to generate labels.


#### Transfer Learning

Transfer learning refers to the family of methods where a model developed for a task is reused as the starting point for a model on a second task. First, the base model is trained for a base task that has cheap and abundant training data. Language Modeling is a great task for [NLP](../Deep%20Learning/NLP.md). This has gained huge popularity with the large language [Transformers](../Deep%20Learning/Transformers.md) like BERT and GPT. Earlier, [Convolutional Neural Net](../Deep%20Learning/Convolutional%20Neural%20Net.md) pretrained on ImageNet would be fine tuned to a task.

#### Active Learning

Active Learning is a method for improving the efficiency of data labels. The hope here is that ML models can achieve greater accuracy with fewer training labels if they can choose which data samples to learn from. Instead of randomly labeling data samples, you label the samples that are most helpful to your models according to some metrics or heuristics. The most straightforward metric is uncertainty measurement, label the examples that your model is least certain about. For example, in a [Classification](../Classification.md) problem, you would choose predictions with the lowest probabilities for the predicted class.

## Class Imbalance

Class imbalance typically refers to the problem in [Classification](../Classification.md) tasks where there is a substantial difference in the number of samples in each class of the training data. For example, in a training dataset for the task of detecting lung cancer, 99.99% of the X-rays might be of normal lungs and only 0.01% might contain cancerous cells. A model that always predicts "Not Cancer" would be 99.99% accurate.

Class imbalance also happens with [Regression](../Regression.md) tasks where the labels are continuous. Consider the task of estimating health-care bills. Health-care bills highly skewed. The median bill is low, but the 95th percentile could be massive. A 100% difference in a $250 bill would be $500, acceptable, but a 100% difference on a $10k bill is not (actual $20k, predicted $10k). Thus we might have to train the model to be better at predicting 95th percentile bills, even if it reduces the overall metrics.

#### Class Agnostic Metrics

[Classification Metrics](../ML%20Metrics.md) like F1, Precision, and Recall are metrics that measure your model's performance with respect to the positive class in binary classification problems as they reply on true positive, an outcome where the model correctly predicts the positive class

Many classification tasks can be modeled as regression problems. Your model can output a probability, and based on that probability, you classify the sample. For example, if the value is greater than 0.5, it's a positive label, and if it's less than or equal to 0.5, it's a negative label. This means that you can tune the threshold to increase the *true positive rate* (also known as recall), and vice versa. We can plot the true positive rate against the false positive rate for different thresholds. This plot is known as the ROC curve.

#### Resampling

We can modify the distribution of the training data to reduce the level of imbalance to make it easier for the model to learn. A common family of techniques is *resampling*. *Oversampling* is when you add more instances from the minority class. *Undersampling* is when you remove instances of the majority class.

![](../../Attachments/Pasted%20image%2020230613200956.png)

A popular method of undersampling low-dimensional data is *Tomek links*. With this technique, you find pairs of samples from opposite classes that are close in proximity and remove the sample of the majority class in each pair. While this makes the decision boundary more clear and arguably helps models learn the boundary better, it may make th8e model less robust because the model doesn't get to learn from the subtleties of the true decision boundary.

A popular method of oversampling low-dimensional data is *SMOTE* (synthetic minority oversampling technique). It synthesizes novel samples of the minority class through sampling convex combinations of existing data points within the minority class. Both these methods have only been proven effective for low-dimensional data. It can't work well with high-dimensional data or in high-dimensional feature space, such as the case of large [Neural Networks](../Deep%20Learning/Neural%20Networks.md).


#### Algorithm-Level Methods

If data-level methods like resampling mitigate the challenge of class imbalance by altering the distribution of your training data, algorithm-level methods keep the training data distribution intact but alter the algorithm to make it more robust to class imbalance.

Because the [Loss Functions](../Deep%20Learning/Loss%20Functions.md) guides the learning process, many algorithm-level methods involve adjustment to the loss function. You can create a class-balanced loss which takes the proportion of each class into account when calculating loss. In its vanilla form, we can make the weight of each class inversely proportional to the number of samples in that class, so that the rarer classes have higher weights. This can be weight times cross entropy or whatever loss function you choose. A more sophisticated version of this loss can take into account the overlap among existing samples, such as class-balanced loss based on effective number of samples.

## Data Augmentation

![](../../Attachments/Pasted%20image%2020230618153844.png)

We'll often want to increase the size and diversity of our training data split through data augmentation. It involves using the existing samples to generate synthetic, yet realistic examples.

1. Split the Dataset: We want to split our dataset first because many augmentation techniques will cause a form of data leak if we allow the generated samples to be placed across different data splits.
2. Augment the training split: We want to apply augmentation on only the training set becuase our validation and testing splits should be used to provide an accurate estimate on actual data points.
3. Inspect and validate: it's useless to augment just for the sake of increasing our training sample size if the augmented data samples are not probable inputs that our model could encounter in production.