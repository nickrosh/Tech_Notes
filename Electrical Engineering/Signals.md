
A *signal* is electronics is simply a voltage that changes with time in a particular way. 

![](../Attachments/Pasted%20image%2020230120195416.png)

## Sinusoidal Signals

*Sine Waves* are the most common signals in electronics. [AC Current](Electricity.md) is a 60Hz sine wave. A great merit of sine waves are that they are the solutions to certain linear differential equations that happen to describe many phenomena in nature as well as the properties of linear circuits. A [linear circuit](Electric%20Circuit.md) driven by a sine wave always responds with a sine wave, although in general the phase and amplitude change. No other periodic signal can make this statement. Mathematically, the voltage of a sine wave can be described by
$$V = A\sin(2\pi f t)$$
Where 'A' is the amplitude and 'f' is the frequency in hertz. Sometimes it is important to know the of the signal at some arbitrary time t = 0, in which case you may see a *phase* $\phi$ in the expression:
$$V = A\sin(2\pi f t + \phi)$$
The other variation of this simple theme is the use of *angular frequency* $\omega$ instead of $2\pi f$ which would look like this:
$$V = A\sin(\omega t)$$

#### Decibels

In addition to amplitude, there are several other ways to characterize the magnitude of a sinewave or any other signal. You sometimes see it specified by *peak-to-peak amplitude* which is simply twice the amplitude. The other method is the *root-mean-squared amplitude* or RMS amplitude. RMS is what is usually used because it is also the number used to compute power in AC current. RMS voltage is simply :
$$V_\text{rms} = \frac{A}{\sqrt{2}}$$

 How do you compare the relative amplitudes of two signals? Because we often deal with ratios as large as a million, it is better to use a logarithmic measure, and for this we use the *decibel*. By definition, the ratio of two signals, in decibels (dB) is:
 $$dB = 10\log{\frac{P_2}{P_1}}$$
 P1 and P2 represent the *power* in the two signals. However, when dealing with amplitudes as we usually do, we can express the ratio of two signals having the same waveform as the following:
 $$dB = 20\log{\frac{A_2}{A_1}}$$
Although decibels are ordinarily used to specify the ratio of two signals, they are sometimes used as an absolute measure of amplitude. You assume some reference and compute the decibel ratio to that reference. It can be something like 0 dB V (1 V RMS) or 0 dB m (the voltage corresponding to 1 milliwatt into some assumed load impedance) or even the voltage noise produced by a [Resistor](Analog/Resistors.md) at room temperature.


## Square Waves

Square waves and square pulses are used extensively in digital electronics, where the high and low of a square wave correlate to a 1 and 0 in [Binary](Digital/Binary.md). A continuous square wave is used in synchronous digital circuits as a [Clock Signal](Digital/Clock%20Signal.md) to keep every component aligned