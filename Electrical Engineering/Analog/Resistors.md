
The electrical *Resistance* of an object is a measure of its opposition to the flow of [electric current](../Electricity.md). Here is the [diagram symbol](../Electric%20Circuit.md) for a resistor. Resistors are the most common and ubiquitous passive electrical component. Resistors are used in amplifiers as loads for active devices, is bias networks, and as feedback elements. In combination with [Capacitors](Capacitors.md), they establish time constants and act as filters. They are used to set operating current and [Signal](../Signals.md) levels. Resistors are used in [Power Electronics](Power%20Electronics.md) to reduce voltages by dissipating power, to measure currents, and to discharge capacitors after power is removed. They are used in precision circuits to establish currents, to provide accurate voltage rations, and to set precise gain values. In logic circuits, they act as bus and line terminators and as "pullup" and "pulldown" resistors. In high-voltage circuits, they are used to measure voltages and to equalize leakage currents among [Diodes](Diodes.md) or capacitors connected in series. In [Radio Frequency](Radio%20Frequency.md) circuits, they set the bandwidth of resonant circuits, and they are even used as coil forms for [inductors](Inductors.md).

![](../../Attachments/Pasted%20image%2020230120174029.png)


## Ohm's Law

![](../../Attachments/Pasted%20image%2020230120174341.png)

Ohm's Law states that the voltage (V) across a resistor is proportional to the current (I) that passes through it. Resistance is measured in Ohms, and denoted by the symbol $\Omega$. Thus the behavior of an ideal resistor is modeled by:

$$V = IR$$


## Series and Parallel

![](../../Attachments/Pasted%20image%2020230120185358.png)

When resistors are in series, you simply sum their resistances, when they are in parallel, the total resistance is the reciprocal of the sum of reciprocals of the resistances. There are also a couple shortcuts instead of calculating the complex algebra:
- A large resistor in series with a small resistor has the resistance of the larger one, roughly. Also a large resistor in parallel with a small one has the resistance of the smaller one. You can "trim" the resistance by connecting a second resistor in series or parallel. To trim up, add a small resistor in series to increase R. To trim down, add a large resistor in parallel to decrease R. For example, to lower the value of a resistor by 1%, say, put a resistor 100 times as large as parallel.
- Suppose you want the resistance of a 5K in parallel with a 10K. If you think of the 5K as just two 10K's in parallel, then the whole circuit becomes three 10K's in parallel. Because the resistance of *n* equal resistors in parallel is 1/nth the individual resistors, the answer is 10/3 or 3.33K Ohms.

These types of quick calculations are actually more accurate than you might think. Remember that real resistors usually have a 5% margin of error anyway, so exact calculations to multiple decimal places aren't even that useful.

Also, in real life, almost every single component, power source, etc. has internal resistance. This is not usually modeled in ideal circuit analysis, but every [capacitor](Capacitors.md) and [inductor](Inductors.md) have internal resistance as well.