
*Neural Networks* (or a Multilayer Perceptron) is a Machine Learning algorithm which loosely models the neurons of a biological brain. Typically neurons are aggregated into layers. Different layers perform different transformations on their inputs. Signals travel from the first layer (input) to the last layer (output), possibly after traversing the layers multiple times.

![](../../Attachments/Pasted%20image%2020230226174822.png)

Each connection, like the synapses in the brain, can transmit a signal to other neurons. An artificial neuron receives signals then processes them and can signal neurons connected to it. The "signal" at a connection is a real number, and the output of each neuron is computed by some non-linear [Activation Function](Activation%20Functions.md) of the sum of its inputs. There are however, key differences with biological neurons. Artificial neurons can have any number of outputs, whereas a biological neuron only has one output via its axon.

![](../../Attachments/Pasted%20image%2020230226174741.png)


Each connection between neurons has a *weight* that determine its relative strength of activation between one particular neuron and another. Each neuron also has a *bias* which simply denotes how active each neuron needs to be before it can actually be considered active to propagate to the next layer. Finally a [Activation Function](Activation%20Functions.md) like ReLU is applied on the weights and bias. Just taking the sum of weights and bias is called an *affine transformation*, and can only provide linear modeling. We can also prove that multiple layers of affine transformations are equal to a single layer equal to the product of the other multiple sums. Thus, without an activation function, multiple layers has no meaning, and the neural network cannot learn non-linearities.


## Forward Propagation

When we want to push data through the neural net to get an output, this is the *forward propagation* (or forward pass) step. The inputs are propagated to the next layer, and multiplied by the weights to that particular neuron, and then biased. The sum of all weights multiplied by inputs and bias is then fed into the neuron's activation function. Thus we can represent the function of a layers outputs as the following:
$$a^{(1)} = \sigma(Wa^{(0)}+b)$$
with "W" being the weights matrix, "a" being the input vector, "b" being the bias vector, and "$\sigma$" being the activation function. You can think of a neural network just as a function, except with thousands if not millions or billions of parameters in the form of weights and biases.


## Backpropagation

![](../../Attachments/Pasted%20image%2020230226233404.png)

*Backpropagation* is how Neural Networks compute the *gradient* of parameters. This is the algorithm for determining how a single training example would nudge the parameters by comparing its value against the [Loss Function](Loss%20Functions.md) and how every single weight and bias influenced that decision. [SGD](Optimizers.md) would take a batch of these examples and update the parameters according to the learning rate.

Let's say we do a forward pass on a training example, and receive a single cost value: 
$$C_0 = (a^{(L)} - y)^2$$
The neuron responsible for this cost is wrapped in an [Activation Function](Activation%20Functions.md):
$$a^{(L)} = \sigma(z^{(L)})$$
We can also break z down into the weights, activations, and bias:
$$z^{(L)} = w^{(L)}a^{(L-1)} + b^{(L)}$$ And of course, $a^{(L-1)}$ has its own function and weight, bias, etc. What we want to compute is how the cost function is affected by the parameters in the neural net, starting with the weight:
$$\frac{\partial C_0}{\partial w^{(L)}}$$
If we go back down the line, we can actually use the chain rule to compute this:
$$\frac{\partial C_0}{\partial w^{(L)}} = \frac{\partial z^{(L)}}{\partial w^{(L)}} \frac{\partial a^{(L)}}{\partial z^{(L)}} \frac{\partial C_0}{\partial a^{(L)}}$$
We can do the same thing for the bias, we can use simple derivatives to determine how much each parameter is determining the change in the cost function. Each of these backprop steps is just one component of the gradient vector. Backpropagation provides a clear way of updating and understanding each weight and bias in a very deep network. This algorithm is also why [Activation Functions](Activation%20Functions.md) must be easily differentiable, if we cannot compute the gradient we will struggle with training.

## Regularization

Much like common methods of [Regularization](../Regularization.md), neural networks benefit from regularization methods that decrease [Variance](../Bias%20vs.%20Variance.md) and prevent overfitting. A key concept in deep learning is the concept of *capacity* or how much data a model can learn. This is an informal concept that generally refers to how complicated a pattern or relationship a model can express. You can express a 6 layer 1000 node network to be able to express more complexity than a 2 layer 32 node network. Both depth and width play into this consideration, and can be influenced through experimentation.

#### Weight Decay

Rather than directly manipulating the number of parameters, *weight decay* operates by restricting the values that the parameters can take. More commonly called L2 regularization outside of Deep Learning circles, this is analogous to Ridge Regression. Weight decay might be the most widely used technique for regularizing parametric machine learning models. To penalize the size of the weight vector, we add a penalty term of the squared weight multiplied by the hyperparameter $\lambda$ to the SGD updates.

#### Early Stopping

![](../../Attachments/Pasted%20image%2020230227001802.png)

Early stopping is a very simple and intuitive method of regularizing neural networks. Here, rather than constraining the values of the weights, one constrains the number of epochs of training. Monitor validation error throughout training (typically by checking once after each opoch) and cut off training when the validation has not decreased by more than some small amount $\epsilon$ for some number of epochs. This is sometimes called patience criteria.

#### Dropout

![](../../Attachments/Pasted%20image%2020230227004751.png)

One approach to reducing overfitting is to fit all possible different neural networks on the same dataset and the average the predictions from each model, as in [Ensemble Learning](../Ensemble%20Learning.md). This is not feasible in practice when the model is large.

*Dropout* is a regularization method that approximates training a large number of neural networks with different architectures in parallel. During training, some number of layer outputs are randomly ignored or "dropped out". This has the effect of making the layer look-like and be treated-like a layer with a different number of nodes and connectivity to the prior layer. In effect, each update to a layer during training is performed with a different "view" of the configured later.

Dropout has the effect of making the training process noisy, forcing nodes within a layer to probabilistically take on more or less responsibility for the inputs. This suggests that dropout breaks-up situations where network layers co-adapt to correct mistakes from prior layers, in turn making the model more robust. Because the outputs of a layer under dropout are randomly subsampled, it has the effect of reducing the capacity or thinning the network during training. As such, a wider network, e.g. more nodes, may be required when using dropout.

Dropout is implemented per-layer, and can be used with all network types, such as [Convolutional Neural Nets](Convolutional%20Neural%20Net.md) and [Recurrent Neural Nets](Recurrent%20Neural%20Net.md). It is common for larger networks to more easily overfit the data. When using dropout, it is possible to use a larger network with less risk of overfitting. A good rule of thumb is to divide the number of nodes in the layer before dropout by the proposed dropout rate and use that as the number of nodes in the new network that uses dropout. For example, a network with 100 nodes and a proposed dropout rate of 0.5 will require 200 (100/0.5) when using dropout. You can grid search the dropout rate between 1.0 and 0.1, but 0.5 and 0.8 are usual rates used.