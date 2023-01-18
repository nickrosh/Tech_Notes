
Assembly is a human-readable version of computer [instructions](ISA.md). There is generally a 1:1 correspondence between an assembly statement and a machine instruction, but constants, assembler directives, symbolic labels (e.g. [memory locations](Memory%20&%20Cache.md), [Registers](Registers.md), and Macros) are generally also supported. Assembly is the lowest-level programming that can be done other than directly editing binary. Assembly code is converted into executable machine code by a utility program referred to as an *assembler*. These days, we won't actually write assembly, we'll just analyze the binary output of a [Compiler](../../Systems%20Software/Compiler.md) using Assembly to more easily read the binary.

Assembly uses a mnemonic to represent each Opcode, each directive, typically also each architectural [Register](Registers.md), flag, etc. Many operations require one or more operands in order to form a complete instruction.

![](../../Attachments/Pasted%20image%2020230116221817.png)


## ARM

ARM is a [RISC](ISA.md) load-store architecture. Looking over a simple conversion of a [C function](../../Software%20Engineering/C/Functions.md) to assembly, we see how the machine actually interprets the code. Look at the `square` function, first the [Registers](Registers.md) `r7` is pushed onto the stack. Then we see loading and storing of `r3` and the multiplication with itself with the `mul` opcode.

![](../../Attachments/Pasted%20image%2020230116224631.png)


## x86

x86 Assembly comes in two styles. AT&T which is used by GNU, and traditional Intel syntax. Here we see an Intel syntax x86 version of the same C code as above. Look at how with the identical C code, the instructions are very different. In the RISC load-store architecture, all the values must be loaded into registers and computed in discrete actions. In the x86 version below, we see the `square` function is much shorter.

![](../../Attachments/Pasted%20image%2020230116224459.png)