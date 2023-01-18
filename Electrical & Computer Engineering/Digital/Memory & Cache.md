
In a high level Von Neumann architecture, CPU and a single block of memory are all that is required for a computer. However, in real life there is a tradeoff to be had. Technologies that provide greater storage quantities for the most part are slower. In order to have the benefit of fast computation and data fetching, while also having large storage quantities, we use a multi-memory strategy. There is a pyramid of different memory stores where the higher you go the closer you are to the CPU. The lower you go, the more storage space you have, but the slower the fetching speed. Thus we have a hierarchy where we utilize the super fast registers, the fast cache for our most commonly used data. And then the main memory, and the super slow disk storage when the memory is a miss.

![](../../Attachments/Pasted%20image%2020230115165130.png)


## Cache

In order to get the best granularity and ensure optimal usage of the memory pipeline, there are multiple levels of *cache*. This means that we can keep the most frequently used data right at the highest levels of cache, while keeping somewhat frequently used data in a lower cache level. This means we don't have to suffer the performance penalty of a *cache miss* every time we want to access data that is used somewhat frequently. Cache is almost always implemented as SRAM. Rarely, there are four levels of cache, but this is the most common setup:
- L1: Smallest size, but almost as fast as [Registers](Registers.md). For the most part, only dedicated to a single CPU core
- L2: Usually unified for multiple cores, but can also be banked for a single core.
- L3: Generally a unified cache between multiple CPU cores.

![](../../Attachments/Pasted%20image%2020230115170033.png)


#### SRAM

*Static Random Access Memory* is a volatile memory unit that utilizes 6 or 8 [transistors](CMOS.md) to store one bit. Additional circuitry like *sense amplifiers* and *decoders* are required to make a bank of SRAM function, but they allow very fast data retrieval. Due to the high count of transistors for a single bit of storage, SRAM is too expensive to be used for primary memory. When a bit is written to the SRAM cell, it sets the cell in a metastable position that will hold the value until the computer is powered off. You may also notice from the circuit below that an SRAM is really just cross coupled [NOT Gates](Logic%20Gates.md) with two additional switches for the write line.

![](../../Attachments/Pasted%20image%2020230115171754.png)

## Memory

Computer memory is the primary data store for programs. Memory is implemented as large banks of DRAM situated close to the [CPU](CPU.md). When there is a cache miss, the CPU will fetch the data from primary memory to be brought in. When the memory being fetched cannot be found in primary memory either, it is referred to as a *page miss*.

#### DRAM

*Dynamic Random Access Memory* is a volatile one transistor one capacitor technology that allows for greater amounts of storage. With DRAM, the bit is not stored as a metastable sequential logic state, like [Flip Flops](Flip%20Flops.md) or SRAM, instead it is the voltage level of the capacitor. If the capacitor is holding charge, then that is a '1', conversely if the capacitor has no charge that means a '0'. This requires significant additional circuitry to keep every charged capacitor from losing its charge and corrupting the data. Additionally, in an Integrated circuit, the capacitor is manufactured as a trench capacitor, which makes it unsuitable to be placed in a [CPU](CPU.md) unlike SRAM.

![](../../Attachments/Pasted%20image%2020230115172520.png)

DRAM must be refreshed continuously, as it will lost its capacitive charge in a fraction of a second. In fact, the reads and writes, and the subsequent charging are all explicit timings. There is a limit to trying to lower read and write time as signal integrity will be harder to maintain. A workaround to doubling memory throughput is to send data on both the rising and falling edge of the [Clock Signal](Clock%20Signal.md). This is called *Double Data Rate* or DDR.

![](../../Attachments/Pasted%20image%2020230117174127.png)