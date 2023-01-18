
A *Central Processing Unit* is the electronic circuitry that executes [instructions](ISA.md) comprising a computer program. 


## Operation

The fundamental operation of most CPUs, regardless of the physical form they take, is to execute a sequence of stored instructions that is called a *program*. The instructions to be executed are kept in [Memory](Memory%20&%20Cache.md). This process of grabbing the instructions, decoding them, and executing them are collectively called the *instruction cycle*. After the execution of an instruction, the entire process repeats, with the next instruction cycle normally fetching the next-in-sequence instruction because of the incremented value in the [program counter](Registers.md).

![](../../Attachments/Pasted%20image%2020230117151033.png)

#### Fetch

Fetch involves retrieving an instruction. The instruction's location in memory is determined by the program counter (called "instruction counter in x86") which is a register that stores the address of the next instruction to be fetched. After an instruction is fetched, the program counter is incremented by the length of the instruction so that it will contain the address of the next instruction in the sequence. If the next instruction to be fetched is not in memory, it will take a long time to fetch it from disk. This problem is mostly resolved by modern CPUs which utilize [caches](Memory%20&%20Cache.md) and pipeline architectures

#### Decode

The instruction that the CPU fetches from memory determines what the CPU will do. Next, a [binary decoder](Encoders%20&%20Multiplexers.md) known as the *instruction decoder* converts the instruction into signals that control other parts of the CPU. The way in which the instruction is interpreted is defined by the computers [ISA](ISA.md). In RISC designs, the instruction decoder is implemented as a hardwired, unchangeable binary decoder circuit. In others, like x86, a microprogram can be used to convert more complex or rarely used instructions into sequential micro operations for the CPU.

#### Execute

After fetch and decode, the execute step is performed. Depending on the CPU architecture, this may consist of a single action or a sequence of actions. During each action, control signals electrically enable or disable parts of the CPU so they can perform all or part of the desired operation. The action is then completed, typically is response to a clock pulse. Very often the result is written to an internal [Register](Registers.md). x86 allows you to write the return value directly to a location in main [Memory](Memory%20&%20Cache.md). For example, if an addition operation is to be executed, registers containing the operands are activated, as are the parts of the ALU that perform the addition. When the clock pulse occurs, the operands flow from the source registers into the ALU, and the sum appears at its output.


## Structure and Implementation

The overall structure of all CPUs are pretty much the same. CPUs have not fundamentally changed their architecture since Von Neumann first described the architecture to simply consist of an internal control unit, an ALU for calculations, and memory. Input and output devices also interact with the processor to send and receive data. In this image, Black lines indicate data flow, whereas red lines indicate control flow:

![](../../Attachments/Pasted%20image%2020230117114957.png)

#### Control Unit

The *Control Unit* is the component of the CPU that directs the operation of the processor. A CU [decodes](Encoders%20&%20Multiplexers.md) instructions into timing and control signals that direct the operation of the other units (memory, ALU, I/O, etc.). Older CPU control units would sequentially process instructions, this is called a *multicycle control unit*. Then [Instruction Pipelining](Instruction%20Pipelining.md) was invented such that each part of the instruction pipeline could be computed at once. The control unit, ALU, and memory are all separate components and can work concurrently. Later advancement like *branch prediction* and *out-of-order execution* further optimized the pipeline and prevented stalled time without any instructions to execute.

![](../../Attachments/Pasted%20image%2020230117155447.png)


#### Arithmetic Logic Unit

The processors *Arithmetic Logic Unit* or ALU uses [combinational logic](Logic%20Gates.md) that performs arithmetic and [Bitwise Operations](../../Software%20Engineering/Bitwise%20Operations.md) on integer [Binary](Binary.md) values. There is also the *Floating Point Unit* (FPU) that performs calculations on [Floating Point Numbers](Floating%20Point%20Numbers.md).

![](../../Attachments/Pasted%20image%2020230117161548.png)

The inputs to an ALU are the data to be operated on, called *operands*, and a code indicating the operation to be performed. The ALU's output is the result of the performed operation. The Opcode is received from the Control Unit, and the operands come from [Registers](Registers.md). The ALU will be able to do addition, subtraction, and all of the bitwise operators. A simple 2-bit full adder with a carry bit is demonstrated below:

![](../../Attachments/Pasted%20image%2020230117162048.png)

Multiplication and division are more complex. Most techniques involve computing the set of partial products and then summing them together with binary adders. This process is similar to long multiplication, except that it uses base-2 [Binary](Binary.md). 

#### Clock

Most CPUs are synchronous circuits, which means they employ a [Clock Signal](Clock%20Signal.md) to pace their sequential operations. To ensure proper operation of the CPU, the clock period is longer than the maximum time needed for all signals to propagate through the CPU. In setting the clock rate well above the worst-case propagation delay, it is possible to design the entire CPU and the way it moves data around the "edges" of the rising and falling clock signal. This has the benefit of simplifying the CPU significantly, both from a design perspective, and a [component](CMOS.md)-count perspective. However, it also carries the disadvantage that the entire CPU must wait on its slowest elements, even though some portions of it are much faster. The frequency of the clock pulses determines the rate at which the CPU executes instructions and, consequently, the faster the clock, the more instruction the CPU will execute each second.


#### Memory Controller

*Memory Controllers* contain the logic necessary to read and write to [DRAM](Memory%20&%20Cache.md), and to "refresh" the DRAM. Without constant refreshes, DRAM will lose the data written to it as the capacitors leak their charge within a fraction of a second.

Reading and writing to DRAM is performed by selecting the row and column data addresses of the DRAM as inputs to the [multiplexer](Encoders%20&%20Multiplexers.md) on the memory controller, where the de-multiplexer on the DRAM uses the converted inputs to select the correct memory location and return the data, which is then passed back through a multiplexer to consolidate the data in order to reduce the required bus width for the operation.


#### Memory Management Unit

There is also a *Memory Management Unit* which has all memory references passed through itself, primarily performing the translation of [Virtual Memory](../../Systems%20Software/Virtual%20Memory.md) addresses to [Physical Memory](Memory%20&%20Cache.md) addresses. Most MMUs use an in-memory table of items called a *page table* containing one *page table entry (PTE)* per page, to map virtual page numbers to physical page numbers in main memory. An associative cache of PTEs is called a *translation lookaside buffer* and is used to avoid the necessity of accessing the main memory every time a virtual address is mapped.