
In a Machine Learning System Design interview, the main focus is on Data and Modeling. This is because the general thrust of ML design interviews is to understand your thought process when faced with an (almost) real-world problem and data collection/preprocessing, as well as the model you will choose, will be core components of what you will build. Hence, you should focus on these areas the most in the interview.

![](../../Attachments/Pasted%20image%2020230606195022.png)


## Clarifying Requirements

The first thing that you should do when given a question is to clarify the requirements. For example. your question could be "*Design a system that recommends our products to users who have a profile*". Most ML system design questions are intentionally vague so that you would ask further questions in order to understand the scope and what components you should focus on.

After the interviewer gives you the prompt, **reword the prompt into your own words**. This ensures that you and the interviewer are on the same page and that you will answer the correct question. Then you can begin to think about how this system will affect the **user experience**. For the prompt above, we can ask questions such as:
- How will the user view the recommendations? Is it after they have clicked an item, on their home page, etc.?
- What is the current user experience? Do users have any recommendations currently?
- Can users interact with the recommendation (maybe favorite or block the recommendation)?

These questions will give you a better understanding of the customer experience as well as what the business wants to gain from this system which will guide you when thinking about online metrics. Next, we can begin to get into clarifying the **scope of this problem**:

1. **Scale of the Problem**: How much data would we have access to? How many users are on the platform? (for smaller datasets less complex models would be more appropriate but for bigger datasets, larger models like [Neural Networks](../Deep%20Learning/Neural%20Networks.md) could work)
2. **Hardware Constraints**: How much time do we have to complete the task? How much compute power is available? Do we have access to [GPU](../../Electrical%20Engineering/Digital/GPU.md)s or TPUs? (If we are limited on hardware size, then we should use simpler models).
3. **Model Constraints**: Do we need a model that is quick to respond to a request or do we need a model that is extremely accurate? Does this systems apply to all user segments? How quickly do items get stale (this gives us an idea about how often we need to retrain our model)? Would the model need to be interpretable? Should we build one model for all user segments (like countries) or should we build multiple models?

You should have the answers to all of your clarifying questions at this point. If there is an online doc, it would be helpful to write them down.

#### Metrics

Now that you have a clear idea of the user case and asked a few clarifying questions, you can use this information to determine the best [ML Metrics](../ML%20Metrics.md) to use when modeling. You should always give at least two metrics: one for offline, and one for online.

Offline metrics are those we used to score the model when we are building it. This is before it is put into production and shown to users. This is the typical scenario in research or tutorials where you split your dataset into three sets: train, test, and validation. Some examples of these offline metrics include F1, AUC, $R^2$, RMSE.

Online metrics are the scores we get from the model once it is in production serving requests. Online metrics could be the click-through rate or how long the users spends watching a video that was recommended. These metrics are use-case specific. You need to think about how a company would evaluate whether the model was useful to the users once it is in production.

Another set of metrics that would be useful are non-functional metrics. You can mention these to the interviewer to show that you are thinking of all the possible ways to measure the benefit of the model.


## Architecture (Optional)

The next step is to create a general architecture, some companies require you to draw the architecture. But most companies do not put a heavy premium on this aspect like in distributed [System Design Interviews](../../Distributed%20Systems/System%20Design/System%20Design%20Interviews.md). Once you talk about the flow of data from database/storage to serving the customer, most companies are satisfied.

Data collection (from what database/storage product) -> Data [Preprocessing](../ML%20Engineering/Preprocessing.md) (workflows) -> Modeling (one or more models on servers) -> serve (how will the user get this model output)

One decision you would have to make is whether the system would be real time or batch. With real time systems, the complexity of the system increases and we need to be aware of the lag time when our model is making predictions. On the other hand, batch systems are simpler to implement (you can pre-save many predictions) and allows more complex models. However, you can have issues with staleness.


## Data

This is the section where you would spend most of the time in the interview and where the interview would be looking to see how you perform.

1.  **First, identify the target variable and how you would collect and label it (if necessary).**
	- In the recommendations example, the target variable would be whether, historically, a user liked a product from the company. There are two ways we can collect this target value: implicitly or explicitly. An example of explicit target collection would be if we look at our logs and check whether someone bought a product. This means they liked the product enought to buy it. On the other hand, an implicit target collection would be if a user "saves for later" a product, or if a user views a product a certain number of times. Note that explicit data collection is usually the best way to collect the target variable for most cases. If you think you can find a way to collect the target variable implicitly, then have this discussion with your interviewer and then talk about the pros and cons of each of your implicit suggestions.

2. **Discuss Features and possible feature crosses**
	- Usually you can break features into 3 categories:
		- A. User: Age, gender, nationality, country, language, amount of friends they have, amount of purchases they made, etc.
		- B. Content: Title of product, how many people have purchased the product, number of times it was favorited, date added to the website, etc.
		- C. Context: Web browser used, login location, region, time of day, day of week, device, product clicked, etc.
	- Then we can talk about useful feature crosses.

