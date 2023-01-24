
Semiconductor manufacturing allows us to pack *billions* of components in a chip that can fit in my palm. *Integrated Circuits* are the greatest invention of the last hundred years, and are responsible for the digital age we live in. 

![](../Attachments/Pasted%20image%2020230122011719.png)

Semiconductors can be made into p-type (positive charge) or n-type (negative charge) which is a property that allows extremely small circuits to be "printed" with photolithography. A photomask is made of the circuit layout and light is then shot at the chip through the mask, hardening the parts that make the gates and structures of the [CMOS](Digital/CMOS.md) transistors. This allows the manufacture of devices that are mere nanometers in width. Integrated circuits are built in a huge series of steps that "builds" the chip from the bottom up, first doping the silicon base to the correct chemical properties. Then photolithography to add the gates, then more layers for all of the metal interconnect layers, which actually wires everything together.

![](../Attachments/Pasted%20image%2020230122013500.png)

In the above circuit, you can see the actual physical layout of how a CMOS inverter will be made in an integrated circuit. The red areas are polysilicon, and the blue areas are metal interconnects. When the polysilicon passes over a green area for NMOS (or orange area for PMOS), this is what a transistor is. We then just connect the source and drain of the NMOS and PMOS and to power and ground to complete the circuit.


## Register Transfer Level

In reality, digital microelectronics are not actually designed with physical layout of gates and interconnects. There are billions of transistors in modern chips. Thus we go to a level of abstraction above the [Logic Gates](Digital/Logic%20Gates.md) level called *Register Transfer Level* or RTL. This abstraction models a digital circuit in terms of the flow of digital [Signals](Signals.md) between hardware [Registers](Digital/Registers.md) and the [logical operators](Digital/Logic%20Gates.md) performed on those signals. This is expressed in *Hardware Description Language* (HDL) which is like a programming language, except it is describing the full state of a circuit. In HDL there is no control flow, the entire code is executed as once as it is not a program, it is a description of a circuit. We merely describe the dataflow with RTL, and logic synthesizers will automatically select components and place them in an optimal position to satisfy the description. The actual layout of the logic gates and components are taken from libraries made by the manufacturer like TSMC. 

![](../Attachments/Pasted%20image%2020230122023441.png)
This simple circuit can be described in HDL (in this case VHDL) below:

```vhdl
D <= not Q;

process(clk)
begin
	if rising_edge(clk) then
		Q <= D;
	end if;
end process;
```


## Mixed-Signal VLSI

Digital Circuits can all be described in RTL, but what about analog integrated circuits? There are more considerations that have to be put in, including the physical size of the transistors and adding in integrated passive elements crucial to analog circuits like [Resistors](Analog/Resistors.md) and [Capacitors](Analog/Capacitors.md).

![](../Attachments/Pasted%20image%2020230122015936.png)

This is the physical layout of an [Op-Amp](Analog/Op-Amps.md). Circuits like this are needed to then interface with the digital electronics in the chip. Analog IC design adds a whole additional layer of complexity as the components them have much greater margins of error than their discrete counterparts. So, values are generally chosen as ratios of other components, rather than purely by their absolute value. In mixed-signal VLSI, the devices are almost always [Analog MOSFETs](Analog/Analog%20FET.md) as there is an additional cost to mixing CMOS and [BJT](Analog/BJT.md) on the same chip.