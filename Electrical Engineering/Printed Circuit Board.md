*Printed Circuit Boards* or PCBs are a medium used to connect electrical components in a controlled manner. PCBs are used to combine discrete components like [resistors](Analog/Resistors.md) and [Capacitors](Analog/Capacitors.md) and [Integrated Circuits](Integrated%20Circuit.md), and the necessary power and I/O needed for the board.


![](../Attachments/Pasted%20image%2020230118020539.png)

CAD software called *Electronic Design Automation* (EDA) is used to design PCBs. The above left image is the cad rendering of the PCB, and the right image is the manufactured design. There are also two types of components. The classic *through-hole* components are the ones with long wire leads that can be put into breadboards. [Integrated Circuits](Integrated%20Circuit.md) can be through-hole packaged referred to as *Dual-Inline-Package* or DIP. Almost all modern PCBs now use *Surface Mount Technology* where components are much smaller and flat and are soldered to the board, like in the above right image. 

PCBs can have any number of layers, starting from the simplest single layer PCBs, where you only connect the copper traces, to more advanced designs. The number of layers and interconnection designed between them (Via's, Plated Through Holes) provide a general estimate of the board complexity. Using more layers allows for more routing options and better control of signal integrity, but are also time consuming and more costly to manufacture. 

One of the simplest boards to produce is the two-layer board, like the above picture. It has copper on both sides that are referred to as external layers. Multi layer boards sandwich additional internal layers of copper and insulation. You can create *Via's* that connect different layers to each other, or *Plated Through Holes* that connect every layer connected to the hole. [Radio Frequency](Analog/Radio%20Frequency.md) PCBs have tight requirements on preventing parasitic [capacitance](Analog/Capacitors.md) and are usually at least 4 layers to have dedicated *ground plane* and *power plane* layers. A power plane is the counterpoint to a ground plane and behaves as an AC [Signals](Signals.md) ground while providing [DC Power](Electricity.md) to the circuits mounted on the PCB. Advanced mobile [CPU](Digital/CPU.md) PCBs can have up to 12 layers, containing a huge density of traces buried inside the board itself.

Modern PCBs are designed with dedicated layout software, generally in the following steps:

1. Schematic capture of the [Electric Circuit](Electric%20Circuit.md) through an *Electronic Design Automation* (EDA) tool like Eagle or Easy EDA
2. Card dimensions and template are decided based on required circuitry and enclosure of the PCB.
3. The positions of the components and heat sinks are determined
4. Layer stack of the PCB is decided, with one to tens of layers depending on complexity. Ground and Power Planes are decided. Signal interconnections are traced on signal planes. Signal planes can be on the outer as well as inner layers. For optimal Electromagnetic Interference performance, high frequency signals are routed in internal layers between power or ground planes.
5. Line impedance is determined using dielectric layer thickness, routing copper thickness, and trace width. Trace separation is also taken into account in case of differential signals. 
6. Components are placed. Thermal considerations and geometry are taken into account. Via's and lands are marked
7. Signal traces are routed. EDA tools usually create clearances and connections in power and ground planes automatically.
8. Fabrication data consists of a set of Gerber files, a drill file, and a pick-and-place file.


## Tips

- Add in fiduciary holes for assembly, these are small holes in the corners to allow the pick-and-place machine to know how to assemble the board
- Keep high-frequency signal traces as short as possible. Signal degradation goes up as the traces get longer
- Place your components carefully. Placement will decide ultimate design success. You must understand the components, some should not be placed next to others. For example, head-sensitive electrolytic capacitors should be kept away from heat generating [Diodes](Analog/Diodes.md) and [Resistors](Analog/Resistors.md).
	1. Be mindful of components that have more pins as they will require more space for traces
	2. Keep components placed in the same orientation. Components usually have stand pin numbering to help with this.
	3. Take each components function and its relation to other components into account before placement
	4. If components are already procured, then you can print out the layout on paper true to size and see if the components fit.
- Use 45 degree trace angles. If you make 90 degree angles, it might be manufactured wrong and will probably make your design more difficult anyway.
- Place decoupling capacitors as near as possible to the power and ground pins of integrated circuits to maximize decoupling efficiency.
- Use a ground plane, if you can afford it. To give all your traces a single reference point for voltage and is especially important for analog circuits. Placing a ground plane directly under signal traces lowers the impedance of the traces, which is ideal.
- Route noisy ground traces away from signal traces to avoid crosstalk. The same goes for digital traces and anything else that's noisy.
- Leave space between traces, there is a margin of error in manufacturing and you don't want traces to short circuit
- Leave space between traces and mounting holes, you don't want an accidental connection and a shocking hazard.