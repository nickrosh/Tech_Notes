

**Classification** is predicting discrete values or **classes** for each input


## Comparison of Algorithms

### Logistic Regression

| Pros | Cons |
| --- | --- |
| Outputs have probabilistic interpretation | Performs poorly with multiple or non-linear relations |
| Can be [regularized](Regularization.md) (L1, L2, Elastic-Net) to avoid overfitting | Not flexible for complex interactions |
| Can be updated easily with SGD | |


### Classification Trees

| Pros | Cons |
| --- | --- |
| Can learn non-linear relations, robust to outliers | Unconstrained single trees often overfit. This is fixed by ensemble |
| Random Forest very powerful out-of-box | Any bias in individual trees is carried to the forest |
| Boosted Trees usually the best Model for Tabulated Data | |


### Deep Learning

| Pros | Cons |
| --- | --- |
| State-of-the-art for CV, NLP, text, etc. | Very data hungry |
| Reduces need for [Feature Engineering](Feature%20Engineering.md) | Computationally expensive |
| Easily updated with batch propagation | Much more difficulty with tuning |


### Support Vector Machines

| Pros | Cons |
| --- | --- |
| Can model non-linear boundaries, as there are many kernels | Memory Intensive, trickier to tune as you must select kernel |
| Robust against overfitting, especially in high dimension space | Doesn't scale well |


### Naïve Bayes

| Pros | Cons |
| --- | --- |
| Extremely simple, both implementation and scale | Usually beaten by tuned algorithms |
| Essentially an updating probability table | |
