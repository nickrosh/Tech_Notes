
A *capacitor* is a device that stores [Electrical](../Electricity.md) energy in an electric field by virtue of collecting electric charges on two close surfaces insulated from each other. The ideal capacitor is characterized by a constant capacitance C in farads, defined as the ratio of the charge Q on each conductor to the voltage V between them.
$$C = \frac{Q}{V}$$

Thus in a DC [Electric Circuit](../Electric%20Circuit.md), the capacitor will only effect transients, or changes in voltage over time. Thus in a capacitor, the rate of *voltage change* is proportional to the *current* through it:
$$I = C\frac{dV}{dt}$$

However, with an AC circuit, you generally look at the response of the circuit in regards to the frequency. This is because the capacitor's [Impedance](Impedance.md) isn't set. It's dependent on the frequency:
$$Z = \frac{1}{j\omega C}$$

![](../../Attachments/Pasted%20image%2020230122024718.png)

## Bypassing and Coupling

To DC current, a capacitor looks like an open circuit, it will only allow AC through. This lets you couple a varying [Signal](../Signals.md) while blocking its average DC level. This is a *blocking* capacitor, also called a *Coupling* capacitor. 

![](../../Attachments/Pasted%20image%2020230122025314.png)

Likewise, because a capacitor looks like a short circuit at high frequencies, it suppresses (*bypasses*) signals where you don't want them. For example, on the DC voltages that power your circuits. Demographically, these two applications account for the vast majority of capacitors that are wired into the world's circuits.

![](../../Attachments/Pasted%20image%2020230122025711.png)


## Series and Parallel

Capacitors are actually the opposite of resistors in this regard. Series capacitors are calculated like parallel resistors and vice versa.

![](../../Attachments/Pasted%20image%2020230122030758.png)

## Differentiators and Highpass Filters

Combining a capacitor with a [Resistor](Resistors.md) can allow to differentiates an input. So we can make a circuit that can detect the rising or falling edge of a [Clock Signal](../Digital/Clock%20Signal.md). This is looking at a waveform in the time domain. If we look at the signal in the frequency domain, this exact circuit acts as a *Highpass Filter*:

![](../../Attachments/Pasted%20image%2020230122160102.png)

Only high frequencies will be allowed to pass. The cutoff frequency $f_c$ is determined by the values of the resistor and capacitor:
$$f_c = \frac{1}{2\pi RC}$$

## Integrators and Lowpass Filters

Conversely, we can also take the integral of a signal, by swapping the resistor and capacitor. Taken in the frequency domain, this becomes a lowpass filter, only allowing frequencies under a cutoff frequency same as the highpass filter:

![](../../Attachments/Pasted%20image%2020230122161257.png)

These RC filters are simple, but don't have a great roll-off, meaning the filtering after the chosen frequency is not that steep. We can make better filters with [Op-Amps](Op-Amps.md).