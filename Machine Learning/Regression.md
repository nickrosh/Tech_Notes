

**Regression** is predicting continuous/numeric variables. Mostly used for forecasting and optimizer systems


### Linear Regression

| Pros | Cons |
| --- | --- |
| Easily understandable | Performs poorly with non-linear relations |
| Can be regularized (L1, L2, Elastic-Net) to avoid overfitting | Not flexible for complex interactions |
| Can be updated easily with SGD | |


### Regression Trees

| Pros | Cons |
| --- | --- |
| Can learn non-linear relations | Unconstrained single trees often overfit. This is fixed by ensemble |
| Random Forest very powerful out-of-box | Any bias in individual trees is carried to the forest |
| Boosted Trees usually the best Model for Tabulated Data | |


### Deep Learning

| Pros | Cons |
| --- | --- |
| State-of-the-art for CV, NLP, text, etc. | Very data hungry |
| Reduces need for [Feature Engineering](Feature%20Engineering.md) | Computationally expensive |
| Easily updated with batch propagation | Much more difficulty with tuning |


### K Nearest Neighbors

| Pros | Cons |
| --- | --- |
| Simple concept | Memory Intensive |
| | Performs poorly for high-dimension data |
