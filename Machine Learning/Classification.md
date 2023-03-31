

**Classification** is predicting discrete values or **classes** for each input

![](../Attachments/Pasted%20image%2020230225144653.png)

## Comparison of Algorithms

### [Logistic Regression](Logistic%20Regression.md)

| Pros | Cons |
| --- | --- |
| Outputs have probabilistic interpretation | Performs poorly with multiple or non-linear relations |
| Can be [regularized](Regularization.md) (L1, L2, Elastic-Net) to avoid overfitting | Not flexible for complex interactions |
| Can be updated easily with SGD | |


### [Classification Trees](Decision%20Trees.md)

| Pros | Cons |
| --- | --- |
| Can learn non-linear relations, robust to outliers | Unconstrained single trees often overfit. This is fixed by ensemble |
| Random Forest very powerful out-of-box | Any bias in individual trees is carried to the forest |
| Boosted Trees usually the best Model for Tabulated Data | |


### [Neural Networks](Deep%20Learning/Neural%20Networks.md)

| Pros | Cons |
| --- | --- |
| State-of-the-art for CV, NLP, text, etc. | Very data hungry |
| Reduces need for [feature engineering](ML%20Engineering/Preprocessing.md) | Computationally expensive |
| Easily updated with batch propagation | Much more difficulty with tuning |


### Support Vector Machines

| Pros | Cons |
| --- | --- |
| Can model non-linear boundaries, as there are many kernels | Memory Intensive, trickier to tune as you must select kernel |
| Robust against overfitting, especially in high dimension space | Doesn't scale well |


### [Naïve Bayes](Bayes%20Theorem.md)

| Pros | Cons |
| --- | --- |
| Extremely simple, both implementation and scale | Usually beaten by tuned algorithms |
| Essentially an updating probability table | |
