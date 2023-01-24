
An *Electric Circuit* is a closed loop network of electric components ([Resistors](Analog/Resistors.md), [Capacitors](Analog/Capacitors.md), [Inductors](Analog/Inductors.md), etc.). A circuit that contains active electronic components like transistors ([BJT](Analog/BJT.md), [CMOS](Digital/CMOS.md)) is known as an *Electronic Circuit*.

Circuits that contain non-linear components must be modelled with more advanced techniques. Chiefly among them is modelling the behavior of transistors, either through complex calculations, or through piecemeal calculations, treating the different zones (saturation vs triode) as different analyses.

## Kirchoff's Laws

Electric circuits at low frequencies obey *Kirchoff's Laws* which dictate how current and voltage acts in a system. Electric circuits with only linear components can be modeled with mesh and node analysis, stemming from Kirchoff's laws. These are called *Linear Circuits*. We can also model real world voltage and current sources with *Thevenin's* and *Norton's* theorems, respectively.

#### Kirchoff's Current Law (KCL)

For any node (junction) in a circuit, the sum of currents flowing into that node is equal to the sum of currents flowing out of that node.

![](../Attachments/Pasted%20image%2020230121191534.png)

In the above image: $i_1 + i_2 - i_3 - i_4 = 0$ or more generally at every node:
$$\sum_{k=1}^{n}{I_k} = 0$$


#### Kirchoff's Voltage Law (KVL)

The directed sum of the voltages (electric potential differences) around any closed loop is zero

![](../Attachments/Pasted%20image%2020230121192026.png)

In the above image, the voltage drop at the 3 [resistors](Analog/Resistors.md) must equal the voltage source. This is defined at each loop generally as:
$$\sum_{k=1}^{n}{V_k} = 0$$

## Circuit Diagram

We use diagrams as a schematic representation of a circuit. Lines are used to denote wires, or other connections. We also put the polarity of the power source so we can understand how the power is flowing.

![](../Attachments/Pasted%20image%2020230119010210.png)

We use circuit diagrams for both analog and digital electronic circuits. Each element is connected via their terminals. The "wire" is an abstract connection. There doesn't literally have to be a wire.

![](../Attachments/Pasted%20image%2020230119012142.png)