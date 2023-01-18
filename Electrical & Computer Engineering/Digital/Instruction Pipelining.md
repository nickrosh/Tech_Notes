
Traditionally, [CPU Control Units](CPU.md) would handle each instruction sequentially. This is called a *multicycle control unit*.

![](../../Attachments/Pasted%20image%2020230117154723.png)

More complex control units *pipeline instructions* meaning that each of the stages that an instruction goes though can be done independently, this greatly increases the throughput of the instruction pipeline.

![](../../Attachments/Pasted%20image%2020230117154852.png)

But what happens when the processor encounters a conditional branch statement (i.e. `if x=0 goto line 20`)? This will create a gap in the instruction pipeline, slowing down program execution. These issues are called *pipeline hazards*. The one described here is specifically a *pipeline stall*. In the image below B cannot execute until A is completed, this causes a gap, otherwise known as a NOP (no operation) or *bubble*.

![](../../Attachments/Pasted%20image%2020230117155953.png)

A solution employed by modern processors is *branch prediction*. The processor will actually predict which way a branch (if-then-else structure) will go before this is known definitively. Two-way branching is usually implemented with a conditional jump instruction. A conditional jump can either be "taken" and the jump to a different location in memory, or "not taken" and continue execution immediately after the conditional jump. It is not known path is taken until the instruction is executed.

Without branch prediction, the processor would have to wait until the conditional jump instruction has been executed before the next instruction can be fetched. The branch predictor attempts to avoid this waste of time by trying to guess whether the conditional jump is most likely to be taken or not taken. The predicted branch is then fetched and *speculatively executed*. There is another optimization called *out-of-order execution* which prematurely executes future instructions that have no current dependencies. All of these optimizations are intended to avoid stalls in the pipeline.

![](../../Attachments/Pasted%20image%2020230117164932.png)

But even with branch prediction, we can be wrong. If the processor predicts the wrong branch, we can end up wasting valuable time. This is called *branch misprediction*. The processor will have to retrace and fix the mistake, as seen in the picture below:

![](../../Attachments/Pasted%20image%2020230117155333.png)

