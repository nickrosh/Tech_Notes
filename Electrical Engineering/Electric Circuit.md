
An *Electric Circuit* is a closed loop network of electric components ([Resistors](Resistance.md), [Capacitors](Capacitance.md), [Inductors](Inductance.md), etc.). A circuit that contains active electronic components like transistors ([BJT](Analog/BJT.md), [CMOS](Digital/CMOS.md)) is known as an *Electronic Circuit*.

Electric circuits at low frequencies obey [Kirchoff's Laws](Kirchoff's%20Laws.md) which dictate how current and voltage acts in a system. Electric circuits with only linear components can be modeled with mesh and node analysis, stemming from Kirchoff's laws. These are called *Linear Circuits*. We can also model real world voltage and current sources with *Thevenin's* and *Norton's* theorems, respectively.

Circuits that contain non-linear components like transistors must be modelled with more advanced techniques. Chiefly among them is modelling the behavior of transistors, either through complex calculations, or through piecemeal calculations, treating the different zones (saturation vs triode) as different analyses.

## Circuit Diagram

We use diagrams as a schematic representation of a circuit. Lines are used to denote wires, or other connections. We also put the polarity of the power source so we can understand how the power is flowing for [DC circuits](Electricity.md).

![](../Attachments/Pasted%20image%2020230119010210.png)

We use circuit diagrams for both analog and digital electronic circuits. Each element is connected via their terminals. The "wire" is an abstract connection. There doesn't literally have to be a wire.

![](../Attachments/Pasted%20image%2020230119012142.png)