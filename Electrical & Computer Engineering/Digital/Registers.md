*Registers* are quickly accessible locations available to the [CPU](CPU.md). These registers fulfill the most fundamental actions like the program counter, denoting which [instruction](Assembly.md) the program is at, and holding values for computation.

For example, for the x86 [ISA](ISA.md), there are many registers, 16 of which handle data and address, and the rest handling floating point and integer calculations. Here is some of the 32-bit versions of the x86 general purpose registers:

![](../../Attachments/Pasted%20image%2020230115163032.png)

For the most part, registers are implemented as banked Flip Flops but can also be made from [SRAM](Memory%20&%20Cache.md). The most common architecture is banked D Flip-Flops.

As the registers are embedded in the [CPU](CPU.md) cores itself, it usually takes a single cycle to access them. The size of the registers grow to the virtual memory size of the [Operating System](../Systems/Operating%20System.md). Thus we can see an evolution of register sizes from 8 bits (one byte) to 16 bits (one word) to 32 bits (double word or DWORD) to the modern day 64 bits (quad word or QWORD). We can see this evolution in the picture below looking at the changing x86 registers:

![](../../Attachments/Pasted%20image%2020230115180456.png)

This doesn't include the 64-bit registers, which replace the 32-bit register name 'E' with a 'R' (EAX -> RAX). While most registers have lost their special purpose in the modern [instruction set](ISA.md), two are reserved for special purposes - the stack pointer (SP) and the base pointer (BP).


## Flip Flops

Most registers are implemented with flip flops, which are volatile memory cells that allow for immediate computation in the ALU.


#### D Flip Flop

![](../../Attachments/Pasted%20image%2020230115175502.png)

This is a D Latch. It is controlled by the level input. In order to be properly used as a register, we need the storage to be edge triggered. To do this, we put two latches in series to make a *flip flop*.

![](../../Attachments/Pasted%20image%2020230115175650.png)

This is referred to as a D Flip Flop. It will respond to inputs during positive clock edges. This is critical to keep the data coherent in the greater computation. This system of putting two latches in series to make an edge-triggered flip flop is the same for the remaining flip flop types.

#### S-R Latch

![](../../Attachments/Pasted%20image%2020230115175919.png)


#### J-K Latch

![](../../Attachments/Pasted%20image%2020230115175936.png)


#### T Latch

![](../../Attachments/Pasted%20image%2020230115180012.png)

