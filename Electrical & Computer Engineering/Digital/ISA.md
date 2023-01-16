
An *Instruction Set Architecture* is the abstract model of a computer. A device that executes instructions described by that ISA, such as a [CPU](CPU.md) is called an *implementation*. In general, an ISA defines the supported *instructions*, data types, [Registers](Registers.md), the hardware support for managing [Memory](Memory%20&%20Cache.md), and other fundamental features. An ISA specifies the behavior of machine code running on implementations of that ISA in a fashion that does not depend on the characteristics of that implementation, providing binary compatibility between implementations. This allows changes in new [CPU](CPU.md) designs and micro-architectures, while still allowing backwards compatibility and and a standard *application binary interface* (ABI).


## Instructions

Machine language is built up from discrete statements or *instructions*. On the processing architecture, a given instruction may specify:
- Opcode (the instruction to be performed)
- Any explicit operands: [Registers](Registers.md), Literals/constant values, addressing modes used to access [Memory](Memory%20&%20Cache.md)

Here is the format of a generalized instruction: 
![](../../Attachments/Pasted%20image%2020230116012132.png)

More complex operations are built up by combining these simple instructions, which are executed sequentially, or as otherwise directed by control flow instructions. The operations common to all architectures are the following:
1. **Data handling and memory operations**
	- Set a register to a fixed constant value
	- Copy data from a memory location or a register to a memory location or a register. They are used to store the contents of a register, the contents of another memory location, or the result of a computation, or to retrieve stored data to perform a computation on it later. These are often called *Load-Store Operations*
	- Read and write data from hardware devices
2. **Arithmetic and logic operations**
	- Add, subtract, multiply, or divide the values of the two registers, placing the result in a register, possibly setting one or more condition codes n a status register.
	- Perform [Bitwise Operations](Bitwise%20Operations.md)
	- Compare two values in registers
	- Floating-point instructions for arithmetic on [Floating Point Numbers](Floating%20Point%20Numbers.md)
3. **Control Flow Operations**
	- Branch to another location in the program and execute instructions there
	- Conditionally branch to another location if a certain condition holds
	- Indirectly branch to another location
	- Call another block of [code](../../Software%20Engineering/C/Functions.md), while saving the location of the next instruction as a point to return to.

There are also more complex instructions that are more commonly found in CISC architectures, like moving large blocks of memory or [SIMD](SIMD.md) instructions.

## RISC vs CISC

There are two different philosophies to instruction set architectures. The traditional view is that there should be many specialized instructions for every situation (Complex Instruction Set Architecture), which is the philosophy of x86. What researchers realized is that only a handful of instructions are actually used by the [compiler](../Systems/Compiler.md) anyway so it would just be more efficient to use a smaller set of instructions at a faster rate. This is called *Reduced Instruction Set Architecture*.

In reality, it's not really an ongoing debate. There hasn't been a new CISC architecture in 35 years, x86 just has so much legacy development and software that it would not make sense to abandon it. The most popular RISC architectures today are ARM and the first open source ISA, RISC-V. So it should really be called RISC vs x86.

One of the main differences we can see between RISC and x86 is that x86 allows you to directly use a location in memory as an operand to a arithmetic or logic operation. In RISC, you would have to first load the memory into a register, make the calculation, and then store the value back to memory.