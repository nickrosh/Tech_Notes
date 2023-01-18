
*Registers* are quickly accessible locations available to the [CPU](CPU.md). These registers fulfill the most fundamental actions like the program counter, denoting which [instruction](Assembly.md) the program is at, and holding values for computation.

For example, for the x86 [ISA](ISA.md), there are many registers, 16 of which handle data and address, and the rest handling floating point and integer calculations. Here is some of the 32-bit versions of the x86 general purpose registers:

![](../../Attachments/Pasted%20image%2020230115163032.png)

For the most part, registers are implemented as banked Flip Flops but can also be made from [SRAM](Memory%20&%20Cache.md). The most common architecture is banked D Flip-Flops.

As the registers are embedded in the [CPU](CPU.md) cores itself, it usually takes a single cycle to access them. The size of the registers grow to the virtual memory size of the [Operating System](../../Systems%20Software/Operating%20System.md). Thus we can see an evolution of register sizes from 8 bits (one byte) to 16 bits (one word) to 32 bits (double word or DWORD) to the modern day 64 bits (quad word or QWORD). We can see this evolution in the picture below looking at the changing x86 registers:

![](../../Attachments/Pasted%20image%2020230115180456.png)

This doesn't include the 64-bit registers, which replace the 32-bit register name 'E' with a 'R' (EAX -> RAX). While most registers have lost their special purpose in the modern [instruction set](ISA.md), two are reserved for special purposes - the stack pointer (SP) and the base pointer (BP).

## Register Files

A *register file* is an array of registers. Register banking is a method of using a single name to access multiple different physical registers depending on the operating mode. The [ISA](ISA.md) of a [CPU](CPU.md) will almost  always define a set of registers which are used to stage data between memory and the functional units on the chip. Modern processors usually implement register files with fast [SRAM](Memory%20&%20Cache.md) with multiple ports for both dedicated read and write. This is more specialized than regular SRAM that uses the same port for reading and writing.

In simpler CPUs, the *architectural registers* as defined by the ISA will correspond one-for-one to the entries in a physical register file within the CPU. More complicated CPUs use *register renaming*, so that the mapping of which physical entry stores a particular architectural register changes dramatically during execution. This abstraction allows us to bank multiple physical registers together to enable very large [Vector Instructions](SIMD.md).


