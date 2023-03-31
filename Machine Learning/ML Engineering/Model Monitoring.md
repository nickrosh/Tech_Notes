
Once a model is in production, it is critical to continuously monitor it to ensure the model has not degraded in performance. 


## Model Drift

*Model Drift* refers to the phenomenon that model performance decays in production. There are multiple reasons that could cause drift.

#### Data Drift

*Data Drift* also known as feature drift or covariate drift, occurs when the distribution of the production data is different from the training data. The model is not equipped to deal with this drift in the feature space and so, it's predictions may not be reliable. The actual cause of drift can be attributed to natural changes in the real-world but also to systemic issues such as [missing data](Preprocessing.md), pipeline errors, schema changes, etc. It's important to inspect the drifted data and trace it back along it's pipeline to identify when and where the drift was introduced.

![](../../Attachments/Pasted%20image%2020230310021325.png)

As data starts to drift, we may not yet notice significant decay in our model's performance, especially if the model is able to interpolate well. However, this is a great opportunity to potentially retrain before the drift starts to impact performance.

#### Target Drift

Besides just the input data changing, as with data drift, we can also experience drift in our outcomes. This can be a shift in the distributions but also the removal or addition of new classes with categorical tasks. Though retraining can mitigate the performance decay caused target drift, it can often be avoided with proper inter-pipeline communication about new classes, schema changes, etc.

#### Concept Drift

Besides the input and output data drifting, we can have the actual relationship between them drift as well. This concept drift renders our model ineffective because the patterns it learned to map between the original inputs and outputs are no longer relevant. Concept drift can be something that occurs in various patterns:

![](../../Attachments/Pasted%20image%2020230310021829.png)

- Gradually over a period of time
- Abruptly as a result of an external event
- Periodically as a result of recurring events

Additionally, all of the types of drift discussed can happen simultaneously, which further complicates identifying the sources of drift.
