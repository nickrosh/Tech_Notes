
*Single Instruction Multiple Data* are specialized vector [instructions](ISA.md) that allow the [CPU](CPU.md) to process the same operation on multiple data points simultaneously. Vector instructions increase throughput by simplify the computation pipeline. Instead of a series of instructions saying "retrieve this pixel, now retrieve this pixel", a SIMD processor will have a single instruction that effectively says "retrieve N pixels". Another advantage is that the instruction operates on all loaded data in a single operation. In other words, if the SIMD system works by loading up eight data points at once, the `add` operation being applied to the data will happen to all eight values at the same time. 

![](../../Attachments/Pasted%20image%2020230117012312.png)

There are however, significant drawbacks:
- Not all algorithms can be vectorized easily.
- SIMD requires large [Register files](Registers.md) which increase power consumption and take up die area
- Currently, implementing an algorithm with SIMD usually requires human labor. Most [Compilers](../../Systems%20Software/Compiler.md) don't generate SIMD instructions whenever possible

In spite of these weaknesses, vector instructions are powerful enough to warrant continued use. The current top implementation in x86 is AVX-512, which allows for vectors of 512 bits to be computed at one time. Skipping both the fetch and decode portions of the [instruction pipeline](CPU.md) nets significant performance gains.

![](../../Attachments/Pasted%20image%2020230117012124.png)