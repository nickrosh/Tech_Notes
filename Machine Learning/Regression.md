

**Regression** is predicting continuous/numeric variables. Mostly used for forecasting and optimizer systems

![](../Attachments/Pasted%20image%2020230225144752.png)

## Comparison of Algorithms

### [Linear Regression](Linear%20Regression.md)

| Pros | Cons |
| --- | --- |
| Easily understandable | Performs poorly with non-linear relations |
| Can be regularized (L1, L2, Elastic-Net) to avoid overfitting | Not flexible for complex interactions |
| Can be updated easily with SGD | |


### [Regression Trees](Decision%20Trees.md)

| Pros | Cons |
| --- | --- |
| Can learn non-linear relations | Unconstrained single trees often overfit. This is fixed by ensemble |
| Random Forest very powerful out-of-box | Any bias in individual trees is carried to the forest |
| Boosted Trees usually the best Model for Tabulated Data | |


### [Neural Networks](Deep%20Learning/Neural%20Networks.md)

| Pros | Cons |
| --- | --- |
| State-of-the-art for CV, NLP, text, etc. | Very data hungry |
| Reduces need for [feature engineering](ML%20Engineering/Preprocessing.md) | Computationally expensive |
| Easily updated with batch propagation | Much more difficulty with tuning |


### K Nearest Neighbors

| Pros | Cons |
| --- | --- |
| Simple concept | [Memory](../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) Intensive |
| | Performs poorly for high-dimension data |