3. **Data preprocessing/Feature Engineering**
	- **Processing our features**: We might have many different types of features (e.g. numeric, categorical, text, or even images or videos) and we should speak about how we would preprocess them before adding them to the model:
		- **Numeric**:
			- **Normalization** (min-max [Scaling](../Scaling.md)): shrinks our numeric values between 0 and 1. We would tend to use normalization when you know that the distribution of your data does not follow a gaussian distribution.
			- **Z-Score Scaling**: the values are centered around the mean with unit standard deviation. This method is better if the data follows a gaussian distribution. 
			- **Log Transform**: applies $\log(d)$ to every data point d. This method is used mainly when the data is not gaussian and this transformation makes the data more gaussian.
		- **Categorical**:
			- Sometimes it might be appropriate to use **label encoding**. This can cause problems with ordinal encoding e.g. if red was 1 and blue was 5, is blue fives times whatever red is?
			- If a categorical value has a dozen or fewer values, you can use **one hot encoding**
			- If there are more than a few values, then we can use **embeddings** to create a set of number dimensions for all our categories
			- Lastly we can **hash** each of our categories into a value, however you should be careful to avoid collisions
			- Sometimes there are only a few low frequency categories. If this is the case we can map those categories that appear only a handful of times in our data to a new category called "other"
		- **Text**:
			- **Tokenization**: we split the sentence into words and remove punctuation like commas from words
			- **Lower casing**: sometimes we might want to lower case our entire corpus. However we may not always want to do that, like maintaining proper nouns.
			- **Stop Word Removal**: Here we remove very commonly used words since they may not add significant information to the model. Again, this might not be wanted tin certain domains like translating languages
			- **Lemmatization**: reduces a word to its root word in the respective language e.g. caring -> care.
			- **Embeddings**: We can use [Embeddings](../Deep%20Learning/Embeddings.md) algorithms and even [Transformers](../Deep%20Learning/Transformers.md) like BERT to convert words or sentences to embeddings.
		- **Images**:
			- **Image standardization**: For ML models, we should have consistency amongst our data and standardization scales each image to have the same height and width.
			- **Grayscale Conversion**: covert colored images to black and white. This saves memory and many applications do not require the color of the image to perform well. However, if the question you are asked needs color then you should not perform this preprocessing step.
			- **Normalization**: just like for numeric values, we can scale all our pixels to be between 0 and 1 (sometimes between -1 and 1). This method ensures that all images in our dataset contribute equally and thus prevents bias from certain high pixel images.
			- **Data Augmentation**: This method makes alterations to our images to increase diversity and the number of images we have for training. Some augmentation techniques include flipping the image, cropping the image, and rotating the image.
	- Another important data issue that we need to handle is missing values:
		- We can drop the rows with missing data which is an easy method but we are losing information in our dataset.
		- We can use imputation. For categorical features, we can substitute the missing value for the max/min/most frequent value. For numeric values we can use the average, median, or even create a model to impute these missing values.
		- A less popular option is to create a new feature with "1" or "0" if the value is not missing or not.
	- Sometimes our data has outliers, you should ask the interviewer if there are outliers in the data. If there are, you can use some of the following
		- Similar to missing data, you can drop the rows
		- If applicable, we can use ML models that are not affected by outliers like [Decision Trees](../Decision%20Trees.md).
		- There is also the ability to cap outliers to a certain value. This also allows us to keep all our data but it can affect the distribution of our data.
	- For [Classification](../Classification.md) problems we might be in a situation where there is class imbalance and we must know the steps we can take to remedy this problem.
		- **Under-sampling**: we ignore majority class examples so that we get a balanced dataset. This improves the run time and reduces storage because we remove data points. However, we can discard useful information and the sample chosen can be biased and we may not get a representative view of the population of the majority class.
		- **Over-sampling**: we randomly replicate the minority class. This method ensures we use all our data so we do not loose potentially informative data points. However, it increases the chance of overfitting as we replicate the minority class. 
		- **SMOTE (synthetic minority oversampling technique)**: analyze the feature space of the minority class and recreate similar examples using a nearest neighbors approach. Using this method we remove the problem of overfitting as well as there is no loss of informative data. On the other hand, it is not very effective for high dimensional data.
	- One of the last topics we should briefly mention is the need to have a proper train test split. For classification, we should have the same amount of the classes in the training as well as testing dataset. For time series problems, our training data should be earlier than our testing data.

4. Feature Selection:
	- Filter Methods: These methods are less accurate than others but tend to be fast to compute so they work well with large datasets. Some examples of these methods are analysis of variance (checks the means of two or more groups and determines if they are significantly different from each other). chi-square tests (used for categorical features)