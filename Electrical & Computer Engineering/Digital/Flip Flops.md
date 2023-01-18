
Many [Registers](Registers.md) are implemented with flip flops, which are volatile [Memory](Memory%20&%20Cache.md) cells that allow for immediate computation in the [CPU](CPU.md). Flip flops are sequential digital logic, they depend on the state of the program and maintain a bit of data. 


#### D Flip Flop

![](../../Attachments/Pasted%20image%2020230115175502.png)

This is a D Latch. It is controlled by the level input. In order to be properly used as a register, we need the storage to be edge triggered on a [Clock Signal](Clock%20Signal.md). To do this, we put two latches in series to make a *flip flop*.

![](../../Attachments/Pasted%20image%2020230115175650.png)

This is referred to as a D Flip Flop. It will respond to inputs during positive clock edges. This is critical to keep the data coherent in the greater computation. This system of putting two latches in series to make an edge-triggered flip flop is the same for the remaining flip flop types.

#### S-R Latch

![](../../Attachments/Pasted%20image%2020230115175919.png)


#### J-K Latch

![](../../Attachments/Pasted%20image%2020230115175936.png)


#### T Latch

![](../../Attachments/Pasted%20image%2020230115180012.png)

