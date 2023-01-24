
The usage of transistors to the efficient control and conversion of [electric power](../Electricity.md) is the field of *Power Electronics*. The most important device in power electronics is the [Power MOSFET](Analog%20FET.md).

## DC to DC Converters

The simplest way to convert DC voltage down is with [Resistors](Resistors.md) in a simple voltage multiplier. However, this method is very inefficient, with all of the power being divided being lost as heat. The entire premise of efficient DC conversion is the idea of switching voltage sources on and off.

![](../../Attachments/Pasted%20image%2020230123221251.png)

The definition of DC voltage is the time average electric potential difference in a period. Thus if we had a source that was on 50% of the time, and off 50% of the time (almost like a [Clock Signal](../Digital/Clock%20Signal.md)) then we essentially halve the voltage at the output. This percentage of on vs off is called the *Duty Cycle*, and is crucial to DC conversion. In power electronics, we use a Power MOSFET as the switch for this purpose.

#### Buck Converter

![](../../Attachments/Pasted%20image%2020230123220247.png)

The most common use case is reducing the DC voltage without losses. This is done with a *Buck Converter*. The switch is directly after the voltage source and switches the voltage to the desired duty cycle. While the switch is CLOSED, current charges an [Inductor](Inductors.md) and the load, and when the switch is OPEN, the inductor expels current to keep the current from changing. This along with a bypass [Capacitor](Capacitors.md) keeps the DC voltage relatively stable.


#### Boost Converter

![](../../Attachments/Pasted%20image%2020230123221719.png)

A *Boost Converter* uses the opposite idea to get higher DC voltage. The inductor is placed prior to the switch. When the switch is OPEN, the source is charging the inductor, and the capacitor. When the switch is closed, current flows through the inductor, and then through the short circuit through the switch. This creates a massive current through the inductor, and charges its magnetic field immensely. When we then OPEN the switch again, the current goes back down, and the inductor will expel power to maintain the higher current. The voltage of the inductor and source are summed, created a larger voltage that then powers the load. This is how you boost the voltage at the expense of current.


#### Regulator

![](../../Attachments/Pasted%20image%2020230123221040.png)

We can add control circuitry, including digital electronics, to monitor the voltage going to the output and dynamically adjust the duty cycle. This is called *Pulse Width Modulation* and this method gives us very precise stable DC voltage. Above we see a PWM Buck Converter. 

## AC to DC Rectifiers

![](../../Attachments/Pasted%20image%2020230123223010.png)

We can use [Diodes](Diodes.md) to turn an AC current into DC. This is done by flipping the negative component of AC, and then adding a [Capacitor](Capacitors.md) at the end to smooth out the voltage.


## DC to AC Inverters

![](../../Attachments/Pasted%20image%2020230123223624.png)

We can use a series of switches to make DC current emulate AC sinewaves. This starts at the simplest [Square Wave](../Signals.md) to more complex modified sinewaves which try to model the sinewave better. The more closely you can match a true sinewave, the less harmonic distortion, which leads to heavy inefficiencies in inductive loads like motors.