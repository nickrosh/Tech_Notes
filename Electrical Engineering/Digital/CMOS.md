
The *Complementary Metal Oxide Semiconductor Field Effect Transistor* is the foundational transistor technology of all modern digital computing. MOSFETs are Voltage controlled gate transistors, via the Field effect. A [voltage](../Electricity.md) is applied to the gate, which creates a diffusion layer that allows current to flow from source to drain.

## Digital Switch

In digital electronics, a transistor acts as a switch. This is the fundamental unit that can be used to build [Logic Gates](Logic%20Gates.md) and other complex functions. Digital logic derives from Boolean algebra, where any problem can be decomposed to True False calculations. Here is the simplest example of utilizing a NMOS transistor like a switch. When we apply Vin to the gate, we close the circuit and turn on the lamp:

![](../../Attachments/Pasted%20image%2020230115011905.png)


## NMOS & PMOS

![](../../Attachments/Pasted%20image%2020230115010140.png)

There are two variants, PMOS for positive channel, and NMOS for negative channel. A NMOS transistor can be roughly viewed as *normally open* meaning that voltage needs to be applied to the  gate to allow current to flow from source to drain. Conversely, PMOS is *normally closed* meaning that current can flow from drain to source until voltage is applied to the gate, which stops the current flow. 

NMOS is much more common, but PMOS is also widely, used in analog circuits, and in combination with NMOS. In digital circuits, we want to see clean 0 and 1 values, corresponding to voltage and ground. In real life, MOSFETs, like all transistors, are non-linear components. The initial response to gate voltage is vastly different than when

![](../../Attachments/Pasted%20image%2020230115012303.png)

This is called the *linear region*. In digital electronics, we really only care about when the voltage gets to whatever level we consider HIGH, thus the *saturation region* is not really relevant.  MOSFETS can also be used in analog applications like [Amplifiers](../Analog/Analog%20FET.md) where we would actually bias the transistor to maintain operation in the saturation region at all times.

## Integrated Circuit CMOS Manufacturing

CMOS is manufactured by doping the silicon to the exact n-type and p-type chemical properties required. Usually this is arsenic and boron. Once the silicon has been doped, photolithography is used to etch the gates and metal interconnects to form the actual circuits. Below you can see the side-cut view of actual CMOS transistors. When the voltage is applied to the gate, the region of silicon underneath the gate is called the *depletion zone* and will slowly fill up with charge carriers starting at the drain. It will grow to the source and then a conducting path will be formed between drain and source. This time to create the depletion zone is actually what is causing the non-linear behavior in the triode region of the transistor gate response. 

![](../../Attachments/Pasted%20image%2020230115005849.png)