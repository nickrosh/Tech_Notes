
FETs are primarily used as [CMOS](../Digital/CMOS.md) digital circuits, but they are also popular in the analog world, especially as they can be made with the same technology as digital circuits to make mixed-signal [Integrated Circuits](../Integrated%20Circuit.md). Analog FETs are larger and have much higher current and power limits compared to the digital versions. FETs are controlled by voltage applied to its gate, which allows current to pass between drain and source (and opposite for depletion type FETs).

![](../../Attachments/Pasted%20image%2020230123025837.png)

Unlike digital, we have two different types of FETs in analog. MOSFETs are good in switching applications and [Power Electronics](Power%20Electronics.md), while JFETs (Junction Field Effect Transistors) are more suited to linear amplifier circuits. Like [BJTs](BJT.md), there is a n-channel and p-channel version of the transistor.


## JFET Amplifier

Junction FETs have higher transconductance and gain compared to the MOSFET, as well as lower flicker noise. This make it used in low-noise, high input-impedance [Op-Amps](Op-Amps.md).

![](../../Attachments/Pasted%20image%2020230123030554.png)

Just like a BJT, we can use JFETs as an amplifier. In this above case, we bias the gate and put the AC signal to the gate. Then we observe the amplified output at the drain. FETS must be biased to their *saturation* region:

![](../../Attachments/Pasted%20image%2020230123031000.png)


## Power MOSFET

MOSFETs are used extensively in [Power Electronics](Power%20Electronics.md) as an analog switch for dc-dc conversions. They can handle massive peak currents of over 1000A and steady current of 200A. The keys to the success is extremely high input impedance, and its inherent thermal stability.

![](../../Attachments/Pasted%20image%2020230123032533.png)