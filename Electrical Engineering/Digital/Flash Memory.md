
Flash memory is an electronic non-volatile memory storage. This non-volatility makes it well suited for [secondary memory](Memory%20&%20Cache.md) unlike DRAM which requires a constant power source.

## Floating Gate MOSFET

![](../../Attachments/Pasted%20image%2020230123231130.png)

Flash memory is enabled by the invention of *Floating Gate MOSFETs*. These are based on the same technology as regular [CMOS](CMOS.md), except that it has two gates. One "Control Gate" which acts like the regular one and allows current to pass between drain and source. And then there  is a second gate called the "Floating Gate". It is sandwiched between non-conductive materials and thus when current passes between drain and source, charge is "trapped" in the floating gate. So even when there is no power to the memory, the memory cell holds the charge for weeks and beyond. This is compared to DRAM capacitive storage, which loses charge in a fraction of a second. When performing a READ on the cell, the floating gate is simply checked for whether it is charged or not.


## NAND and NOR

There are two popular configurations of flash memory, called NOR and NAND based on their configuration similarity to the [Logic Gates](Logic%20Gates.md).

![](../../Attachments/Pasted%20image%2020230123230438.png)

In NOR flash, each cell has one end connected directly to ground, and the other end connected directly to a [bit](Binary.md) line. It acts like a NOR gate: when one of the word lines is brought high, the corresponding storage transistor acts to pull the output bit line low. NOR flash is still the technology of choice for embedded applications requiring a discrete non-volatile memory device. The low read latencies characteristic of NOR devices allow for both direct code execution and data storage in a single memory product.


![](../../Attachments/Pasted%20image%2020230123230405.png)

NAND flash memory bits are connected in series. This means the bit line is pulled low only if all the word lines are pulled high. Compared to NOR flash, NAND has much fewer interconnections and ground lines and thus can have many more memory cells in a unit of area. The drawback however, is that NAND must read in an entire page at a time, typically between 4KB and 16KB. But erasing can only happen at the level of blocks, which consists of multiple pages. The benefit of much denser memory and still great performance means most flash memory in the market (Solid State Drives, USB sticks, SD cards, etc.) are made with NAND flash.