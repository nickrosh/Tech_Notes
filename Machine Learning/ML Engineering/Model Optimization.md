
[Distributed Training](Distributed%20Training.md) strategies are great for when our data or models are too large for training, but what about when our model are too large to deploy for inference? The following model compression techniques are commonly used to make large models fit within existing infrastructure for inference or for training with limited resources.

## Training Optimizations


#### Gradient Accumulation

Gradient accumulation is a technique where you can train on bigger batch sizes than your machine would normally be able to fit into memory. This is done by accumulating gradients over several batches, and only stepping the [Optimizer](../Deep%20Learning/Optimizers.md) and updating the gradients after a certain number of batches have been performed. Thus, you can essentially "emulate" a batch size of 64 if you do a batch size of 16 and accumulate it over 4 batches.

One crucial point about gradient accumulation is that it must be implemented with FP32 (not 16-bit) to keep the training precise. This is what accumulation libraries should be doing anyway. Accumulation will speed up your training, but it will come at the cost of a higher loss function, however the tradeoff is usually worth it.

#### Gradient Checkpointing

Even if you set the batch size to 1 and use gradient accumulation, you can still run out of memory when working with large models. In order to compute the gradients during the backward pass all activations from the forward pass are normally saved. This can create a big memory overhead. Alternatively, one could forget all activations during the forward pass and recompute them on demand during the backward pass. This would however add a significant computational overhead and slow down training.

Gradient Checkpointing strikes a compromise between the two approaches and saves strategically selected activations throughout the computational graph so only a fraction of the activations need to be re-computed for the gradients. A general rule of thumb is that gradient checkpointing slows down training by ~20%.

#### LoRA

*Low-Rank Adaptation* of [Large Language Models](../Deep%20Learning/Transformers.md) is a technique that allows you to fine tune a model with a fraction of the compute needed to fine tune a full model. Many applications in [NLP](../Deep%20Learning/NLP.md) rely on adapting one large scale model to downstream tasks. Such tasks are usually done with fine-tuning which updates all the parameters of the pre-trained model. LoRA adds a pair of rank-decomposition weight matrices to existing weights, and only trains those newly added weights. This has a couple of advantages:
- Previous pretrained weights are kept frozen so the model is not as prone to catastrophic forgetting.
- Rank-decompositions matrices have significantly fewer parameters than the original model, which means that trained LoRA weights are easily portable.
- LoRA matrices are generally added to the [Attention](../Deep%20Learning/Attention.md) layers of the original model. You can control the extent to which the model is adapted toward new data.

![](../../Attachments/Pasted%20image%2020230625162626.png)

We can further combine LoRA with Quantization to shrink large models down to the size that even a consumer [GPU](../../Electrical%20Engineering/Digital/GPU.md) can handle. This method is called QLoRA, which introduces 4-bit quantization, double quantization, and the exploitation of CUDA unified memory for paging.
- 4-bit Normal Float quantization: This is a method that improves upon quantile quantization. It ensures an equal number of values in each quantization bin. This avoids computational issues and errors for outlier values.
- Double Quantization: the authors of QLoRA defines it as follows "The process for quantizing the quantization constants for additional memory savings".
- Paging with unified memory: It relies on the NVIDIA Unified memory feature and automatically handles page-to-page transfers between the [CPU](../../Electrical%20Engineering/Digital/CPU.md) and [GPU](../../Electrical%20Engineering/Digital/GPU.md). It ensures error-free GPU processing, especially in situations where the GPU may run out of memory.

![](../../Attachments/Pasted%20image%2020230625162716.png)

## Pruning

Remove weights (unstructured) or entire channels (structured) to reduce the size of the network. The objective is to preserve the model's performance while increasing its sparsity. For example, many heads in [Multi-Head Attention](../Deep%20Learning/Attention.md) have been shown to provide little to model performance, and can thus be eliminated without a big hit.

## Quantization

Reduce the memory footprint of the weights by reducing their precision (e.g. 32 bit to 16 bit). We may lose some precision but it shouldn't affect performance too much. There are special [Floating Point](../../Electrical%20Engineering/Digital/Floating%20Point%20Numbers.md) formats for ML that have lower precision and higher exponent precision than mantissa. In training, we worry about overflow, which his when we exceed the max numerical range of a weight. This is why BF16 is much better than the same sized FP16. The exponent term of BF16 is just as large as FP32 so the numerical range is much better, at the cost of much lower precision. However, since our [Optimizers](../Deep%20Learning/Optimizers.md) are not exact anyway and have a component of randomness, perfect precision is not needed in training

Note that regardless of whether one uses BF16 or FP16, there is also a copy of weights which is always in FP32. This is what gets updated by the optimizer. So the 16-[bit](../../Electrical%20Engineering/Digital/Binary.md) formats are only used for the computation, the optimizer updates the FP32 weights will full precision and then casts them into the 16-bit format for the next iteration.

## Distillation

Training smaller networks to "mimic" larger networks by having it reproduce the larger network's layer's outputs. Below is an example of distilling the knowledge in a [Neural Network](../Deep%20Learning/Neural%20Networks.md).

![](../../Attachments/Pasted%20image%2020230310020618.png)




