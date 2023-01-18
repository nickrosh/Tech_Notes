
[CMOS](CMOS.md) transistors can be used to build *logic gates* which perform fundamental Boolean operations. Entire [computers](CPU.md) can be build with just logic gates. Today, hardware is written at the RTL level, and the logic synthesizer creates a combination of switches and gates and other structures to satisfy the RTL requirement.

## NOT

![](../../Attachments/Pasted%20image%2020230115014347.png)

The inverter is the simplest gate. It simply inverts the input value. It is constructed from a [NMOS and PMOS](CMOS.md) in complement.


## NAND

![](../../Attachments/Pasted%20image%2020230115014404.png)

The *NOT-AND* gate returns 1 on every type of input except when all inputs together are 1, in other words, the opposite of AND. The NAND gate is a universal gate, meaning you can use it to construct all other gates and digital logical functions. Below you will see some of the way to build other gates using only NAND gates:

![](../../Attachments/Pasted%20image%2020230115014848.png)


## NOR

![](../../Attachments/Pasted%20image%2020230115014418.png)

The NOR gate is the negative of OR, it will only go positive when all inputs are zero. NOR is also a universal gate like NAND. In fact, the entire Apollo Guidance computer was made solely with 3-input NOR gates.

## AND

![](../../Attachments/Pasted%20image%2020230115015213.png)

AND gates are widely used in [Registers](Registers.md) and other calculations. This can be constructed in CMOS by simply adding a NOT gate to the output of a NAND gate.


## OR

![](../../Attachments/Pasted%20image%2020230115015256.png)

The OR gate will return 1 when any of the inputs are 1. This is an expensive gate like AND, but has its uses. This can be constructed by adding a NOT gate to the output of a NOR gate.


## XOR

![](../../Attachments/Pasted%20image%2020230115015702.png)

The *Exclusive-OR* gate is an interesting one. It will only return 1 when a single one of its inputs is 1
