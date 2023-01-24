
A *Diode* is a two terminal component that conducts [current](../Electricity.md) primary in one direction. It has low (ideally zero) [resistance](Resistors.md) in one direction, and high (ideally infinite) resistance in the other.

![](../../Attachments/Pasted%20image%2020230122174121.png)

The Diode operates in several different operating regions:

![](../../Attachments/Pasted%20image%2020230122174617.png)

1. **Breakdown**: At very large reverse bias, beyond the peak inverse voltage, a progress called reverse breakdown occurs are the diode will allow reverse voltage. This is denoted by $V_\text{br}$ above. This is actually desired in the Zener diode which is designed to reliably let the current flow backwards
2. **Reverse biased**: For a bias between breakdown and 0 volts, the reverse current is very small.
3. **Forward biased**: the current-voltage curve is exponential. when the voltage is lower than the *forward threshold voltage* or $V_d$ above, the current will not rise too significantly, so we generally bias the diode so that voltage will be at least this value when we are in the forward operating region.

## Zener, Schottky, and LEDs

![](../../Attachments/Pasted%20image%2020230122174223.png)

- **Zener Diode**: Special type of diode that is designed to reliably let current flow "backwards" when a certain set reverse voltage, known as the *Zener Voltage* is reached. This is opposed to the sudden, unexpected, and damaging breakdown voltage of a regular diode
- **Schottky Diode**: Special type of diode made with metal that has a lower forward voltage drop than regular diodes. They can be used as low-loss [rectifiers](Power%20Electronics.md), or voltage clamping. They also have higher switching speeds which makes them useful in [Radio Frequency](Radio%20Frequency.md) circuits
- **Light Emitting Diode**: A diode creates with special materials that causes it to emit light at various frequencies when forward biased. 