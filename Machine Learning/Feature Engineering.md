
 Feature Engineering is the critical step to improve the training data to improve model performance. This involves both Feature Selection, and Feature Extraction, and creating new features from existing ones.

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
- You can use [Clustering](Clustering.md) to help make classes
- Seek domain knowledge and experts


---
## Feature Selection
**Removing irrelevant and redundant features**

#### Variance Thresholds
Removing features whose values don't change much in observations. These features provide little value.

| Pros | Cons |
| --- | --- |
| Based on solid intuition| If your problem needs dimensions reduction, VT is rarely sufficient |
| Easy and relatively safe way to reduce dimensions | Requires manual threshold setting |

#### Correlation Thresholds
Removing highly correlated features. These are redundant.

| Pros | Cons |
| --- | --- |
| Based on solid intuition, similar features are redundant | User manually sets the threshold |
| Some algorithms are not robust to correlated features, so removing them can boost performance | May remove useful information |

#### Genetic Algorithm
Uses an algorithm to select features.

| Pros | Cons |
| --- | --- |
| Efficiently selects features from high-dimension data | Higher level of complexity, PCA is faster and simpler in most cases |
| Maintains original features, useful when there must be a transparent and interpretable solution | |


---
## Feature Extraction
**Creating new smaller datasets that captures most of the info**

#### Principal Component Analysis (PCA)
Algorithm to create new features out of the dataset

| Pros | Cons |
| --- | --- |
| Versatile and works well in practice | Principal components are not interpretable |
| Fast and simple to implement | User must manually set threshold for cumulative explained variance |
| Several variations to deal with situation (Kernel PCA, Sparse PCA, etc.) | Must normalize data first |

#### Linear Discriminant Analysis (LDA)
Finds maximum separability of the classes

| Pros | Cons |
| --- | --- |
| Supervised, thus "can" improve predictive performance | New features are not interpretable |
| Many variations like PCA | User must manually set threshold for cumulative explained variance |
| | Requires labelled data |
| | like PCA, must normalize data first |

#### Autoencoders
Type of [Neural Networks](Neural%20Networks.md) for feature extraction.

| Pros | Cons |
| --- | --- |
| Performs well for audio, text, image | Requires a lot of data as it is a Neural Net |
