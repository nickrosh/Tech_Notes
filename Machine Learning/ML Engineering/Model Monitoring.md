
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



## Model Fairness

Both for reasons of ethics and even legal requirements, it becomes critical to monitor model performance for bias and explainability.

#### Bias

Checking your model for bias is important. This can be any view of the dataset such as looking at race, sex, class, etc. The following are some metrics that can reveals bias issues in a model:

- **Class Imbalance**: This metric simply detects if an advantaged group is represented in the dataset at a substantially higher rate than another group, or vice versa
- **Total Variation Distance**: This measure of distance in label distribution is half of Hamming distance between the probability distribution of labels of the advantage and disadvantaged group.
- **Conditional Demographic Disparity in Predicted Labels (CDDPL)**: This metric examines whether the model predicted a bigger proportion of rejected outcomes for the disadvantaged class than the proportion of accepted outcomes for the same class.

#### Explainability

Deep Learning models are generally black boxes. It is difficult to discern why a [Neural Network](../Deep%20Learning/Neural%20Networks.md) made its decision based on its model weights and activations. SHAP Values (Shapley Additive Explanations) is a method to discern roughly how much each feature contributes to the model decisions. Here are the steps to calculate the SHAP values for a single feature *F*:

1. Create the set of all possible feature combinations
2. Calculate the average model prediction
3. For each combination, calculate the difference between the model's prediction *without* F and the average prediction
4. For each combination, calculate the difference between the model's prediction *with* F and the average prediction
5. For each combination, calculate how much F changed the model's prediction from the average (i.e. step 4 minus step 3), this is the *marginal contribution* of F.
6. Shapley Value = the average of all the values calculated in step 5 (i.e. the average of F's marginal contributions)

We can use Shapley values to see the influence of sensitive features like sex or race.