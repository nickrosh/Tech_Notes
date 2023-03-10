
The time to [train](../Deep%20Learning/Optimizers.md) a [Neural Networks](../Deep%20Learning/Neural%20Networks.md) increases with the number of model parameters and dataset. Thus, for any large-scale model, we must scale our infrastructure to multiple [CPUs](../../Electrical%20Engineering/Digital/CPU.md) and especially multiple [GPUs](../../Electrical%20Engineering/Digital/GPU.md).

![](../../Attachments/Pasted%20image%2020230310013412.png)

## Data Parallelism

When using *Data Parallelism*, workers receive different slices of the larger dataset. It mainly accomplishes this with the following three tasks:
1. First, it creates and dispatches copies of the model, one copy per each [GPU](../../Electrical%20Engineering/Digital/GPU.md) (or other accelerator)
2. It shards the data and then distributes it to the corresponding devices.
3. It finally aggregates all results together in the [backpropagation](../Deep%20Learning/Neural%20Networks.md) step.

So we can see that the first task should happen once per training, but the last two tasks should occur in each iteration.

#### Fully Sharded Data Parallel

The newest innovation is Fully Sharded Data Parallel (FSDP). FSDP is a type of data-parallel training, but unlike traditional data-parallel, which maintains a per-[GPU](../../Electrical%20Engineering/Digital/GPU.md) copy of a model's parameters, gradients, and optimizer states, it shards all of these states across data-parallel workers and can optionally offload the sharded model parameters to [CPU's](../../Electrical%20Engineering/Digital/CPU.md).

Usually, model layers are wrapped with FSDP in a nested way, so that only layers in a single FSDP instance need to gather the full parameters to a single device during forward or backwards computation. In this way, peak GPU [Memory](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) could be saved and thus training can be scaled to use a larger model size or batch size.


## Model Parallelism

*Model Parallelism* shards a model (i.e., its layers or tensors) across multiple cores, unlike data parallelism, replicating the same model for all training cores. This is the strategy used by [AlexNet](../Deep%20Learning/Convolutional%20Neural%20Net.md).

![](../../Attachments/Pasted%20image%2020230310014007.png)


## Gradient Accumulation

Gradient accumulation is a technique where you can train on bigger batch sizes than your machine would normally be able to fit into memory. This is done by accumulating gradients over several batches, and only stepping the [Optimizer](../Deep%20Learning/Optimizers.md) and updating the gradients after a certain number of batches have been performed. Thus, you can essentially "emulate" a batch size of 64 if you do a batch size of 16 and accumulate it over 4 batches.

## Optimization

Distributed training strategies are great for when our data or models are too large for training, but what about when our model are too large to deploy for inference? The following model compression techniques are commonly used to make large models fir within existing infrastructure

#### Pruning

Remove weights (unstructured) or entire channels (structured) to reduce the size of the network. The objective is to preserve the model's performance while increasing its sparsity.

#### Quantization

Reduce the memory footprint of the weights by reducing their precision (e.g. 32 bit to 8 bit). We may lose some precision but it shouldn't affect performance too much. There are special [Floating Point](../../Electrical%20Engineering/Digital/Floating%20Point%20Numbers.md) formats for ML that have lower precision and higher exponent precision than mantissa.


#### Distillation

Training smaller networks to "mimic" larger networks by having it reproduce the larger network's layer's outputs. Below is an example of distilling the knowledge in a [Neural Network](../Deep%20Learning/Neural%20Networks.md).

![](../../Attachments/Pasted%20image%2020230310020618.png)