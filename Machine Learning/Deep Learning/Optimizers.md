
Optimizing algorithms are the key to Machine Learning. This is how models "learn" from training data. The central aim of optimizing algorithms is to manipulate the [Neural Networks](Neural%20Networks.md) parameters in order to minimize the [Loss Function](Loss%20Functions.md) of the model.

#### Gradient Descent

![](../../Attachments/Pasted%20image%2020230226195317.png)

Using simple calculus, we are able to compute the derivative of our current "location" in the [optimization landscape](Loss%20Functions.md) and see what direction is the steepest decline. This derivative is called the *gradient*. Thus simply going towards the negative of the gradient will be the descent towards the local minima. We move in the direction of the gradient with a magnitude called the *learning rate* ($\eta$), this is a hyperparameter. If the learning rate is too large, it will keep jumping over the place, if it is too small, it will take too long to converge. We have to set a relatively small learning rate and also set a *learning rate schedule* which decreases the learning rate as we approach a minima, this way we can be more accurate with finding the convergence point. 

For a input space of N inputs, our gradient is multivariate as well. It is a vector consisting of N partial derivatives:

$$\nabla f(x) = \Bigl[ \frac{\partial f(x)}{\partial x_1}, \frac{\partial f(x)}{\partial x_2},... , \frac{\partial f(x)}{\partial x_N} \Bigr]^T$$

In a [Neural Networks](Neural%20Networks.md), the gradient is computed using Backpropagation. Thus Gradient Descent is performed in the following steps:

1. Compute the [Loss Function](Loss%20Functions.md) on the dataset
2. Use Backpropagation to compute the gradient and all the weight and bias updates that must be performed on each layer, sum these changes.
3. Apply the update to the weights and biases according to the learning rate $\eta$.

#### Stochastic Gradient Descent

In classical gradient descent , each step of descent is computed on the average of the loss function of every single example in the dataset. The [Time Complexity](../../Data%20Structures%20&%20Algorithms/Time%20&%20Space%20Complexity.md) for each independent variable iteration is $O(n)$, therefore when the training dataset is larger, the cost of gradient descent grows.

*Stochastic Gradient Descent* (SGD) reduces the computational complexity at each iteration by randomly choosing one observation to compute the gradient and update x. This however, causes wild swings in the loss function as we are updating the weights and biases one observation at a time.

![](../../Attachments/Pasted%20image%2020230226230446.png)

So we see two extremes. Classical gradient descent which uses the full dataset to compute gradients and to update parameters, one pass at a time. And Stochastic Gradient Descent, which processes one training example at a time to make progress, which is also inefficient as you cannot make use of the vectorization available through GPUs and [SIMD](../../Electrical%20Engineering/Digital/SIMD.md) instructions. The solution is *Mini-Batch Stochastic Gradient Descent*, where we process mini-batches of randomly selected data at a time. This technique is so effective, this is what people actually refer to when they reference Stochastic Gradient Descent. Here Batch size is $\mathcal{B}$:
$$\mathbf{g}_t = \partial_{\mathbf{w}} \frac{1}{|\mathcal{B}_t|} \sum_{i \in \mathcal{B}_t} f(\mathbf{x}_{i}, \mathbf{w})$$
In practice, we pick a batch size that is large enough to offer good computational efficiency while still fitting into the [Memory](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) of a GPU.

#### Adam

Adam is the State-of-The-Art Optimizer Algorithm. It combines two improvements on top of SGD. Specifically:
- **Adaptive Gradient Algorithm (AdaGrad)** that maintains a per-parameter learning rate that improves performance on problems with sparse gradients (e.g. [NLP](NLP.md) and [Computer Vision](Computer%20Vision.md) problems)
- **Root Mean Square Propagation (RMSProp)** that also maintains per-parameter learning rates that are adapted based on the average of recent magnitudes of the gradients for the wright (e.g. how quickly it is changing). This means this algorithm does well on online and non-stationary problems (noisy)

The following are the hyperparameters for Adam:
-   **alpha**. Also referred to as the learning rate or step size. The proportion that weights are updated (e.g. 0.001). Larger values (e.g. 0.3) results in faster initial learning before the rate is updated. Smaller values (e.g. 1.0E-5) slow learning right down during training
-   **beta1**. The exponential decay rate for the first moment estimates (e.g. 0.9).
-   **beta2**. The exponential decay rate for the second-moment estimates (e.g. 0.999). This value should be set close to 1.0 on problems with a sparse gradient (e.g. [NLP](NLP.md) and [Computer Vision](Computer%20Vision.md) problems).
-   **epsilon**. Is a very small number to prevent any division by zero in the implementation (e.g. 10E-8).