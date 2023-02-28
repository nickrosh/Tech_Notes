

## Hidden Units

If hidden units only had a linear response, then there is no point to having multiple layers in a [Neural Network](Neural%20Networks.md). We can prove that two linear layers are equal to a single layer with multiplied weights. Thus, in order to be able to model non-linearities in the data, we must include non-linear activation functions after the linear combination in a node. In fact, without non-linear activation functions, a Neural Network is pretty much just a [Linear Regression](../Linear%20Regression.md).

![](../../Attachments/Pasted%20image%2020230226145106.png)

One primary concern when selecting activation functions is trying to avoid functions that *saturate* the domain. Saturation means that the [Loss Functions](Loss%20Functions.md) topology becomes very flat and thus makes it much harder for the gradient to converge towards a minima.  


#### ReLU

![](../../Attachments/Pasted%20image%2020230226150014.png)

*Rectified Linear Units* are a simple a powerful activation function. It is currently the most popular function for hidden units. As it is linear, it is very easy to optimize and you don't have to worry about higher-level derivatives.
$$\sigma(z) = \max(0, z)$$
 When initializing, it can be a good idea to set to bias very small, this way the ReLU units won't start at zero. The fact that all input <=0 go to zero weight causes some problems. This also means ReLU can't learn via [gradient methods](Optimizers.md) on examples for which the activation is zero, this is called the *Dying ReLU* problem. There are some variations like *Leaky ReLU* that try to get around this by having the left side be very slightly negative, so as not to crush all the weights to zero.

#### Sigmoid

![](../../Attachments/Pasted%20image%2020230226150023.png)

Prior to ReLU, sigmoid was the most common activation function (0 to 1). The primary weakness of sigmoid is that it saturates across most of its domain, and is only sensitive to input around zero.
$$\sigma(z) = \frac{1}{1+e^{-z}}$$
The function is differentiable and provides a smooth gradient, i.e. preventing jumps in output values. This is represented by an S-shape of the sigmoid activation function. This is also the function used in [Logistic Regression](../Logistic%20Regression.md). Sigmoid is also commonly used for models where we have to predict the probability as an output. Thus Sigmoid can also be used as an output.

#### Hyperbolic Tangent

![](../../Attachments/Pasted%20image%2020230226150029.png)

Tanh (or hyperbolic tangent) is very similar to the sigmoid/logistic activation function, except that it has a range from (-1, 1). In Tanh, this larger the input (more positive) the closer the output value will be to 1, whereas the smaller the input, the closer the output will be to -1.
$$\sigma(z) = \frac{e^z-e^{-z}}{e^z+e^{-z}}$$
Tanh faces the problem of vanishing gradients similar to sigmoid. Plus the gradient of the Tanh function is much steeper compared to the sigmoid function. However, Tanh is zero-centered, and the gradients are not restricted to move is a certain direction. Therefore, in practice, tanh nonlinearity is always preferred to sigmoid nonlinearity.


## Output Units

The choice of [Cost Function](Loss%20Functions.md) is tightly coupled with the choice of output units. Most of the time, we use Cross-Entropy. The choice of how to represent the output then determines the form of the cross-entropy function.

![](../../Attachments/Pasted%20image%2020230226150123.png)


#### Linear

![](../../Attachments/Pasted%20image%2020230226150110.png)

This is also known as the identity function or the transfer function. A linear activation simply means no additional transformation is done after the linear combination of weights. This is done in order to get a scalar output. In other words, putting a linear activation at the output layer allows a Neural Network to model a [Regression](../Regression.md) problem.

#### SoftMax

![](../../Attachments/Pasted%20image%2020230226150055.png)

Say you have three classes that your model is predicting, meaning there would be three neurons in the output layer. Now suppose the output from the neurons is (1.8, 0.9, 0.68). Applying the SoftMax over these values to give a probabilistic view would result in the following output (0.58, 0.23, 0.19). The function returns 1 for the largest probability index while it returns 0 for the other two array indexes, so the output would be (1, 0, 0). SoftMax is essentially a combination of sigmoid units, each giving the probability of one class.

SoftMax takes a vector of values, and converts them into a vector of real values that sum to 1. Thus SoftMax is used as the output layer in multi-class [Classification](../Classification.md) problems, by selecting the class with the highest probability. Note, if you only want binary classification, just use one sigmoid as the output layer.