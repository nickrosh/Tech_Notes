
A *Clock Signal* is a logic signal which oscillates between a high and low state at a constant frequency and is used like a metronome to synchronize actions of digital circuits. In a synchronous logic circuit (like a [CPU](CPU.md)), the clock signal is applied to all [storage devices](Memory%20&%20Cache.md), [Flip Flops](Flip%20Flops.md), and latches, and causes them all to change state simultaneously, preventing race conditions.

A clock signal is usually produced by a crystal oscillator, which utilizes a piezoelectric crystal as a frequency-selective element. These crystals are also used to keep track of time in quartz watches. A simple Tank circuit can also be used ([capacitor and inductor](../Analog/Resistors.md)) to generate a stable clock signal.

![](../../Attachments/Pasted%20image%2020230117180254.png)

You can really use anything as a clock. In fact, many analog circuits use [sine waves](../Signals.md) due to the harmonics associated with square waves. But the most popular clock signal is a square wave with a 50% duty cycle.