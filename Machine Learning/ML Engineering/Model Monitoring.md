
Once a model is in production, it is critical to continuously monitor it to ensure the model has not degraded in performance. 


## Model Drift

*Model Drift* refers to the phenomenon that model performance decays in production. There are multiple reasons that could cause drift.

#### Data Drift

*Data Drift* also known as feature drift or covariate drift, occurs when the distribution of the production data is different from the training data. The model is not equipped to deal with this drift in the feature space and so, it's predictions may not be reliable. The actual cause of drift can be attributed to natural changes in the real-world but also to systemic issues such as [missing data](Preprocessing.md), pipeline errors, schema changes, etc. It's important to inspect the drifted data and trace it back along it's pipeline to identify when and where the drift was introduced.

To make this concrete, consider the task of detecting breast cancer. You know that the risk of breast cancer is higher for women over 40, so you have a variable "age" as your input. You might have more women over the age of 40 in your training data than in your inference data, so the input distributions differ for your training and inference data. However, for an example with a given age, such as above 40, the probability that this example has breast cancer is constant. So $P(Y|X)$, the probability of having breast cancer given age over 40, is the same.

![](../../Attachments/Pasted%20image%2020230310021325.png)

As data starts to drift, we may not yet notice significant decay in our model's performance, especially if the model is able to interpolate well. However, this is a great opportunity to potentially retrain before the drift starts to impact performance. If you know in advance how the real-world input distribution will differ from your training input distribution, you can leverage techniques such as [importance weighting](Training%20Data.md) to train your model to work for the real-world data. This is usually very difficult in practice as we don't usually know how future data will change. 

#### Target Drift

Besides just the input data changing, as with data drift, we can also experience drift in our outcomes. This is known as *Target Drift*, or *Label Drift*. You can think of this as the case when the output distribution changes but, *for a given output*, the input distribution stays the same. This can be a shift in the distributions but also the removal or addition of new classes with categorical tasks. Though retraining can mitigate the performance decay caused target drift, it can often be avoided with proper inter-pipeline communication about new classes, schema changes, etc.

#### Concept Drift

Besides the input and output data drifting, we can have the actual relationship between them drift as well. This is called *Concept Drift*, also known as *posterior drift*. You can think of this as "same input, different output". This concept drift renders our model ineffective because the patterns it learned to map between the original inputs and outputs are no longer relevant. Concept drift can be something that occurs in various patterns:

![](../../Attachments/Pasted%20image%2020230310021829.png)

- Gradually over a period of time
- Abruptly as a result of an external event
- Periodically as a result of recurring events

Additionally, all of the types of drift discussed can happen simultaneously, which further complicates identifying the sources of drift.


## Detecting Data Distribution Shifts

Data distribution shifts are only a problem if they cause your model's performance to degrade. So the first idea might be to monitor your model's [ML Metrics](../ML%20Metrics.md) like Accuracy, F1, Recall, etc. in production to see whether they have changed. "Change" here usually means "decrease", but if my model's accuracy suddenly goes up or fluctuates significantly for no reason that I'm aware of, I'd want to investigate. The problem however, is that your access to ground truth labels in production is delayed at best. You have to wait for hand labels or even long running natural labels.

In industry, a simple method many companies use to detect whether the two distributions are the same is to compare their statistics like min, max, mean, median, etc. These are useful, but far from sufficient to detect data drift. We must rely on distribution comparison tests like *KL Divergence*, *JS Divergence*, and *KS tests* to determine the difference between training and input.

#### KL Divergence


#### JS Divergence


#### KS Test


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