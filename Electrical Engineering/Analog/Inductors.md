
An *Inductor*, also called a coil, choke when blocking AC, or a reactor in Power Engineering, is a passive electrical component that stores energy in a magnetic field when [electric current runs through it](../Electricity.md). It is simply a coil of wire, usually wrapped around a magnetic core, utilizing the properties of electromagnetic induction to store energy. The rate of current change in an inductor is proportional to the applied across it (for [Capacitors](Capacitors.md) it is the other way around, the rate of *voltage change* is proportional to the *current* through it). The defining equation for an inductor is the following:
$$V = L\frac{dI}{dt}$$
Where L is the inductance measured in Henry's.

![](../../Attachments/Pasted%20image%2020230122165509.png)


## Series and Parallel

![](../../Attachments/Pasted%20image%2020230122171033.png)

Inductors are like [Resistors](Resistors.md), when they are series, sum their inductance together. When they are parallel, take the reciprocal of the sum their reciprocals.

## Resonant LC Circuit

Inductors, when paired with capacitors can create oscillating [Signals](../Signals.md) that are the foundations of [Radio Frequency](Radio%20Frequency.md).

![](../../Attachments/Pasted%20image%2020230122172244.png)

The frequency of the oscillations, also known as the *resonant frequency* is defined by the value of the capacitor and inductor:
$$f_0 = \frac{1}{2\pi \sqrt{LC}}$$
The energy stored oscillates between being stored in the capacitor and the inductor. When the inductor discharges, this charges the capacitor, and when the capacitor discharges it charges up the inductor. This back and forth can create an oscillating signal if the values of the components are chosen carefully.