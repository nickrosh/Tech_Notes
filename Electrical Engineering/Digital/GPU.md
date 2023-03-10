
A *Graphics Processing Unit* is optimized for data throughput, allowing to push as many possible instructions through it's cores at once. It does so by being able to parallelize certain highly parallel tasks like graphics processing and [Neural Networks](../../Machine%20Learning/Deep%20Learning/Neural%20Networks.md) training.

![](../../Attachments/Pasted%20image%2020230310114024.png)

A GPU will have many many more "cores" than a [CPU](CPU.md), however these cores are almost entirely just Arithmetic Logic Units. CPU's utilize advanced [Instruction Pipelining](Instruction%20Pipelining.md) and other methods to process complex instruction sequences. This makes the single threaded performance of a CPU core much more powerful than that of a GPU, however, GPU's are able to split up highly parallel tasks to an extent that enables speed ups in the hundreds of times that of a CPU. CPU's also try to perform parallel processing with [SIMD](SIMD.md) instructions, but this parallel processing is the core purpose of the GPU.

## Structure and Implementation

![](../../Attachments/Pasted%20image%2020230310114456.png)

The vast majority of the GPU is taken by the Graphics Compute Array (GCA). A thread, or CUDA core (for Nvidia) is a parallel processor that computes [Floating Point Numbers](Floating%20Point%20Numbers.md). Modern GPUs like the 3080 (mine) have over 8000 CUDA cores. The relationship between core count and compute power is not perfectly linear, but you can generally say the more cores there are, the more compute power the GPU has.

A thread block (or CUDA block) is a grouping of CUDA cores that can be executed together in series or parallel. The logical grouping of cores enables more efficient data mapping. Thread blocks share L1 [Cache](Memory%20&%20Cache.md) on a per-block basis. Current CUDA architectures cap to amount of threads per block at 1024. The next level of abstraction is the *Streaming Microprocessor* or SM. SM's are groupings of thread blocks on the same kernel. Grids can be used to perform larger computations in parallel. (e.g. those that require more than 1024 threads). The maximum thread blocks per SM is 32. Below is the SM from Nvidia Ampere (2020):
![](../../Attachments/Pasted%20image%2020230310120638.png)

This is all brought together with many SMs working in parallel. The GPU also contains higher level L2 cache that allows shared memory between SMs, and connections to VRAM . A full GPU die compared to a SM is shown below:

![](../../Attachments/Pasted%20image%2020230310121009.png)

A block stays in a single SM but SM does not manage its execution as a single unit. Instead, each block is further divided into *warps* that contain 32 threads each. Ampere has 4 warp-schedulers. Each scheduler handles a static set of warps and at each clock cycle, it picks one ready to execute its next instruction. The warp will then dispatch to a dedicated set of arithmetic instruction units to execute one single instruction. For example, warp 1 of block 4095, containing 32 threads, is scheduled by warp scheduler 0 to execute an INT32 operation on 16 INT32 units concurrently.

![](../../Attachments/Pasted%20image%2020230310121816.png)

## TPU