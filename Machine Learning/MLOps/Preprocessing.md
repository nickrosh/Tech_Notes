
Preprocessing data is probably the majority of the work of a Machine Learning Engineer. The quality and quantity of data is the single most important factor that determines model performance.

## Preparation

Preparation of data involves organizing and cleaning the data. The very first steps would be actually [joining](../../Databases/Relational%20Databases.md) the data together to create a single table to be used for the following preprocessing.

### Missing Values

First, we have to identify the rows with missing (Null) values and once we do, there are several approaches to dealing with them.

1. **Omit Samples (rows) with missing data**: Do this only if a small subset are missing the data
2. **Omit the Entire Feature (column)**: Do this if too many samples are missing the value
3. **Fill in (impute) missing values for Features**: For this, you need to be careful and depend on domain knowledge. For numerical features, this could mean imputing all Null values with the average value of the feature. For categorical features, this could mean imputing all Null values with the most frequent value in the feature. Another common strategy is forward/back filling. Meaning that the Null value will be replaced by the value before/after it.
4. **Some values may not always seem "missing"**: for example, zero values could be "missing" or the string "NA" could be an analog to Null. You must be careful to find all missing values.


### Outliers (Anomalies)

- Craft Assumptions about what is a "normal" expected value, e.g. saying everything should be within two standard deviations.
- Be careful not to remove important outliers (e.g. Fraud)
- Values may not be outliers when we apply a transformation (e.g. power law)
- Anomalies can be global (point), contextual (conditional) or collective (individual points are not anomalous and the collective group is an outlier).



### Cleaning

Cleaning our data involves applying constraints to make it easier for our models to draw signal from the data.

- Use domain expertise and EDA
- Apply constraints via filters
- Ensure data type consistency
- Remove data points with certain or null column values
- For [Computer Vision](../Deep%20Learning/Computer%20Vision.md): cropping, resizing, clipping, etc.
- For [NLP](../Deep%20Learning/NLP.md): changing to lower case, stemming, lemmatize, [Regex](../../Software%20Engineering/Regex.md), etc.


## Transforming

Transformation the data involves feature encoding, engineering, and other actions to increase model performance.

### [Scaling](../Scaling.md)

Scaling is required for models where the scale of the input affects the processes, but it is important to keep some things in mind:
- Learn constructs from the train split and apply to other splits
- You shouldn't blindly scale features, for example, scaling categorical features might eliminate their meaning

### Encoding

All data fed to a Machine Learning model must be numerical. Thus, we need to encode categorical data to be able to use it. There are a few common strategies:
- **Label Encoding**: Providing a unique sequential number to each class of a feature. This is compact and simple but runs into some issues as the ordinal numbering suggests a sequential relationship between the classes. Sometimes this is correct (Easy, Medium, Hard -> 1, 2, 3) and sometimes this is meaningless and hurts performance (Red, Green, Blue -> 1, 2, 3).
- **One-Hot Encoding**: This turns every class of a feature into its own binary vector (column). This does not give the ordinal relationship of labels, but has the drawback of being very sparse and thus very [Memory](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) inefficient.
- [Embeddings](../Deep%20Learning/Embeddings.md): Are learned low-dimensional representations of high-dimensional vectors, like natural language. This is a very powerful method of encoding huge categorical data.


### Feature Engineering

Feature Engineering is the act of combining features in unique ways to draw out more signal from the data. This requires a degree of subject matter expertise and is very specific to the actual problem and data being solved. Feature Engineering is a critical step to improve the training data to improve model performance. 

**Indicator Variables:** variables that isolate key info
- Thresholds: take thresholds of a feature (Ex age >= 21)
- Multiple Features: highlighting certain combos of features
- Special Events: highlighting special dates or events (Ex. Black Friday for Retail)
- Groups of Classes:  (Ex. flagging traffic from Google)

**Feature Representation:** changing the representation for clarity (Ex. Numerical to Categorical  Mapping, Grouping Classes)

**External Data:** bringing in outside data to augment the set, such as time & date, APIs, etc.

**Error Analysis:** 
- Start with larger errors and analyze what went away
- Segment by Classes and see the error rate by class
- You can use [Clustering](../Clustering.md) to help make classes
- Seek domain knowledge and experts


### Feature Selection

**Removing irrelevant and redundant features**

**Variance Thresholds:** Removing features whose values don't change much in observations. These features provide little value. For example, removing features with less than X variance.

| Pros | Cons |
| --- | --- |
| Based on solid intuition| If your problem needs dimensions reduction, VT is rarely sufficient |
| Easy and relatively safe way to reduce dimensions | Requires manual threshold setting |


**Correlation Thresholds:** Removing highly correlated features. These are redundant. For instance, deleting a feature that is 99% correlated with another feature, having both would bias the model.

| Pros | Cons |
| --- | --- |
| Based on solid intuition, similar features are redundant | User manually sets the threshold |
| Some algorithms are not robust to correlated features, so removing them can boost performance | May remove useful information |


**Genetic Algorithm:** Uses an algorithm to select features.

| Pros | Cons |
| --- | --- |
| Efficiently selects features from high-dimension data | Higher level of complexity, PCA is faster and simpler in most cases |
| Maintains original features, useful when there must be a transparent and interpretable solution | |


### Feature Extraction

**Creating new smaller datasets that captures most of the info**

**[Principal Component Analysis](../PCA.md) (PCA):** Algorithm to create new features out of the dataset that represent the majority of the dataset variance, while decreasing dimensionality.

| Pros | Cons |
| --- | --- |
| Versatile and works well in practice | Principal components are not interpretable |
| Fast and simple to implement | User must manually set threshold for cumulative explained variance |
| Several variations to deal with situation (Kernel PCA, Sparse PCA, etc.) | Must normalize data first |

**Linear Discriminant Analysis (LDA):** Finds maximum separability of the classes

| Pros | Cons |
| --- | --- |
| Supervised, thus "can" improve predictive performance | New features are not interpretable |
| Many variations like PCA | User must manually set threshold for cumulative explained variance |
| | Requires labelled data |
| | like PCA, must normalize data first |

**Autoencoders:** Type of [Neural Networks](../Deep%20Learning/Neural%20Networks.md) for feature extraction.

| Pros | Cons |
| --- | --- |
| Performs well for audio, text, image | Requires a lot of data as it is a Neural Net |
