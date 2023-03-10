
Digital Pictures are stored as huge matrices of numbers. Each such number corresponds to the brightness of a single pixel. In the RGB model, the color image is actually composed of three such matrices corresponding to three color channels: red, green, and blue. Each of these matrices store values between 0 and 255 (enough to store in one [byte](../../Electrical%20Engineering/Digital/Binary.md)). Now let's say we have an annotated dataset of one-megapixel photographs. This means that each input to the network has one million dimensions. Even an aggressive reduction to one thousand hidden dimensions would require a [fully connected layer](Neural%20Networks.md) characterized by $10^9$ parameters. That's a lot of [GPU's](../../Electrical%20Engineering/Digital/GPU.md).

This provides the motivation for making more efficient architectures for [Computer Vision](Computer%20Vision.md) tasks. The king of CV is *Convolutional Neural Networks*.

## Convolution

A *Convolution* is a mathematical operation on two functions $(f \text{ and } g)$ that produces a third function $(f*g)$ that expresses how the shape of one is modified by the other. In image processing, a *Kernel* filter is convolved onto an image to modify it:

![](../../Attachments/Pasted%20image%2020230228000804.png)

We utilize this in Deep Learning to create *Convolutional Layers* (also known as Conv Layers), where instead of a fully connected matrix to learn, the kernels (or filters) themselves are learned. Instead of Neurons, we have filters. The outputs of this layer are called *feature maps* as they are learned representations (features) of the input. 

#### Padding

![](../../Attachments/Pasted%20image%2020230228233446.png)

Whenever we use a kernel larger than 1x1, the output will be have smaller dimensions than the input. We lose pixels at the edge of the image. This can be counteracted by padding the edges of the input with 0, this is called *padding*.


#### Stride

![](../../Attachments/Pasted%20image%2020230301001104.png)
When computing a convolution, we start at the upper left. We can actually traverse more than one pixel at a time, this is called *Stride*. So stride=2 means the filter skips every other pixel. This is useful for computational efficiency or if we wish to downsample. This is especially done for larger kernels since they capture a large area of the underlying image.

#### Channels

Like mentioned before, real digital images are RGB, meaning there are three matrices for each picture, we refer to these as *input channels*. In order to process every input channel, we will need a kernel for every channel. Putting 3 input channels through 3 kernels will yield 1 output feature map. 

![](../../Attachments/Pasted%20image%2020230301001405.png)

In fact, we usually want to increase the number of channels as we go deeper in the model, downsampling to trade off spatial resolution for more channel depth. Intuitively, you could think of each channel as responding to a different set of features. Thus, if we want to double the number of output channels, we use two times as many kernels as input channels.

There are also 1x1 convolutional layers, which are a special case. At first glance they don't seem to make much sense, however they are commonly used in the designs of complex models. Because the minimum window is used, the 1x1 convolution loses the ability of larger convolutional layers to recognize patterns consisting of interactions among adjacent elements in the height and width dimensions. The only computation of the 1x1 convolution occurs on the channel dimension. It acts like a [fully connected](Neural%20Networks.md) layer applied at every single pixel location to get the dot product of that one pixel. Also note that 1x1 convolutions are usually followed up by [nonlinearities](Activation%20Functions.md) so they don't just get folded up into other convolutions.


## Pooling

![](../../Attachments/Pasted%20image%2020230301002908.png)

In vision tasks, the units of our final layer should be sensitive to the entire input. By gradually aggregating information, yielding coarser and coarser maps, we accomplish this goal of ultimately learning a global representation, while keeping all the advantages of convolutional layers at the intermediate layers of processing. We don't want the output to be sensitive to images being shifted or magnified, or any simple transformation. 

*Pooling Layers* serve the dual purpose of mitigating the sensitivity of convolutional layers to location, and of spatially downsampling representations. Like convolutions, pooling is a fixed shape window that is slid over all regions of input (according to its stride). However, this layer has no parameters to learn, it simply takes one value from all the values in the pool. The most common is max-pooling, which only takes the max value in the entire pool. Pooling also has no effect on the number of channels, unlike Conv Layers.


## Batch Normalization

Getting deep networks to [converge](Optimizers.md) in a reasonable amount of time is difficult. *batch normalization* is a popular and effective technique that consistently accelerates the convergence of deep networks. We often [preprocess](../MLOps/Preprocessing.md) our data before training, which often improves model performance. This normalization in the model itself can provide the same benefits. When one layer's activations are 100 times that of another layer, this might necessitate compensatory adjustments in the learning rates, and could hurt convergence. Adaptive solvers like Adam seek to solve this problem with second-order methods. Batch Norm prevents to problem from happening, with adaptive normalization.

![](../../Attachments/Pasted%20image%2020230301005600.png)

Batch normalization is applied to individual layers, or optionally , to all of them: in each training iteration, we first normalize the inputs (of batch normalization) by subtracting their mean and dividing by their standard deviation, where both are estimated based on the statistics of the current minibatch. Next, we apply a scale coefficient and an offset to recover the lost degrees of freedom. It is precisely due to this [normalization](../Scaling.md) based on *batch* statistics that *batch normalization* derives its name. This however, causes a dependency between multiple rounds of backpropagation. Batch Norm also provides some inherent [Regularization](../Regularization.md) so you shouldn't need to use it concurrently with dropout. You will want to use batch norm before [ReLU](Activation%20Functions.md).


#### Layer Normalization

*Layer Normalization* works just like batch norm, only that it is applied to one observation at a time. The normalization statistics are estimated from the summed inputs to the neurons within a hidden layer, so the normalization does not introduce any new dependencies between training cases. It works well for [Recurrent Neural Nets](Recurrent%20Neural%20Net.md) and [Transformers](Transformers.md).


## Architecture

![](../../Attachments/Pasted%20image%2020230301002422.png)

Now let's pull everything together to show how a CNN can perform advanced [Computer Vision](Computer%20Vision.md) tasks. The above image is the architecture for LeNet, the early architecture invented to learn MNIST. This is a very simple system with two convolution steps and some average pooling and sigmoid activations.

![](../../Attachments/Pasted%20image%2020230301002803.png)

The next jump came in 2012 with AlexNet. This used 5 conv nets, with max pooling to downsample. You can also see the three input channels being increased to 96 then 256 in order to extract more features. Then the tensor is flattened  and fed to fully connected layers, before being fed to a final SoftMax layer for image [Classification](../Classification.md).

![](../../Attachments/Pasted%20image%2020230301010719.png)

You can start to see a pattern emerging. The convolution and pooling sections of the networks extract features from the data. Those features are then fed into the regular neural net to do simple classification. There are many cutting edge CNNs like ResNet, which are up to 100 layers deep and utilize batch norm to achieve powerful results. 